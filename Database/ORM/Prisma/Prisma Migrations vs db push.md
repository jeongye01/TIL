## 📌 상황

Prisma에서는 DB 스키마를 `schema.prisma`로 정의하고,

이를 실제 DB에 반영할 때 두 가지 방법이 있다:

1. **`prisma migrate dev`** → `prisma/migrations/` 폴더 생성 + SQL 실행
2. **`prisma db push`** → 스키마를 DB에 강제로 반영 (이력 없음)

---

## ✅ Migrations의 특징

- **Migration History**(`prisma/migrations/`)를 남김
- 단계별 `ALTER TABLE` 같은 SQL 스크립트가 기록됨
- 협업 시 팀원끼리 같은 이력을 공유할 수 있음
- 운영 환경에서 안전하게 배포 가능 (데이터 보존)

---

## ✅ db push의 특징

- Migration History를 남기지 않음
- DB 구조만 `schema.prisma`와 맞춤
- 필요하다면 테이블 drop + 재생성 → ⚠️ 데이터 유실 위험
- 개발/프로토타입 단계에서 빠른 실험용으로 적합

---

## 🔎 비교 요약

| 구분 | prisma migrate dev | prisma db push |
| --- | --- | --- |
| 이력 관리 | ✅ 있음 (`migrations/` 폴더) | ❌ 없음 |
| 협업 동기화 | ✅ 팀원과 동일 이력 공유 가능 | ❌ 꼬일 수 있음 |
| 운영 배포 | ✅ 안전 (데이터 보존) | ⚠️ 위험 (데이터 날릴 수 있음) |
| 사용 목적 | 실제 개발/배포용 | 개인용, 실험/프로토타입 |
