---
layout: post
title:  웹 취약점: XSS(Cross-Site Scripting) 제대로 알기
date:   2024-06-19
category: Web Security
image: "assets/img/blog11.jpg"
author: 최진혁
tags:
  - 웹보안
  - XSS
  - 해킹
---

XSS(Cross-Site Scripting, 이하 XSS)는 사용자의 입력값이 웹 페이지에 그대로 출력될 때 발생하는 대표적인 웹 취약점입니다.  
이 취약점을 통해 공격자는 악성 스크립트를 삽입하고, 사용자의 쿠키 탈취, 세션 하이재킹 등 다양한 공격을 시도할 수 있습니다.

---

## XSS란?

XSS는 공격자가 웹사이트에 악성 JavaScript 코드를 주입해  
다른 사용자의 브라우저에서 해당 스크립트가 실행되도록 하는 공격입니다.

예를 들어, 아래와 같이 사용자의 입력값을 필터링 없이 출력하는 코드가 있다고 가정해봅시다.

```python
@app.route('/search')
def search():
    query = request.args.get('q')
    return f"검색 결과: {query}"
여기서 입력값에

<script>alert('XSS!')</script>
를 넣으면, 해당 스크립트가 그대로 실행됩니다.
이로 인해 사용자 쿠키 탈취, 피싱 등 다양한 피해가 발생할 수 있습니다.

실제 공격 시나리오
쿠키/세션 탈취: document.cookie를 통해 인증정보를 공격자 서버로 전송

웹사이트 변조: DOM 조작, 가짜 입력창 생성 등

사용자 권한 탈취 및 피싱

방어 방법
입력값 출력 시 HTML 이스케이프 처리
(<, >, ", ', & 등 특수문자를 변환)

HTTPOnly 쿠키 사용
JavaScript로 접근 불가능하게 쿠키 설정

Content Security Policy(CSP) 적용
외부 스크립트, 인라인 스크립트 제한

XSS는 "출력 시 반드시 이스케이프!"
보안의 기본은 사용자 입력값을 신뢰하지 않는 것입니다.

예시 코드 (Flask Jinja2)
python
복사
편집
# 취약한 코드
return f"검색 결과: {query}"

# 안전한 코드 (자동 이스케이프 적용)
return render_template('search.html', query=query)
마무리
XSS는 사용자 데이터를 그대로 출력하는 순간 발생할 수 있습니다.
단순해 보이지만, 실제 공격에 사용되면 피해가 매우 커질 수 있으니 항상 주의해야 합니다!
출력 이스케이프, HTTPOnly, CSP 등 기본 원칙을 꼭 지킵시다.