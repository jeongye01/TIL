## 1. 개념

- **서버리스 데이터 통합(ETL) 서비스**
- 분석을 위해 데이터를 준비하고 변환하는 데 유용
- S3, RDS, DynamoDB 같은 여러 데이터 소스에 있는 데이터를 **탐색·정리·변환·적재**할 수 있게 도와줌

---

## 2. Glue의 주요 구성 요소

1. **Glue Crawler**
    - S3 같은 저장소를 스캔해서 **스키마(테이블 구조)** 를 자동으로 추출
    - DB 메타데이터를 Glue Data Catalog에 기록
    - 예: `s3://gdelt-clean/*.parquet` → `events` 테이블 생성
2. **Glue Data Catalog**
    - 스키마 정보를 저장하는 **메타데이터 저장소**
    - Athena, Redshift Spectrum, EMR 같은 서비스가 이 Catalog를 보고 쿼리 실행
3. **Glue ETL Job**
    - PySpark 기반으로 데이터를 **변환(Transform)** 해서 다시 저장 가능
    - Glue 작업 북마크 : Glue ETL Job(Spark 기반 변환 작업)을 여러 번 실행할 때, 매번 전체 데이터를 다시 처리하지 않고, 이전에 처리한 위치까지 기억해 두는 기능.
    - 예: CSV → Parquet 변환, 컬럼 추가, 필터링 등
4. Glue Elastic Views
    - SQL을 이용해 여러 데이터  스토어의 데이터를 결합하고 복제
5. Glue DataBrew
    - 사전 빌드된 변환을 사용해 데이터를 정리하고 정규화
6. **Glue Studio**
    - GUI 기반으로 ETL 파이프라인을 설계/실행할 수 있는 화면
