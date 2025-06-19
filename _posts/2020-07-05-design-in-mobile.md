---
layout: post
title:  CSRF 취약
date:   2025-06-19
category: security
image: "assets/img/blog/blog11.jpg"
author: 최진혁
tags: csrf, web-security
---

### CSRF란 무엇일까요?

* **정의:** CSRF(Cross-Site Request Forgery)는 사용자가 인증된 세션을 가진 상태에서, 공격자가 만든 요청을 대신 보내어 권한 있는 작업을 수행하도록 유도하는 취약점입니다.
* **영향:**  
    * **무단 전송:** 은행 이체, 포인트 전송 등 금전적 피해.  
    * **설정 변경:** 비밀번호 변경, 이메일 변경 등 계정 탈취 우회.  
    * **서비스 남용:** 게시글, 댓글 자동 생성으로 서비스 파탄.

---

## 2. 공격 예시

```html
<!-- 공격자 사이트에서 자동 제출 폼 -->
<form action="https://victim.com/transfer" method="POST">
  <input type="hidden" name="to" value="hacker">
  <input type="hidden" name="amount" value="1000000">
</form>
<script>document.forms[0].submit()</script>
```

로그인된 사용자가 해당 페이지를 방문하면, 사용자의 권한으로 이체 요청이 실행됩니다.

---

## 3. 실제 공격 시나리오

- 포인트 전송, 금전 이체  
- 비밀번호 및 이메일 변경  
- 게시글/댓글 대량 자동 생성  
- 계정 설정 무단 변경

---

## 4. 방어 방법

1. **CSRF 토큰 사용**  
   요청 시 랜덤 토큰을 포함, 서버에서 검증.
2. **SameSite 쿠키 속성**  
   `Strict` 또는 `Lax` 설정으로 크로스사이트 요청 시 쿠키 전송 제한.
3. **재인증 요구**  
   중요한 작업 전 비밀번호 재확인 등 추가 인증.

---

## 5. 예시 코드 (Flask-WTF)

```python
# Flask-WTF 폼 예시
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField

class TransferForm(FlaskForm):
    to = StringField('받는 사람')
    amount = StringField('금액')
    submit = SubmitField('이체')
```

```html
<!-- 템플릿 -->
<form method="POST">
    {{ form.hidden_tag() }}
    {{ form.to.label }} {{ form.to() }}
    {{ form.amount.label }} {{ form.amount() }}
    {{ form.submit() }}
</form>
```

---

CSRF는 사용자가 로그인한 상태에서 언제든 발생할 수 있는 위험입니다.  
CSRF 토큰, SameSite 쿠키, 재인증 등 기본 방어책을 반드시 적용하세요!
