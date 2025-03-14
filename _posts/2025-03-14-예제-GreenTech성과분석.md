---
title: "[GM 예제] 기업 마케팅 성과 분석"
date: 2025-03-14 13:30:00 +09:00
categories: [GM, Scenario | Analysis]
tags:
  [
    GM,
    그로스마케팅,
    Python,
    Pandas,
    EDA
  ]
---

# **GreenTech 기업 마케팅 성과 분석**

### **📝시나리오 개요**

가상의 회사 “GreenTech”는 친환경 제품을 판매하는 기업으로, 최근 6개월 동안 온라인과 오프라인에서 다양한 마케팅 활동을 통해 매출 성장을 달성하였다. 본 보고서는 월별 매출 변화, 광고 유형별 효과, 고객 분석 및 리뷰 데이터를 기반으로 향후 마케팅 전략을 제안하고자 한다.

### **주요 목표**

- 온라인 광고 (SNS, 검색 광고)를 활용한 브랜드 인지도 향상
- 오프라인 매장에서의 고객 유입 증가
- 고객 리뷰 및 피드백을 활용한 제품 개선
- 연령대 및 지역별 고객 분석을 통한 맞춤 마케팅 전략 수립

### **분석 항목**

최근 6개월간(2024년 8월 ~ 2025년 1월)의 데이터가 수집되었으며, 주요 분석 항목은 다음과 같다.

1. 월별 매출 및 판매량 변화
2. 광고 유형별 마케팅 비용 및 효과
3. 고객 연령대 및 지역별 판매 트렌드
4. 고객 리뷰 및 만족도 분석
5. 재구매율 및 충성 고객 분석

---

### **데이터 및 시각화 코드**

```python
!pip install koreanize-matplotlib
import koreanize_matplotlib
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
```

<span style="background-color: #F7DDBE;">1) 월별 매출 및 판매량</span>

```python
# 월별 매출 및 판매량 데이터
sales_data = pd.DataFrame({
    "월": ["2024-08", "2024-09", "2024-10", "2024-11", "2024-12", "2025-01"],
    "온라인 매출(만원)": [1200, 1350, 1500, 1650, 1800, 1950],
    "오프라인 매출(만원)": [800, 950, 1100, 1300, 1400, 1550],
    "총 판매량(개)": [300, 340, 380, 420, 460, 500]
})

display(sales_data)
```  
![Image](https://github.com/user-attachments/assets/52e334d0-8647-43bf-a08a-4938e2d5ea5d)

```python
sns.lineplot(x='월', y='온라인 매출(만원)', data=sales_data, label='온라인 매출')
sns.lineplot(x='월', y='오프라인 매출(만원)', data=sales_data, label='오프라인 매출')
plt.title("월별 매출 변화")
plt.xlabel("월")
plt.ylabel("온·오프라인 매출(만원)")
plt.legend()
plt.show()
```  
![Image](https://github.com/user-attachments/assets/dd9b9107-3f70-49b5-9b2b-f7dab9df4a61)

```python
sns.barplot(x='월', y='총 판매량(개)', data=sales_data, color='lightsteelblue')
plt.title("월별 판매량 비교")
plt.xlabel("월")
plt.ylabel("총 판매량(개)")
plt.show()
```  
![Image](https://github.com/user-attachments/assets/8803b887-51c4-449f-9165-f34bff2bbfd9)

<span style="background-color: #F7DDBE;">2) 광고 유형별 마케팅 비용 및 효과</span> 

```python
ad_data = pd.DataFrame({
    "광고 유형": ["SNS 광고", "검색 광고", "이메일 마케팅", "오프라인 홍보"],
    "마케팅 비용(만원)": [600, 800, 300, 500],
    "클릭률(%)": [5.2, 4.8, 3.2, 2.5],
    "전환율(%)": [2.3, 2.8, 1.9, 1.5]
})

display(ad_data)
```  
![Image](https://github.com/user-attachments/assets/058694a7-88a3-4cda-a784-797c302b2c08)

```python
# 마케팅 비용 및 성과 지표
fig = plt.figure(figsize=(6,6))
ax1 = fig.add_subplot()
ax1.bar(ad_data['광고 유형'], ad_data['마케팅 비용(만원)'], color='lightpink')
ax1.set_xlabel('광고 유형')
ax1.set_ylabel('마케팅 비용(만원)')

ax2 = ax1.twinx()
ax2.plot(ad_data['광고 유형'], ad_data['클릭률(%)'], color='steelblue', label='클릭률(%)', marker='o')
ax2.plot(ad_data['광고 유형'], ad_data['전환율(%)'], color='orangered', label='전환율(%)', marker='o')
ax2.set_ylabel('성과 지표(%)')

plt.title("광고 유형별 마케팅 비용 및 성과 지표")
plt.legend()
plt.show()
```  
![Image](https://github.com/user-attachments/assets/a5712d5b-7afc-4b9a-82d9-05cfd11511a4)

```python
# 클릭률 vs 전환율
index = np.arange(4)
w = 0.3
plt.bar(index, ad_data['클릭률(%)'], width=w, label='클릭률(%)')
plt.bar(index + w, ad_data['전환율(%)'], width=w, label='전환율(%)')
plt.title("광고 유형별 클릭률 vs 전환율")
plt.xlabel("광고 유형")
plt.ylabel("클릭률 vs 전환율")
plt.xticks(index+0.15, ad_data['광고 유형'])
plt.legend()
plt.show()
```  
![Image](https://github.com/user-attachments/assets/22ab06fc-7774-4e5b-a76a-e4f5690a7c83)

<span style="background-color: #F7DDBE;">3) 연령대 및 지역별 판매량</span>

```python
customer_data = pd.DataFrame({
    "연령대": ["20대", "30대", "40대", "50대"],
    "서울 판매량(개)": [120, 140, 160, 100],
    "부산 판매량(개)": [90, 110, 130, 80],
    "대구 판매량(개)": [70, 85, 95, 60],
    "광주 판매량(개)": [60, 75, 85, 55]
})

display(customer_data)
```  
![Image](https://github.com/user-attachments/assets/67a7bb47-0672-4f15-a403-7812c741628b)

```python
index = np.arange(4)
w = 0.2

plt.bar(index-w, customer_data['서울 판매량(개)'], width=w, label='서울')
plt.bar(index, customer_data['부산 판매량(개)'], width=w, label='부산')
plt.bar(index+w, customer_data['대구 판매량(개)'], width=w, label='대구')
plt.bar(index+w*2, customer_data['광주 판매량(개)'], width=w, label='광주')
plt.title("연령대별 지역 판매량(개)")
plt.xlabel("연령대")
plt.ylabel("지역별 판매량(개)")
plt.xticks(index+0.1, customer_data['연령대'])
plt.legend()
plt.show()
```  
![Image](https://github.com/user-attachments/assets/32a325dc-d85a-4bcd-91f6-1568265b70c8)

<span style="background-color: #F7DDBE;">4) 고객 리뷰 및 만족도</span> 

```python
review_data = pd.DataFrame({
    "평점": [5, 4, 3, 2, 1],
    "리뷰 수": [150, 200, 100, 50, 30],
    "주요 피드백": ["제품 품질 만족", "가격 대비 우수", "보통 만족", "배송 지연", "불량 제품"]
})

display(review_data)
```  
![Image](https://github.com/user-attachments/assets/37386290-67fb-49b1-963b-94198db75218)

```python
plt.pie(review_data['리뷰 수'], labels=review_data['평점'],autopct='%.1f%%')
plt.title("리뷰 평점 비율")
plt.show()
```  
![Image](https://github.com/user-attachments/assets/bcdf4e7f-08cb-47b3-99d8-3c700f5221a0)

```python
palettes = sns.color_palette('Set2', 5)
sns.barplot(x='주요 피드백', y='리뷰 수', data=review_data, palette=palettes)
plt.title("만족도 조사")
plt.xlabel("주요 피드백")
plt.ylabel("리뷰 수")
plt.show()
```  
![Image](https://github.com/user-attachments/assets/d5bcd5a4-de3b-4996-94c4-4b51eb0d98d5)

<span style="background-color: #F7DDBE;">5) 재구매율 및 충성 고객</span>

```python
loyalty_data = pd.DataFrame({
    "구매 횟수": ["1회", "2회", "3회 이상"],
    "고객 수": [400, 250, 150],
    "비율(%)": [50, 31.3, 18.7]
})

display(loyalty_data)
```  
![Image](https://github.com/user-attachments/assets/ff940d3c-0d3e-4c7b-831b-db49c8bdd1d8)

```python
sns.barplot(x='구매 횟수', y='고객 수', data=loyalty_data, color='yellowgreen')
plt.title("구매 횟수별 고객 수")
plt.xlabel("구매 횟수")
plt.ylabel("고객 수")
plt.show()
```  
![Image](https://github.com/user-attachments/assets/982ba4f9-c5ac-4c90-8cb2-7b913bb21981)

```python
plt.pie(loyalty_data['비율(%)'], labels=loyalty_data['구매 횟수'], autopct='%.1f%%')
plt.title("구매 횟수 비율")
plt.show()
```  
![Image](https://github.com/user-attachments/assets/945ad056-7a26-4efa-8777-e3562d28dc00)

---

### **개선 전략 제안**

1. **SNS 및 검색 광고 예산 확대**
  - 전환율이 높은 SNS 및 검색 광고에 마케팅 비용을 추가 투자하여 더 많은 고객 유입
2. **충성 고객 프로그램 도입**
  - 재구매율을 높이기 위해 멤버십 프로그램 도입, 2회 이상 구매 고객에게 추가 할인 혜택 제공
3. **배송 프로세스 개선**
  - 고객 불만이 많은 배송 지연 문제를 해결하기 위해 물류 시스템 최적화
4. **리뷰 데이터 활용한 제품 개선**
  - 고객 피드백을 반영하여 제품 품질 개선, 부정적인 리뷰의 주요 원인 분석 및 해결