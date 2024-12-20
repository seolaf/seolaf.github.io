---
title: ["Redis Cheat Sheet"]
date: 2024-12-20 09:00:00 +0900
categories: [cheatsheet, redis]
tags: [web, redis, cheatsheet]
---

## About Redis
Redis는 인메모리 데이터 구조 저장소로, 데이터베이스, 캐시, 메시지 브로커 등으로 사용된다. 아래는 Redis의 기본적인 데이터 조작 명령어들이다.

### 데이터 조작 명령어

|명령어|구문|설명|예시|
|---|---|---|---|
|GET|`GET key`|지정된 키의 값을 조회한다|`GET username`|
|MGET|`MGET key [key ...]`|여러 키의 값을 한 번에 조회한다|`MGET user1 user2`|
|SET|`SET key value`|키-값 쌍을 저장한다|`SET username "john"`|
|MSET|`MSET key value [key value ...]`|여러 키-값 쌍을 한 번에 저장한다|`MSET user1 "john" user2 "jane"`|
|DEL|`DEL key [key ...]`|하나 이상의 키를 삭제한다|`DEL username`|
|EXISTS|`EXISTS key [key ...]`|키의 존재 여부를 확인한다|`EXISTS username`|
|INCR|`INCR key`|키의 값을 1 증가시킨다|`INCR counter`|
|DECR|`DECR key`|키의 값을 1 감소시킨다|`DECR counter`|

### DBMS 관리 명령어

|명령어|구문|설명|
|---|---|---|
|INFO|`INFO [section]`|Redis 서버의 정보를 조회한다|
|CONFIG GET|`CONFIG GET parameter`|Redis 설정값을 조회한다|
|CONFIG SET|`CONFIG SET parameter value`|Redis 설정값을 변경한다|

### 예시 코드
```bash
$ redis-cli
127.0.0.1:6379> SET test 1234
OK
127.0.0.1:6379> GET test
"1234"
```

## NodeJS에서 Redis 사용하기

### Redis 모듈 설치와 기본 설정
NodeJS에서 Redis를 사용하기 위해서는 먼저 필요한 모듈을 설치해야 한다. 아래는 기본적인 설정 예시다.
```javascript
var express = require('express');
var app = express();
app.use(express.json());
app.use(express.urlencoded({ extended: false }));

const redis = require('redis');
const client = redis.createClient();

app.get('/init', function(req, res) {
    client.set(req.query.uid, 
        JSON.stringify({level: 'guest'}));
    res.send('ok');
});

var server = app.listen(3000, function(){
    console.log('app.listen');
});
```

### Redis Command 구조와 취약점
Redis 모듈의 Command는 두 가지 방식으로 처리된다.
1. 문자열 타입 
   ```javascript
   key, value, callback => Command(command, [key, value], callback)
   ```
2. 배열 타입 
   ```javascript
   [key, value] => Command(command, key, value)
   ```

### 취약점 익스플로잇

#### 배열 파라미터 조작
URL에서 배열 형태의 파라미터를 전달하면 의도하지 않은 값을 주입할 수 있다.  

```
http://localhost:3000/init?uid[]=test&uid[]={"level":"admin"}
```  
이렇게 전달된 파라미터는 내부적으로 다음과 같이 처리된다.
```javascript
Command("set", "test", "{'level':'admin'}")
```

#### 취약점이 발생하는 이유
1. 파라미터 검증 부재: req.query의 값을 직접 Redis command에 전달
2. 배열 타입 처리 방식: Array.isArray() 체크를 통과하면서도 악의적인 데이터 주입 가능
3. 자동 형변환: JavaScript의 타입 변환 특성을 이용한 우회


## Redis SSRF 취약점 분석

### Redis 서버 정보 누출
Redis는 기본적으로 인증 수단이 존재하지 않으며, "127.0.0.1"로 서비스를 바인딩한다. Redis 서버의 정보는 다음과 같이 확인할 수 있다.

```bash
$ echo -e 'info\r\n' | nc 127.0.0.1 6379
$2728
# Server
redis_version:4.0.9
redis_git_sha1:00000000
redis_git_dirty:0
redis_mode:standalone
os:Linux 5.0.0-27-generic_x86_64
arch_bits:64
multiplexing_api:epoll
gcc_version:7.4.0
process_id:13438
tcp_port:6379
...
```

### SSRF를 통한 Redis 명령어 주입
Redis는 허용되지 않은 명령어가 입력되더라도 응답이 공지되지 않고 다른 명령어를 실행한다. 이런 특성은 SSRF 공격에 취약하게 만든다.  

#### 허용되지 않은 명령어 테스트
```bash
$ echo -e "anydata: anydata\r\nget hello" | nc 127.0.0.1 6379
-ERR unknown command 'anydata:'
$5
world
```

#### HTTP Body를 통한 명령어 주입
대표적인 공격 기법으로는 HTTP 프로토콜을 이용한 방법이 있으며, HTTP Body 데이터에 실행할 명령어를 포함시켜 전송한다.  
```http
POST / HTTP/1.1
host: 127.0.0.1:6379
user-agent: Mozilla/5.0...
content-type: application/x-www-form-urlencoded
data=
SET key value
...
```
이러한 방식으로 Redis에 대한 SSRF 공격이 성공하면, 해당 서버에 저장된 데이터베이스의 정보를 탈취하거나 수정할 수 있다.  

## Django Redis Cache 취약점 분석

### 기본 설정 구조
Django에서 Redis를 캐시로 사용할 때는 다음과 같이 설정한다.  
```python
# settings.py
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/"
    }
}
```

### PickleSerializer 취약점

#### 기본 사용 예시

```python
from django.http import HttpResponse
from .models import Memo
from django.core.cache import cache

def p_set(request):
    cache.set('cache_memo', Memo('memo test!!'))
    return HttpResponse('set session')
```

#### Redis에 저장된 데이터 확인
```bash
$ redis-cli
127.0.0.1:6379> keys *
1) ":1:cache_memo"
127.0.0.1:6379> get ":1:cache_memo"
"\x80\x04\x95\x97\x00\x00\x00\x00\x00\x00\x00\x8c\x15django.db.models.base\x94\x8c\x0emodel_unpickle\x94\x93\x94\x8c\x02dt\x94\x8c\x04Memo\x94\x86\x94\x85\x94R\x94}\x94(\x8c\x06_state\x94h\x00\x8c\nModelState\x94\x93\x94)\x81\x94\x8c\x02id\x94\x8c\x0bmemo test!!\x94\x8c\x04data\x94\x8c\x00\x94\x8c\x0f_django_version\x94\x8c\x053.0.6\x94ub."
```

#### Serializer 코드 분석
django-redis-cache 모듈의 코드를 보면, get_serializer_class 함수에서 serializer_class를 가져올 때 options의 SERIALIZER_CLASS 값을 기본으로 하며,  redis_cache.serializers.PickleSerializer를 사용한다.  

```python
def get_serializer_class(self):
    serializer_class = self.options.get(
        'SERIALIZER_CLASS',
        'redis_cache.serializers.PickleSerializer'
    )
    return import_class(serializer_class)
```

#### PickleSerializer 구현
```python
class PickleSerializer(object):
    def __init__(self, pickle_version=-1):
        self.pickle_version = pickle_version
        
    def serialize(self, value):
        return pickle.dumps(value, self.pickle_version)
        
    def deserialize(self, value):
        return pickle.loads(force_bytes(value))
```

#### 공격 시나리오
1. 직렬화된 데이터 조작
   * Redis에 저장된 Pickle 데이터를 추출
   * 악의적인 코드를 포함한 Pickle 데이터 생성
   * Redis에 조작된 데이터 삽입

2. RCE(Remote Code Execution) 실행
   * Django 애플리케이션이 조작된 데이터를 역직렬화
   * 악의적인 코드가 서버에서 실행됨
   * 서버 시스템에 대한 접근 권한 획득


## Redis 서버 보안 설정 분석

### Redis 인증 체계
Redis는 기본적으로 인증 없이 접근이 가능한 DBMS다. 인증 설정은 `/etc/redis/redis.conf` 파일에서 관리한다.
```bash
$ cat /etc/redis/redis.conf
...
requirepass pass1234
```

### Redis 바인딩 설정
Redis 3.2.0 이전 버전은 기본적으로 "127.0.0.1"에만 바인딩된다. 바인딩 주소는 다음과 같이 설정한다.  

```bash
# 기본 설정
BIND 127.0.0.1

# IP 제한 설정
# BIND 127.0.0.1 # 주석 처리 시 모든 IP 접속을 허용합니다.
BIND 0.0.0.0

# 기능적으로 허용해야 하는 경우 권고 설정
# IP 지정을 통해 해당 IP만 허용하여 허용하는 IP에 대한 주지적인 확인이 필요합니다.
BIND 192.168.1.2 127.0.0.1
```


### 취약점 및 공격 시나리오

#### 인증 우회
1. AUTH 명령어가 없는 구 버전 사용
2. 약한 패스워드 설정
3. requirepass 설정 누락

#### 바인딩 취약점
1. BIND 0.0.0.0 설정으로 인한 전체 접근 허용
2. 불필요한 IP 대역 허용
3. 내부망 접근 통제 미흡

#### 권한 상승
1. Redis 6.0 이전 버전의 경우 다중 사용자(Multi-User) 지원 부재
2. Access Control List(ACL) 미설정
3. 관리자 권한으로 실행 시 시스템 명령어 실행 가능