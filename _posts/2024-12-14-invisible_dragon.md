---
title: ["[Webhacking.kr] invisible_dragon (sqli - 300)"]
date: 2024-12-14 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, sqli]
---

```php
<?php
include "./config.php";

if (preg_match('/session/isUD', $_SERVER['QUERY_STRING'])) {
    exit('not allowed');
}

//parse_str($_SERVER['QUERY_STRING']);
parse_str($_SERVER['QUERY_STRING'], $arr);
foreach ($arr as $key => $value) {
    $$key = $value;
}

$db = dbconnect();
if (isset($column) && isset($keyword)) {
    if (isset($ie) && !empty($ie)) {
        if (in_array($ie, ['utf8', 'ucs2', 'utf16'])) {
            $_SESSION['format'] = "convert(%s using $ie)";
        } else {
            $_SESSION['format'] = 'convert(%s using latin1)';
        }
    }
    $format = isset($_SESSION['format']) ? $_SESSION['format'] : 'convert(%s using latin1)';
    if (
        preg_match('/^convert\(/', $format) &&
        preg_match('/\)$/', $format)
    ) {
        $column = sprintf($format, $column);
        $column = preg_replace('/[^\w\s(),]/', '', $column);
        if (strlen($column) > 24) {
            exit("no hack!");
        }

        $keyword = addslashes($keyword);
        $query = "select * from prob_invisible_dragon where $column='$keyword'";
        echo "<hr>query : <strong>{$query}</strong><hr><br>";
        $result = @mysqli_fetch_array(mysqli_query($db, $query));
        if ($result) {
            echo "<h2>Search {$result[0]}!</h2>";
        }
    }
}

if (isset($secret)) {
    $query = "select secret from prob_invisible_dragon";
    $result = @mysqli_fetch_array(mysqli_query($db, $query));
    if ($result['secret'] === $secret) {
        echo "<h2>Yes, that is the FLAG what you want ;)</h2>";
    }
}

highlight_file(__FILE__);
?>
```

* URL의 쿼리 스트링에 `session`이라는 문자열이 포함되어 있으면 실행을 중단
  * `i` flag : 대소문자를 구분하지 않고 매칭
  * `s` flag : 개행문자를 포함하여 매칭
  * `U` flag : ungreedy 혹은 lazy 매칭으로 변경
  * `D` flag : `$`문자가 문자열의 절대적인 끝만을 매칭
* URL 쿼리 스트링을 파싱하여 `arr` 배열로 변환
* `column`과 `keyword` 파라미터가 설정되었는지 확인
* `ie` 파라미터가 존재하고 비어있지 않으면, 문자셋 변환 포맷을 설정함. `utf8`, `ucs2`, `utf16`만 허용되며, `ie` 파라미터가 없거나 그 외의 값인 경우우는 `latin1`을 사용
* `format`이 `convert(`로 시작하고 `)`로 끝나는지 확인
* `column`값을 지정된 `format`으로 포맷팅
* 알파벳, 숫자, 언더스코어, 공백, 괄호, 콤마를 제외한 모든 문자를 제거
* `column`의 길이가 24자를 초과하면 실행을 중단
* `keyword`에 특수문자가 있다면 이스케이프 처리하고, SQL 쿼리를 구성


```
http://webhacking.kr:10016?column=username&keyword=admin&ie=utf8
```  
예컨대 다음과 같은 URL로 요청을 했다고 가정하면, 아래와 같은 SQL 쿼리가 생성된다.  
```sql
select * from prob_invisible_dragon where convert(username using utf8)='admin'
```  
