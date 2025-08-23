## 1. 정의

- **Apache Parquet**: 효율적인 컬럼 지향(**efficient columnar data**) 저장 포맷
- 빅데이터/데이터레이크 환경(Athena, Spark, Hive 등)에서 표준처럼 사용됨

---

## 2. Row 기반(CSV) vs Column 기반(Parquet)

- **Row-based (CSV, JSON 등)**
    - 한 행(Row) 단위로 저장
    - 전체 레코드를 자주 읽는 트랜잭션 처리에 적합
    - 특정 컬럼만 집계해도 모든 컬럼을 다 읽어야 함 → 비효율
- **Column-based (Parquet, ORC)**
    - 컬럼 단위로 연속 저장 (**efficient columnar data**)
    - 특정 컬럼만 스캔 가능 (**column pruning**)
    - 값이 연속적이라 압축률 높고 CPU 캐시 효율 ↑
    - 집계·분석 쿼리에 최적화

---

## 3. 장점

1. **저장 효율**: CSV 대비 5~10배 작은 용량 (중복 값 압축)
2. **쿼리 성능**: 필요한 컬럼만 읽기 가능 → 스캔 비용↓ 속도↑
3. **스키마 내장**: 컬럼 타입/구조 정보 포함 → SQL 엔진이 바로 인식
4. **호환성**: Athena, Redshift Spectrum, Spark, Pandas 등 대부분 지원

---

## 4. 언제 쓰나?

- 데이터 레이크 저장소(원본 CSV → 정제본 Parquet)
- BI/분석용 쿼리 (집계, 필터 중심)
- Athena/BigQuery처럼 “스캔한 데이터 양” 기준 과금되는 서비스에서 비용 절감

## 5. 효율 관점 요약

- **저장 공간**: Parquet > CSV (보통 5~10배 절약)
- **쓰기(append)**: CSV > Parquet
- **읽기/집계**: Parquet >>> CSV
    - **Row 포맷**: 전체 레코드를 자주 조회(OLTP, 트랜잭션) → 효율적
    - **Column 포맷**: 특정 컬럼에 대한 집계/분석(OLAP, BI, 리포트) → 효율적
