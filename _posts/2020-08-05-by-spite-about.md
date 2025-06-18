---
layout: post
title:  웹 취약점: SQL Injection 제대로 알기
date:   2024-06-19
category: Web Security
image: assets/img/blog/image.jpg
author: 최진혁
tags:
  - 웹보안
  - SQL Injection
  - 해킹

---

SQL Injection(SQL 인젝션, 이하 SQLi)은 웹 개발자라면 반드시 알고 넘어가야 할 대표적인 웹 취약점입니다.  
이 취약점을 악용하면 해커가 데이터베이스의 민감한 정보까지 탈취할 수 있기 때문에, 실무와 공부 모두에서 매우 중요하게 다뤄집니다.



## SQL Injection이란?

SQLi는 웹 애플리케이션에서 사용자 입력값이 제대로 필터링되지 않을 때 발생하는 공격입니다.  
공격자는 입력창(로그인, 검색 등)에 **SQL 쿼리 구문을 삽입**하여 데이터베이스 명령을 조작할 수 있습니다.

예를 들어, 아래와 같은 코드가 있다고 가정합니다.

```python
username = request.GET['username']
password = request.GET['password']
query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'"

만약 username에

vbnet
복사
편집
admin' -- 
을 입력하면 쿼리는 아래처럼 바뀝니다.

sql
복사
편집
SELECT * FROM users WHERE username = 'admin' -- ' AND password = '1234'
-- 뒤는 주석 처리되어 비밀번호 조건이 무시되고,
비밀번호를 몰라도 관리자 로그인이 가능합니다.

실제 공격 시나리오
비밀번호 몰라도 관리자 계정 로그인

모든 사용자 정보 탈취

DB 테이블, 구조 탐색

데이터 수정, 삭제 등 다양한 변조 가능

방어 방법
Prepared Statement(프리페어드 쿼리, 바인딩)
SQL 구문과 사용자 입력값을 분리해서 처리하면 쿼리 조작이 불가능합니다.

입력값 검증 및 필터링
따옴표, 세미콜론, 논리 연산자 등 특수문자를 차단하거나 이스케이프 처리합니다.

최소 권한 원칙 적용
DB 계정에 꼭 필요한 권한만 부여해 피해 범위를 줄입니다.

SQL 인젝션은 "정확히 어떻게 입력값을 처리하는가"에 따라 생기는 문제입니다.
기본기를 잘 지키는 것이 최고의 보안입니다!

예시 코드 (파이썬 Flask)
python
복사
편집
# 취약한 코드 (절대 이렇게 작성 X)
username = request.form['username']
password = request.form['password']
query = f"SELECT * FROM users WHERE username='{username}' AND password='{password}'"

# 안전한 코드 (Prepared Statement 사용)
cur.execute("SELECT * FROM users WHERE username=? AND password=?", (username, password))
마무리
SQL Injection은 아주 기본적인 취약점이지만, 아직도 많은 웹사이트에서 발견되고 있습니다.
입력값 검증, Prepared Statement 등 기본 원칙만 지켜도 충분히 막을 수 있으니,
항상 신경 써서 개발하는 습관을 들입시다!