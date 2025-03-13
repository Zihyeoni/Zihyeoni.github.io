---
title: "[SQL] JOIN & UNION 활용한 그로스 마케팅"
date: 2025-02-12 23:00:00 +09:00
categories: [GM, Scenario | Analysis]
tags:
  [
    GM,
    그로스마케팅,
    GCP,
    VM,
    가상머신,
    SQL,
    JOIN,
    UNION
  ]
---

# **📑JOIN & UNION 활용한 그로스 마케팅**

### **JOIN & UNION**

**JOIN**: 두 개 이상의 테이블을 기준이 되는 공통 필드를 통해 결합하여 새로운 데이터를 생성하는 방식
- 주로 고객 정보, 마케팅 캠페인, 거래 데이터 등을 통합하여 보다 정밀한 분석을 수행할 때 사용

**UNION**: 여러 테이블에서 가져온 데이터를 동일한 컬럼 구조를 가진 경우 하나의 결과 집합을 합치는 방식
- 여러 마케팅 채널(이메일, SNS, 광고 등)의 성과 데이터를 하나로 합칠 때 유용

| 항목 | UNION | JOIN |
| --- | --- | --- |
| 데이터 결합 방식 | 여러 테이블의 데이터를 **위아래**로 합침 | 공통 키를 기준으로 **좌우**로 결합 |
| 컬럼 개수 | 결합하려는 테이블의 컬럼 개수와 데이터 타입이 동일해야 함 | 테이블 간의 공통 키를 사용하여 여러 개의 컬럼을 조합 |
| 중복 데이터 | 기본적으로 제거됨 (`UNION ALL` 사용 시 제거 안 됨) | 중복 데이터가 있을 경우 그대로 유지됨 |

---

## **JOIN 활용한 그로스 마케팅**

#### <span style="color:orange">**고객 정보와 구매 데이터를 JOIN하여 VIP 고객 분석**</span>

데이블 생성  
```sql
-- 고객 테이블
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

-- 구매 테이블
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    amount DECIMAL(10,2),
    purchase_date DATE
);
```

샘플 데이터 삽입  
```sql
-- 고객 데이터 삽입
INSERT INTO customers VALUES
(1, '김영희', 'younghee@email.com'),
(2, '이철수', 'cheolsu@email.com'),
(3, '박지수', 'jisu@email.com'),
(4, '최민수', 'minsu@email.com'),
(5, '한지혜', 'jihye@email.com');

-- 구매 데이터 삽입
INSERT INTO purchases VALUES
(101, 1, 50000, '2024-02-01'),
(102, 1, 60000, '2024-02-02'), -- 김영희 총 110,000
(103, 2, 40000, '2024-02-03'),
(104, 2, 30000, '2024-02-04'), -- 이철수 총 70,000
(105, 3, 150000, '2024-02-05'), -- 박지수 총 150,000 (VIP)
(106, 4, 90000, '2024-02-06'),
(107, 4, 50000, '2024-02-07'), -- 최민수 총 140,000 (VIP)
(108, 5, 25000, '2024-02-08');
```  
![Image](https://github.com/user-attachments/assets/b9013771-a68a-4f49-9908-f80d95df4145)

10만원 이상 구매한 고객(VIP) 식별  
```sql
SELECT c.customer_id, c.name, c.email, p.total_spent
FROM customers c
JOIN (
    SELECT customer_id, SUM(amount) AS total_spent
    FROM purchases
    GROUP BY customer_id
) p ON c.customer_id = p.customer_id
WHERE p.total_spent > 100000;
```  
![Image](https://github.com/user-attachments/assets/dd125f61-5682-4c95-8dba-28a27923ca81)

#### <span style="color:orange">**이메일 캠페인 반응과 구매 데이터 연결**</span>

```sql
-- 1) 고객 테이블
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

-- 2) 이메일 캠페인 반응 테이블
CREATE TABLE email_campaign (
    campaign_id INT PRIMARY KEY,
    customer_id INT,
    campaign_name VARCHAR(100),
    clicks INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- 3) 구매 테이블
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2),
    purchase_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

```sql
-- 1) 고객 데이터 삽입
INSERT INTO customers VALUES
(1, '김영희', 'younghee@email.com'),
(2, '이철수', 'cheolsu@email.com'),
(3, '박지수', 'jisu@email.com'),
(4, '최민수', 'minsu@email.com'),
(5, '한지혜', 'jihye@email.com');

-- 2) 이메일 캠페인 데이터 삽입
INSERT INTO email_campaign VALUES
(101, 1, '겨울 할인 이벤트', 8),  -- 클릭 수 8 (조건 충족)
(102, 2, '신규 회원 웰컴 이벤트', 5), -- 클릭 수 5 (조건 미충족)
(103, 3, '특가 프로모션', 12), -- 클릭 수 12 (조건 충족)
(104, 4, '봄맞이 할인', 3), -- 클릭 수 3 (조건 미충족)
(105, 5, 'VIP 고객 혜택', 10); -- 클릭 수 10 (조건 충족)

-- 3) 구매 데이터 삽입
INSERT INTO purchases VALUES
(201, 1, 50000, '2024-02-01'),
(202, 3, 120000, '2024-02-02'),
(203, 5, 80000, '2024-02-03');
```  
![Image](https://github.com/user-attachments/assets/ffb6c445-8d36-4713-b4d1-6886320aa8b6)

특정 이메일 캠페인에서 10번 이상 클릭한 고객의 구매 데이터 결합하여 캠페인 성과 분석  
```sql
SELECT c.email, e.campaign_name, e.clicks, p.purchase_amount
FROM email_campaign e
JOIN customers c ON e.customer_id = c.customer_id
LEFT JOIN purchases p ON c.customer_id = p.customer_id
WHERE e.clicks >= 10;
```  
![Image](https://github.com/user-attachments/assets/b4c03f23-5fe6-4783-a820-582e966ceb69)

#### <span style="color:orange">**유입 채널별 전환율 분석**</span>

```sql
-- 1) 방문자 트래킹 테이블 (유입 채널 데이터)
CREATE TABLE user_tracking (
    tracking_id INT PRIMARY KEY,
    user_id INT,
    channel VARCHAR(50),
    visit_date DATE
);

-- 2) 구매 테이블 (고객 구매 데이터)
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2),
    purchase_date DATE
);
```

```sql
-- 1) 방문자 데이터 삽입 (유입 채널별 방문자 ID 포함)
INSERT INTO user_tracking VALUES
(1, 101, 'Google Ads', '2024-02-01'),
(2, 102, 'Google Ads', '2024-02-01'),
(3, 103, 'Facebook Ads', '2024-02-02'),
(4, 104, 'Facebook Ads', '2024-02-02'),
(5, 105, 'Instagram Ads', '2024-02-03'),
(6, 106, 'Instagram Ads', '2024-02-03'),
(7, 107, 'Instagram Ads', '2024-02-03'),
(8, 108, 'YouTube Ads', '2024-02-04'),
(9, 109, 'YouTube Ads', '2024-02-04'),
(10, 110, 'YouTube Ads', '2024-02-04'),
(11, 111, 'YouTube Ads', '2024-02-04');

-- 2) 구매 데이터 삽입 (일부 방문자가 실제로 구매한 데이터)
INSERT INTO purchases VALUES
(201, 101, 50000, '2024-02-02'), -- Google Ads 방문자 1명 구매
(202, 103, 75000, '2024-02-03'), -- Facebook Ads 방문자 1명 구매
(203, 105, 120000, '2024-02-04'), -- Instagram Ads 방문자 1명 구매
(204, 110, 90000, '2024-02-05'); -- YouTube Ads 방문자 1명 구매
```  
![Image](https://github.com/user-attachments/assets/020fcd1d-fe27-4075-9aee-d85c1b1b0396)

각 유입 채널의 전환율 분석  
```sql
SELECT u.channel, COUNT(DISTINCT u.user_id) AS visitors, COUNT(DISTINCT p.customer_id) AS buyers,
       (COUNT(DISTINCT p.customer_id) * 100.0 / COUNT(DISTINCT u.user_id)) AS conversion_rate
FROM user_tracking u
LEFT JOIN purchases p ON u.user_id = p.customer_id
GROUP BY u.channel;
```  
![Image](https://github.com/user-attachments/assets/01948481-cf3f-481a-bb79-a5d26d433657)

#### <span style="color:orange">**장바구니 이탈 고객 찾기**</span>

```sql
-- 1) 고객 테이블
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

-- 2) 장바구니 아이템 테이블 (고객이 담은 상품 목록)
CREATE TABLE cart_items (
    cart_id INT PRIMARY KEY,
    customer_id INT,
    product_name VARCHAR(100),
    price DECIMAL(10,2),
    quantity INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- 3) 구매 테이블 (구매한 고객 정보)
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2),
    purchase_date DATE,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

```sql
-- 1) 고객 데이터 삽입
INSERT INTO customers VALUES
(1, '김영희', 'younghee@email.com'),
(2, '이철수', 'cheolsu@email.com'),
(3, '박지수', 'jisu@email.com'),
(4, '최민수', 'minsu@email.com'),
(5, '한지혜', 'jihye@email.com');

-- 2) 장바구니 데이터 삽입 (상품을 담은 고객 정보)
INSERT INTO cart_items VALUES
(101, 1, '노트북', 1200000, 1),
(102, 2, '스마트폰', 800000, 1),
(103, 3, '태블릿', 600000, 1),
(104, 4, '무선 이어폰', 200000, 2),
(105, 5, '스마트워치', 350000, 1);

-- 3) 구매 데이터 삽입 (일부 고객만 구매)
INSERT INTO purchases VALUES
(201, 1, 1200000, '2024-02-02'), -- 김영희는 구매 완료
(202, 3, 600000, '2024-02-04'); -- 박지수는 구매 완료
```  
![Image](https://github.com/user-attachments/assets/8424114b-fb23-4a68-94c8-528c51b63ce0)

장바구니에 상품을 담았지만 구매하지 않은 고객을 식별하여 리마케팅 대상 선정  
```sql
SELECT c.customer_id, c.name, c.email, b.cart_total
FROM customers c
JOIN (
    SELECT customer_id, SUM(price * quantity) AS cart_total
    FROM cart_items
    GROUP BY customer_id
) b ON c.customer_id = b.customer_id
LEFT JOIN purchases p ON c.customer_id = p.customer_id
WHERE p.customer_id IS NULL;
```  
![Image](https://github.com/user-attachments/assets/9935e961-53b1-4fb2-8f9a-4c3dcb7f1917)

#### <span style="color:orange">**마케팅 비용 대비 매출 분석**</span>

```sql
-- 1) 마케팅 비용 테이블 (광고 캠페인별 지출 데이터)
CREATE TABLE marketing_spend (
    campaign_id INT PRIMARY KEY,
    campaign_name VARCHAR(100),
    ad_spend DECIMAL(10,2) -- 마케팅 비용
);

-- 2) 구매 테이블 (구매 내역 데이터)
CREATE TABLE purchases (
    purchase_id INT PRIMARY KEY,
    campaign_id INT,
    amount DECIMAL(10,2), -- 구매 금액
    purchase_date DATE,
    FOREIGN KEY (campaign_id) REFERENCES marketing_spend(campaign_id)
);
```

```sql
-- 1) 마케팅 비용 데이터 삽입
INSERT INTO marketing_spend VALUES
(1, 'Google Ads Campaign', 1000000),
(2, 'Facebook Ads Campaign', 800000),
(3, 'Instagram Ads Campaign', 600000),
(4, 'YouTube Ads Campaign', 1200000),
(5, 'TikTok Ads Campaign', 700000);

-- 2) 구매 데이터 삽입 (각 캠페인에서 발생한 매출 데이터)
INSERT INTO purchases VALUES
(101, 1, 300000, '2024-02-01'),
(102, 1, 400000, '2024-02-02'),
(103, 1, 500000, '2024-02-03'),
(104, 2, 250000, '2024-02-01'),
(105, 2, 350000, '2024-02-02'),
(106, 3, 700000, '2024-02-03'), -- 광고비보다 높은 매출
(107, 4, 800000, '2024-02-04'),
(108, 4, 600000, '2024-02-05'),
(109, 5, 500000, '2024-02-06');
```  
![Image](https://github.com/user-attachments/assets/7a54e305-078b-44fa-b7d5-bd8f9e362f56)

캠페인별 투자 대비 수익 (profit) 계산
```sql
SELECT m.campaign_name, m.ad_spend, SUM(p.amount) AS total_revenue,
       (SUM(p.amount) - m.ad_spend) AS profit
FROM marketing_spend m
LEFT JOIN purchases p ON m.campaign_id = p.campaign_id
GROUP BY m.campaign_name, m.ad_spend;
```  
![Image](https://github.com/user-attachments/assets/f540ce24-ebf0-4bc2-aeb9-08b6b72d7d8e)

---

## **UNION 활용한 그로스 마케팅**

#### <span style="color:orange">**이메일과 SNS 캠페인 반응 데이터 통합**</span>

```sql
-- 1) 이메일 캠페인 반응 테이블
CREATE TABLE email_campaign (
    campaign_id INT PRIMARY KEY,
    customer_id INT,
    campaign_name VARCHAR(100),
    clicks INT
);

-- 2) SNS 캠페인 반응 테이블
CREATE TABLE sns_campaign (
    campaign_id INT PRIMARY KEY,
    customer_id INT,
    campaign_name VARCHAR(100),
    clicks INT
);
```

```sql
-- 1) 이메일 캠페인 데이터 삽입
INSERT INTO email_campaign VALUES
(101, 1, '겨울 할인 이벤트', 8),
(102, 2, '신규 회원 웰컴 이벤트', 5),
(103, 3, '특가 프로모션', 12),
(104, 4, '봄맞이 할인', 3),
(105, 5, 'VIP 고객 혜택', 10);

-- 2) SNS 캠페인 데이터 삽입
INSERT INTO sns_campaign VALUES
(201, 1, '인스타그램 광고', 20),
(202, 2, '페이스북 광고', 15),
(203, 3, '틱톡 바이럴', 25),
(204, 4, '유튜브 리뷰', 10),
(205, 5, '트위터 프로모션', 18);
```  
![Image](https://github.com/user-attachments/assets/082fa10c-aba8-4c17-9dc3-f8f40a5e92f4)

```sql
SELECT customer_id, campaign_name, clicks, 'Email' AS channel
FROM email_campaign
UNION
SELECT customer_id, campaign_name, clicks, 'SNS' AS channel
FROM sns_campaign;
```  
![Image](https://github.com/user-attachments/assets/f377b274-c7a4-4e50-ad53-2a02e49993f6)

#### <span style="color:orange">**여러 국가에서의 마케팅 성과 통합**</span>

```sql
-- 1) 한국 마케팅 성과 테이블
CREATE TABLE marketing_korea (
    campaign_id INT PRIMARY KEY,
    region VARCHAR(50),
    campaign_name VARCHAR(100),
    revenue DECIMAL(10,2)
);

-- 2) 미국 마케팅 성과 테이블
CREATE TABLE marketing_usa (
    campaign_id INT PRIMARY KEY,
    region VARCHAR(50),
    campaign_name VARCHAR(100),
    revenue DECIMAL(10,2)
);

-- 3) 유럽 마케팅 성과 테이블
CREATE TABLE marketing_europe (
    campaign_id INT PRIMARY KEY,
    region VARCHAR(50),
    campaign_name VARCHAR(100),
    revenue DECIMAL(10,2)
);
```

```sql
-- 1) 한국 마케팅 성과 데이터 삽입
INSERT INTO marketing_korea VALUES
(101, 'Korea', '겨울 할인 이벤트', 500000),
(102, 'Korea', '신규 회원 웰컴 이벤트', 300000),
(103, 'Korea', '특가 프로모션', 700000),
(104, 'Korea', '봄맞이 할인', 400000),
(105, 'Korea', '회원 전용 세일', 550000);

-- 2) 미국 마케팅 성과 데이터 삽입
INSERT INTO marketing_usa VALUES
(201, 'USA', 'Winter Sale', 600000),
(202, 'USA', 'New Member Promo', 350000),
(203, 'USA', 'Exclusive Deal', 800000),
(204, 'USA', 'Spring Discount', 450000),
(205, 'USA', 'VIP Members Only', 600000);

-- 3) 유럽 마케팅 성과 데이터 삽입
INSERT INTO marketing_europe VALUES
(301, 'Europe', 'Winter Discount', 550000),
(302, 'Europe', 'Signup Bonus', 320000),
(303, 'Europe', 'Limited Offer', 750000),
(304, 'Europe', 'Easter Sale', 430000),
(305, 'Europe', 'Members-Only Discount', 570000);
```  
![Image](https://github.com/user-attachments/assets/f2b8056c-0fb1-4113-b032-f8ad8bc97cb5)

```sql
SELECT region, campaign_name, revenue
FROM marketing_korea
UNION
SELECT region, campaign_name, revenue
FROM marketing_usa
UNION
SELECT region, campaign_name, revenue
FROM marketing_europe;
```  
![Image](https://github.com/user-attachments/assets/59b7482e-8255-4128-9dad-f1e60bb51adf)

#### <span style="color:orange">**웹사이트, 모바일 앱, 오프라인 구매 데이터 통합**</span>

```sql
-- 1) 웹사이트 구매 테이블
CREATE TABLE web_purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2)
);

-- 2) 모바일 앱 구매 테이블
CREATE TABLE app_purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2)
);

-- 3) 오프라인 구매 테이블
CREATE TABLE offline_purchases (
    purchase_id INT PRIMARY KEY,
    customer_id INT,
    purchase_amount DECIMAL(10,2)
);
```

```sql
-- 1) 웹사이트 구매 데이터 삽입
INSERT INTO web_purchases VALUES
(101, 1, 75000),
(102, 2, 160000),
(103, 3, 220000),
(104, 4, 85000),
(105, 5, 135000);

-- 2) 모바일 앱 구매 데이터 삽입
INSERT INTO app_purchases VALUES
(201, 1, 70000),
(202, 2, 140000),
(203, 3, 190000),
(204, 4, 72000),
(205, 5, 120000);

-- 3) 오프라인 구매 데이터 삽입
INSERT INTO offline_purchases VALUES
(301, 1, 90000),
(302, 2, 180000),
(303, 3, 230000),
(304, 4, 95000),
(305, 5, 145000);
```  
![Image](https://github.com/user-attachments/assets/306b835d-f259-4a7b-9bf3-ed17483f977b)

고객별 구매 경로 분석  
```sql
SELECT customer_id, 'Web' AS source, purchase_amount
FROM web_purchases
UNION
SELECT customer_id, 'App' AS source, purchase_amount
FROM app_purchases
UNION
SELECT customer_id, 'Offline' AS source, purchase_amount
FROM offline_purchases;
```  
![Image](https://github.com/user-attachments/assets/3eb98f30-9892-48cd-ac34-f988486eff4c)

#### <span style="color:orange">**A/B 테스트 그룹별 성과 비교**</span>

```sql
-- 1) A/B 테스트 그룹 A 테이블
CREATE TABLE ab_test_group_a (
    user_id INT PRIMARY KEY,
    conversion BOOLEAN
);

-- 2) A/B 테스트 그룹 B 테이블
CREATE TABLE ab_test_group_b (
    user_id INT PRIMARY KEY,
    conversion BOOLEAN
);
```

```sql
-- 1) A 그룹 테스트 데이터 삽입
INSERT INTO ab_test_group_a VALUES
(101, TRUE),
(102, FALSE),
(103, TRUE),
(104, FALSE),
(105, TRUE);

-- 2) B 그룹 테스트 데이터 삽입
INSERT INTO ab_test_group_b VALUES
(201, FALSE),
(202, TRUE),
(203, FALSE),
(204, TRUE),
(205, FALSE);
```  
![Image](https://github.com/user-attachments/assets/33789fe5-a580-450b-ac90-11bc2e1fe03a)

```sql
SELECT user_id, 'Group A' AS test_group, conversion
FROM ab_test_group_a
UNION
SELECT user_id, 'Group B' AS test_group, conversion
FROM ab_test_group_b;
```  
![Image](https://github.com/user-attachments/assets/8e6fbef6-39fa-48cd-850a-a3a33fb1fc8e)

#### <span style="color:orange">**여러 마케팅 채널별 ROI 통합**</span>

```sql
-- 1) Google Ads 마케팅 성과 테이블
CREATE TABLE google_ads (
    channel VARCHAR(50),
    ad_spend DECIMAL(10,2),
    revenue DECIMAL(10,2)
);

-- 2) Facebook Ads 마케팅 성과 테이블
CREATE TABLE facebook_ads (
    channel VARCHAR(50),
    ad_spend DECIMAL(10,2),
    revenue DECIMAL(10,2)
);

-- 3) TikTok Ads 마케팅 성과 테이블
CREATE TABLE tiktok_ads (
    channel VARCHAR(50),
    ad_spend DECIMAL(10,2),
    revenue DECIMAL(10,2)
);
```

```sql
-- 1) Google Ads 성과 데이터 삽입
INSERT INTO google_ads VALUES
('Google Ads', 1500000, 4500000),
('Google Ads', 1000000, 3200000),
('Google Ads', 800000, 2500000);

-- 2) Facebook Ads 성과 데이터 삽입
INSERT INTO facebook_ads VALUES
('Facebook Ads', 1200000, 3800000),
('Facebook Ads', 900000, 2900000),
('Facebook Ads', 700000, 2000000);

-- 3) TikTok Ads 성과 데이터 삽입
INSERT INTO tiktok_ads VALUES
('TikTok Ads', 1100000, 3400000),
('TikTok Ads', 950000, 2800000),
('TikTok Ads', 600000, 1800000);
```  
![Image](https://github.com/user-attachments/assets/0801bf79-1b88-4ed1-8595-cea715618b00)

```sql
SELECT channel, ad_spend, revenue
FROM google_ads
UNION
SELECT channel, ad_spend, revenue
FROM facebook_ads
UNION
SELECT channel, ad_spend, revenue
FROM tiktok_ads;
```  
![Image](https://github.com/user-attachments/assets/a45b4363-9430-44d5-a071-0f39ebde920e)

---

## **그로스 마케팅에서의 활용**

**JOIN**: 데이블 간의 연관 데이터를 연결하여 새로운 의미 있는 정보를 생성하는 데 유용
- 고객 분석, 유입 경로별 전환율, 마케팅 비용 대비 수익 계산 등

**UNION**: 여러 테이블의 데이터를 하나의 데이터셋으로 통합하는 데 적합
- 다채널 성과 분석, 국가별 데이터 비교, A/B 테스트 결과 비교 등