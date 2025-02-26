---
title: "[GCP] MariaDB"
date: 2025-02-11 19:30:00 +09:00
categories: [GM, GCP]
tags:
  [
    GCP,
    VM,
    가상머신,
    MariaDB,
    SQL
  ]
---

# **⚙️VM에서 MariaDB 실행**

VM Ubuntu에서 MariaDB를 실행시켜 볼 것이다.

#### **MariaDB** 란?
MySQL에서 파생된 오픈 소스 관계형 데이터베이스 관리 시스템 (RDBMS)  
MySQL과의 높은 호환성을 유지하며 성능 개선, 보안 강화, 새로운 스토리지 엔진 등의 기능 제공

먼저, 이미 만들어 둔 VM의 SSH에 접속해 MariaDB를 설치한다.  
```
sudo apt update
sudo apt install mariadb-server -y
```  
![Image](https://github.com/user-attachments/assets/37077705-5b8f-4733-aa48-e6f4b5b7519f)

설치 후 자동 실행을 설정한다.
```
sudo systemctl start mariadb
sudo systemctl enable mariadb
```

다음은 보안 설정인데, 최초 실행 시에는 설정하지 않아도 된다.
```
sudo mysql_secure_installation
```

- root 비밀번호 설정
- 익명 사용자 제거
- 원격 root 접속 비활성화
- 기본 테스트 데이터베이스 삭제

이제 MariaDB root 계정으로 접속한다.
```
sudo mysql -u root
```

- "sudo"를 사용하면 비밀번호 입력 없이 root 계정으로 접속
- "mysql -u root -p" 명령 사용 시에는 비밀번호 입력 필요

---

## **데이터베이스 생성 및 삭제**

데이터베이스 생성 !  
![Image](https://github.com/user-attachments/assets/51e412de-eb48-4732-9efb-dfca555088d5)

데이터베이스를 삭제하려면 :
```sql
-- 데이터베이스 삭제
DROP DATABASE mydb;
```

---

## **사용자 계정 관리**

**1) 사용자 계정 생성 및 비밀번호 설정**
```sql
-- 사용자 계정 생성 및 비밀번호 설정
CREATE USER 'user1'@'localhost' IDENTIFIED BY '1234';
```
- 'user1' 사용자는 로컬 서버(localhost)에서만 접속 가능
![Image](https://github.com/user-attachments/assets/f5afdab6-2758-4a59-84ed-e986db7f923d)

**2) 사용자 삭제**
```sql
-- 사용자 삭제
DROP USER 'user1'@'localhost';
```
![Image](https://github.com/user-attachments/assets/d2fcc036-c071-4fce-91b6-ca402ed79034)

**3) 비밀번호 변경**

- **root 계정에서 특정 사용자의 비밀번호 변경**
```sql
-- 특정 사용자의 비밀번호 변경
ALTER USER 'user1'@'localhost' IDENTIFIED BY 'newpassword123';
```

- **현재 로그인한 사용자가 자신의 비밀번호 변경**
```sql
-- 로그인한 사용자가 직접 비밀번호 변경
SET PASSWORD = PASSWORD('newpassword123');
```

**4) 사용자 권한 관리**

- **특정 데이터베이스에 대한 모든 권한 부여**
```sql
-- 모든 권한 부여
GRANT ALL PRIVILEGES ON mydb.* TO 'user1'@'localhost';
```

- **특정 권한만 부여 (예: 읽기 및 쓰기 권한)**
```sql
-- 특정 권한 부여
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'user1'@'localhost';
```

- **특정 권한 회수**
```sql
-- 특정 권한 제거
REVOKE INSERT, UPDATE ON mydb.* FROM 'user1'@'localhost';
```

- **권한 변경 사항 적용 (필수 실행)**
```sql
FLUSH PRIVILEGES;
```

**5) 사용자 계정 목록 조회**
```sql
-- 등록된 사용자 계정 목록 확인
SELECT User, Host FROM mysql.user;
```

⬇️ 명령어 정리

| 명령어 | 설명 |
| --- | --- |
| `sudo mysql -u root` | Ubuntu에서 root 계정으로 MariaDB 접속 |
| `CREATE DATABASE mydb;` | 데이터베이스 생성 |
| `DROP DATABASE mydb;` | 데이터베이스 삭제 |
| `CREATE USER 'user1'@'localhost' IDENTIFIED BY 'password123';` | 사용자 계정 생성 |
| `DROP USER 'user1'@'localhost';` | 사용자 계정 삭제 |
| `ALTER USER 'user1'@'localhost' IDENTIFIED BY 'newpassword123';` | 특정 사용자의 비밀번호 변경 |
| `SET PASSWORD = PASSWORD('newpassword123');` | 로그인한 사용자의 비밀번호 변경 |
| `GRANT ALL PRIVILEGES ON mydb.* TO 'user1'@'localhost';` | 특정 데이터베이스의 모든 권한 부여 |
| `GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'user1'@'localhost';` | 특정 권한만 부여 |
| `REVOKE INSERT, UPDATE ON mydb.* FROM 'user1'@'localhost';` | 특정 권한 회수 |
| `FLUSH PRIVILEGES;` | 권한 변경 사항 즉시 적용 |
| `SELECT User, Host FROM mysql.user;` | 사용자 목록 조회 |