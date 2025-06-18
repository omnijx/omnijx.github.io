---
layout: post
title:  SQL Injection 취약점
date:   2025-06-19
category: security
image: assets/img/blog/image.png
author: 최진혁
tags: sql-injection, web-security
---

### SQL Injection이란 무엇일까요?

* **정의:** SQL Injection(SQLi)은 웹 애플리케이션이 사용자 입력값을 제대로 검증하지 않고 SQL 쿼리에 포함할 때, 공격자가 임의의 SQL 코드를 삽입하여 데이터베이스를 조작하는 취약점입니다.
* **영향:**  
    * **인증 우회:** `admin' --` 같은 페이로드로 비밀번호 검증을 우회해 관리자 로그인 가능.  
    * **데이터 탈취:** 모든 사용자 정보나 민감한 데이터를 조회, 다운로드 가능.  
    * **데이터 변조 및 삭제:** 테이블 구조 변경, 데이터 삽입/수정/삭제 가능.

---

## 2. 공격 예시

```python
# 취약한 코드 예시 (Flask)
username = request.args.get('username')
password = request.args.get('password')
query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'"
cur.execute(query)
```

만약 `username=admin' --` 을 입력하면:

```sql
SELECT * FROM users WHERE username = 'admin' -- ' AND password = 'password'
```
`--` 뒤는 주석 처리되어 비밀번호 검증이 무시되고, 공격자는 관리자 권한을 획득합니다.

---

## 3. 실제 공격 시나리오

- 비밀번호를 모른 채 관리자 계정 로그인  
- 모든 사용자 정보 탈취  
- 데이터베이스 구조 탐색 및 테이블 목록 조회  
- 데이터 수정, 삭제로 서비스 장애 유발

---

## 4. 방어 방법

1. **Prepared Statement 사용**  
   SQL 구문과 사용자 입력을 분리하여 바인딩 처리 (`?`, `:param` 등).
2. **입력값 검증 및 이스케이프**  
   특수문자(따옴표, 세미콜론 등) 차단 혹은 이스케이프 처리.
3. **최소 권한 원칙**  
   DB 계정에 꼭 필요한 권한만 부여해 피해 범위를 최소화.
4. **ORM 사용**  
   SQL 문자열을 직접 작성하는 대신 검증된 ORM(Object-Relational Mapping) 활용.

---

## 5. 예시 코드 (Flask + SQLite)

```python
# 안전한 Prepared Statement 사용 예시
username = request.form['username']
password = request.form['password']
cur.execute(
    "SELECT * FROM users WHERE username = ? AND password = ?",
    (username, password)
)
```

---

SQL Injection은 웹 보안의 기본이자 가장 치명적인 취약점 중 하나입니다.  
기본 원칙만 잘 지켜도 충분히 방어할 수 있으니, 항상 Prepared Statement와 입력값 검증을 습관화합시다!
