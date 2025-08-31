## 1. 개념

- **오픈소스 비즈니스 인텔리전스(BI) & 데이터 시각화 툴**
- SQL을 몰라도 클릭 몇 번으로 데이터 분석 가능
- 데이터베이스(MySQL, PostgreSQL, Athena, BigQuery, Redshift 등)와 연결해 **대시보드·리포트·차트**를 만들 수 있음

👉 쉽게 말해 **“무료·오픈소스 Tableau/Power BI”**

---

## 2. 특징

- **웹 기반 UI** → 브라우저에서 바로 사용 가능
- **쿼리 빌더** → SQL 몰라도 드래그&드롭으로 질의 작성
- **SQL Editor** → SQL로 직접 쿼리 가능
- **대시보드** → 여러 시각화를 한 화면에 배치해서 모니터링
- **알림/공유** → 결과를 이메일/Slack으로 알림, 보고서 자동 발송 가능
- **오픈소스** → 무료로 배포 가능, 유료 엔터프라이즈 플랜도 있음

---

## 3. 사용 예시

- Athena에 연결 → S3 데이터(Parquet, 로그 등) 분석 대시보드 생성
- RDS(MySQL, Postgres)에 연결 → 매출/트래픽/사용자 리포트 생성
- 비개발자도 클릭으로 분석 → 데이터팀이 공용 대시보드 운영

---

## 4. 비교

- **Tableau / Power BI**: 강력한 상용 BI 툴
- **Metabase**: 무료 + 가볍고 빠름, 설치 쉬움
- **Superset (Apache)**: 좀 더 엔지니어 친화적 오픈소스 대시보드 툴

## 5. Docker로 실행
```json
docker run -d -p 3000:3000 --name metabase metabase/metabase
```
