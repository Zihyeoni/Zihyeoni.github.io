---
title: "[Looker Studio] 기본 사용법"
date: 2025-03-06 19:00:00 +09:00
categories: [GM, 시각화]
tags:
  [
    Looker Studio,
    시각화
  ]
---

# **📈Looker Studio 기본 사용법 및 데이터 연결**

#### **Looker Studio** 란?  
Google에서 제공하는 데이터 시각화 및 보고서 제작 도구 (비즈니스 인텔리전스(BI) 도구로 활용)  
Google의 다양한 서비스 및 타사 데이터 소스와 연동이 가능해 데이터를 효과적으로 분석 및 공유 가능

## **주요 특징**

**① 다양한 데이터 소스 연결**
  - Google Analytics, Google Ads, BigQuery, Google Sheets 등 Google 제품과 원활히 통합됨
  - MySQL, PostgreSQL 같은 데이터베이스, CSV 파일, 또는 타사 API를 통해 외부 데이터 연결 가능

**② 사용자 친화적 인터페이스**
  - 코딩 지식 없이도 쉽게 대시보드와 보고서를 생성할 수 있는 드래그 앤 드롭 방식의 UI 제공
  - 원하는 데이터 필드를 추가하거나 필터링, 정렬을 간단히 설정 가능

**③ 실시간 데이터 업데이트**
  - 연결된 데이터 소스에서 실시간으로 데이터를 가져와 보고서를 동적으로 업데이트

**④ 맞춤형 시각화**
  - 다양한 차트 유형(라인 차트, 바 차트, 파이 차트 등)과 지도 시각화 지원
  - 사용자가 원하는 디자인으로 대시보드 커스터마이징 가능

**⑤ 협업 및 공유**
  - Google Drive와 연동되어 대시보드를 팀원들과 공유하거나 협업 가능
  - 실시간으로 업데이트된 데이터를 기반으로 공동 작업 가능

**⑥ 무료로 제공**
  - Looker Studio는 기본적으로 무료로 제공되며, 일부 고급 기능은 유료 서비스(예: Looker Studio Pro)에서 제공

## **Looker Studio 장단점**

**장점**
- 무료로 제공되며 사용이 간단
- 다양한 데이터 소스와 통합 가능
- 실시간으로 데이터를 업데이트하여 최신 데이터 반영

**단점**
- 복잡한 데이터 분석을 위한 고급 기능 제한적
- 일부 데이터 소스 연결 시 추가 설정 필요

---

## **Looker Studio에서 데이터 연결 및 시각화**

먼저, Looker Studio에 접속 후 Google 계정으로 로그인한다. ➡️ [Looker Studio 접속](https://lookerstudio.google.com/)

새 보고서를 생성한다.  
![Image](https://github.com/user-attachments/assets/243a8df1-1501-40b6-97a1-3bb815cb6b48)

데이터 소스를 연결하기 위해 다음의 예제 데이터를 Google Sheets에 업로드한다.  
```
월 제품군 매출  비용  이익
1월  전자제품  500000  300000  200000
1월  가구  300000  150000  150000
2월  전자제품  600000  350000  250000
2월  가구  400000  200000  200000
3월  전자제품  550000  320000  230000
3월  가구  350000  170000  180000
```  
![Image](https://github.com/user-attachments/assets/312892e7-1752-40d8-87a3-a8165578ab9b)

저장한 데이터를 선택하고 연결한다.  
<img width="1235" alt="Image" src="https://github.com/user-attachments/assets/c1d88dd3-9b4a-4745-a3f8-ff3ed2524c56" />  

![Image](https://github.com/user-attachments/assets/680fc402-874e-483b-a01f-6d6bc3dfe7c9)

### **대시보드 구성**

**바 차트 추가**
- 데이터: 월 (X축), 매출 (Y축)  
![Image](https://github.com/user-attachments/assets/3ff5461d-5519-4602-be9f-27840bad26b7)

**파이 차트 추가**
- 데이터: 제품군 (범주), 이익 (값)  
![Image](https://github.com/user-attachments/assets/b7037dbe-366d-4112-9d67-718fd70f49c6)

**데이블 추가**
- 데이터: 월, 제품군, 매출, 비용, 이익  
![Image](https://github.com/user-attachments/assets/78ee21ee-1a2f-4295-a34c-aa0d524363d1)

**필터 및 날짜 범위 추가**
- 제품군 별로 필터링 설정
- 날짜 선택기 추가
![Image](https://github.com/user-attachments/assets/ce4e36c8-5bd0-4ef6-ac84-40f0a0397f86)

---

## **완성된 대시보드 사용**

- **필터**: 특정 제품군 선택 및 분석
- **날짜 범위**: 특정 기간의 데이터 선택
- **차트 해석**: 월별 매출 추세, 제품군별 이익 기여도 파악