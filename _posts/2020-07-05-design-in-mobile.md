---
layout: post
title:  웹 취약점: CSRF(Cross-Site Request Forgery) 제대로 알기
date:   2024-06-19
category: Web Security
image: assets/img/blog/image.png
author: 최진혁
tags:
  - 웹보안
  - CSRF
  - 해킹
  

### 2. CSRF (Cross-Site Request Forgery)
CSRF(Cross-Site Request Forgery, 이하 CSRF)는 사용자가 인증된 세션을 이용해,  
의도하지 않은 요청을 공격자가 대신 보내도록 유도하는 웹 취약점입니다.  
사용자가 모르게 공격자 의도대로 데이터가 변경될 수 있기 때문에 매우 위험합니다.

---

## CSRF란?

CSRF는 사용자가 웹사이트에 로그인한 상태에서,  
다른 악성 사이트에서 요청을 보내 사용자의 권한으로 서버에 특정 작업을 하게 만드는 공격입니다.

예를 들어, 사용자가 은행 사이트에 로그인한 상태에서  
다른 사이트에서 아래와 같은 폼이 자동 제출된다면:

```html
<form action="https://bank.com/transfer" method="POST">
  <input type="hidden" name="to" value="hacker">
  <input type="hidden" name="amount" value="1000000">
</form>
<script>document.forms[0].submit()</script>
사용자의 의지와 상관없이 계좌이체가 실행될 수 있습니다.

실제 공격 시나리오
계정정보 변경, 비밀번호 초기화

포인트 전송, 금전 거래 등 요청

게시글, 댓글 도배 등 비정상적인 동작

방어 방법
CSRF 토큰 사용
요청 시마다 고유한 토큰을 포함, 서버에서 유효성 검사

SameSite 쿠키 속성 활용
크로스사이트 요청 시 쿠키 전송 제한

중요 요청은 반드시 재인증 처리(비밀번호 재확인 등)

CSRF는 "인증이 필요한 모든 POST 요청에 CSRF 토큰 적용"
사소해 보여도 실전에서는 꼭 대비해야 하는 취약점입니다.

예시 코드 (Flask-WTF)
python
복사
편집
# CSRF 토큰이 포함된 폼 예시
<form method="POST">
    {{ form.hidden_tag() }}
    ...
</form>
마무리
CSRF는 사용자가 로그인한 상태에서 방심할 때 언제든 노릴 수 있는 취약점입니다.
CSRF 토큰 적용, SameSite 속성 등 기본 방어법을 꼭 기억합시다!