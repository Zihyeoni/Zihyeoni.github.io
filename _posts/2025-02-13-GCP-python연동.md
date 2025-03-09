---
title: "[GCP] Python & SQL 연동"
date: 2025-02-13 20:00:00 +09:00
categories: [GCP]
tags:
  [
    GCP,
    VM,
    가상머신,
    Python,
    SQL
  ]
---

# **Python & SQL 연동**

#### **환경 설정**
- Python 라이브러리: `mysql-connector-python`, `pandas`
- 데이터베이스: MariaDB
- 기능:
    1. MariaDB에 연결
    2. 테이블 생성
    3. `pandas` DataFrame 사용하여 데이터 삽입
    4. `pandas` 활용하여 데이터 조회 및 출력

Maria database를 설치하기 위한 Linux 명령  
```python
sudo apt update && sudo apt upgrade
sudo apt-get install mariadb-server
```

MariaDB(Mysql) 데이터베이스 생성 및 계정 생성  
```python
sudo mysql –u root –p
_________
CREATE DATABASE backend default CHARACTER SET UTF8;
show databases;
use backend;
GRANT ALL PRIVILEGES ON backend.* TO user@localhost IDENTIFIED BY '1234';

exit
mysql –u user –p
_________
```

**Python 설치**  
```sql
sudo apt-get update
sudo apt-get install python3-pip
```

**필요한 라이브러리 설치**  
```bash
pip install mysql-connector-python pandas
```

VM 인스턴스의 외부 IP 복사 후, FTP 호스트에 삽입  
![Image](https://github.com/user-attachments/assets/bfbc7368-2b0d-4a2c-90c8-fc7575a91b87)

main_0213.py (employees 테이블)  
```python
import mysql.connector
import pandas as pd

# 1. MariaDB 연결 설정
db_config = {
    "host": "localhost",      # MariaDB 서버 주소
    "user": "hj",           # 사용자 이름
    "password": "1234",   # 비밀번호
    "database": "backend"     # 사용할 데이터베이스
}

# MariaDB 연결
conn = mysql.connector.connect(**db_config)
cursor = conn.cursor()

# 2. 테이블 생성 (이미 존재하면 삭제 후 생성)
cursor.execute("DROP TABLE IF EXISTS employees;")
cursor.execute("""
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    department VARCHAR(50),
    salary DECIMAL(10,2)
);
""")

# 3. 샘플 데이터 생성 (pandas DataFrame 활용)
data = {
    "name": ["Alice", "Bob", "Charlie", "David", "Eve"],
    "age": [25, 30, 35, 28, 40],
    "department": ["HR", "IT", "Finance", "Marketing", "IT"],
    "salary": [50000, 70000, 80000, 60000, 90000]
}

df = pd.DataFrame(data)

# 4. 데이터 삽입
insert_query = "INSERT INTO employees (name, age, department, salary) VALUES (%s, %s, %s, %s)"
values = [tuple(row) for row in df.to_numpy()]

cursor.executemany(insert_query, values)
conn.commit()

print("데이터 삽입 완료")

# 5. 데이터 조회 및 pandas DataFrame으로 변환
cursor.execute("SELECT * FROM employees")
rows = cursor.fetchall()

# 컬럼명 가져오기
column_names = [desc[0] for desc in cursor.description]
df_result = pd.DataFrame(rows, columns=column_names)

# 6. 결과 출력 (pandas 기본 출력 방식 사용)
print("\n=== Employees Table Data ===")
print(df_result.to_string(index=False))  # 인덱스 없이 출력

# CSV로 저장 (필요한 경우)
df_result.to_csv("employees_data.csv", index=False, encoding="utf-8")
print("데이터를 'employees_data.csv' 파일로 저장하였습니다.")

# 연결 종료
cursor.close()
conn.close()
```

FTP 원격 사이트에 'main_0213.py' 넣기  
![Image](https://github.com/user-attachments/assets/32c8f8ef-9348-4af2-8af9-849474a9748b)

![Image](https://github.com/user-attachments/assets/c81a9e34-ddf1-46dc-8431-fa65841d7baa)

FTP에서 'employees_data.csv' 다운받기  
![Image](https://github.com/user-attachments/assets/f43019eb-6d65-45cd-8920-7c727e5f63d4)

MariaDB에서 확인  
![Image](https://github.com/user-attachments/assets/ebd20ac5-2d96-4b66-a1f8-d1a5a9b456a7)  
![Image](https://github.com/user-attachments/assets/84cf891d-2edd-4446-be4a-e0cc73049c5d)

---

‼️ 한 번 더 연습 ‼️

main2_0213.py (growth_marketing 테이블)  
```python
import mysql.connector
import pandas as pd

# 1. MariaDB 연결 설정
db_config = {
    "host": "localhost",      # MariaDB 서버 주소
    "user": "hj",             # 사용자 이름
    "password": "1234",       # 비밀번호
    "database": "backend"     # 사용할 데이터베이스
}

# MariaDB 연결
conn = mysql.connector.connect(**db_config)
cursor = conn.cursor()

# 2. 테이블 생성 (이미 존재하면 삭제 후 생성)
cursor.execute("DROP TABLE IF EXISTS growth_marketing;")
cursor.execute("""
CREATE TABLE growth_marketing (
    id INT AUTO_INCREMENT PRIMARY KEY,
    channel VARCHAR(50),
    budget INT,
    impressions INT,
    clicks INT,
    conversions INT,
    start_date DATE DEFAULT CURRENT_DATE
);
""")

# 3. 샘플 데이터 생성 (pandas DataFrame 활용)
data = {
    "channel": ["Google Ads", "Facebook Ads", "Instagram Ads"],
    "budget": [8000000, 7000000, 6500000],
    "impressions": [300000, 250000, 230000],
    "clicks": [20000, 18000, 17000],
    "conversions": [4000, 3500, 3400],
    "start_date": ["2024-06-01", "2024-07-01", "2024-08-01"]
}

df = pd.DataFrame(data)

# 4. 데이터 삽입
insert_query = "INSERT INTO growth_marketing (channel, budget, impressions, clicks, conversions, start_date) VALUES (%s, %s, %s, %s, %s, %s)"
values = [tuple(row) for row in df.to_numpy()]

cursor.executemany(insert_query, values)
conn.commit()

print("데이터 삽입 완료")

# 5. 데이터 조회 및 pandas DataFrame으로 변환
cursor.execute("SELECT * FROM growth_marketing")
rows = cursor.fetchall()

# 컬럼명 가져오기
column_names = [desc[0] for desc in cursor.description]
df_result = pd.DataFrame(rows, columns=column_names)

# 6. 결과 출력 (pandas 기본 출력 방식 사용)
print("\n=== Growth Marketing Table Data ===")
print(df_result.to_string(index=False))  # 인덱스 없이 출력

# CSV로 저장 (필요한 경우)
df_result.to_csv("growth_marketing_data.csv", index=False, encoding="utf-8")
print("데이터를 'growth_marketing_data.csv' 파일로 저장하였습니다.")

# 연결 종료
cursor.close()
conn.close()
```

FTP 원격 사이트에 'main2_0213.py' 넣기  
![Image](https://github.com/user-attachments/assets/5fa7351f-5aef-4095-8355-faaa0b2064f7)  
![Image](https://github.com/user-attachments/assets/f4f8b835-63bf-4f77-87ae-018630288ba0)

FTP에서 'growth_marketing.csv' 다운받기  
![Image](https://github.com/user-attachments/assets/d8cf3064-bd70-4c7e-a13d-0260cfa5dfa6)

MariaDB에서 확인  
![Image](https://github.com/user-attachments/assets/6645c415-d12c-463c-8583-1d52607e671b)  
![Image](https://github.com/user-attachments/assets/1a0454a5-d092-45b7-aa66-6dbd794a6367)