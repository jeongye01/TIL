## 1. pyarrow란?

- **Apache Arrow** 프로젝트의 Python 구현체
- **컬럼 지향 메모리 포맷**을 사용해 데이터를 빠르게 직렬화/역직렬화
- Pandas, Spark, BigQuery, Parquet 등 다양한 툴 사이에서 **데이터 교환 표준** 역할

---

## 2. 주요 기능

- **Parquet 파일 처리**
    - Pandas DataFrame ↔ Parquet 변환 지원
    - 예:
        
        ```python
        df.to_parquet("data.parquet", engine="pyarrow")
        pd.read_parquet("data.parquet", engine="pyarrow")
        
        ```
        
- **Arrow Table ↔ Pandas 변환**
    - 고성능 in-memory 연산 가능
- **대규모 데이터 처리**
    - GB 단위 데이터도 빠르게 변환

---

## 3. Pandas에서 pyarrow가 필요한 이유

- Pandas 자체는 Parquet 저장 기능이 없음 → **엔진(backend)** 필요
- 선택지:
    - `pyarrow` (Apache Arrow 기반, 가장 많이 사용)
    - `fastparquet` (경량, 속도 빠름)
- 보통 호환성과 기능이 더 풍부한 **pyarrow**를 표준으로 사용

---

## 4. 특징/장점

- **효율적 저장**: 컬럼 지향 포맷이라 압축 효율 높음
- **분석 최적화**: 특정 컬럼만 읽는 작업에 빠름 (쿼리형 워크로드 최적)
- **생태계 통합**: Spark, Pandas, DuckDB, BigQuery, Dask 등과 바로 연결 가능

---

## 5. 핵심 요약

> pyarrow는 Pandas와 Parquet을 연결해주는 핵심 라이브러리다.
> 
> 
> 데이터 엔지니어링에서 빠른 직렬화, 호환성, 대규모 데이터 처리를 위해 사실상 표준으로 쓰인다.
>
