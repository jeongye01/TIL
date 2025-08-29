## 1. 개념

- **Trino는 오픈소스 분산 SQL 쿼리 엔진**
- 다양한 소스에서 대규모 데이터 세트를 쿼리하는 데 특화
    - 대용량 데이터 볼륨에 대한 쿼리를 전문으로 하기 때문에 데이터 레이크 쿼리를 염두에 두고 설계 되었음.
- ***2019년에 Presto 쿼리 엔진의 Fork로 시작***

## 2. 특징

- **저장소 아님** → 데이터베이스(DB)가 아니라, 데이터를 “조회·분석”하는 쿼리 엔진
- **다양한 소스 지원** → S3, Hive, MySQL, PostgreSQL, Cassandra, Kafka 등
- **표준 SQL 지원** → ANSI SQL 사용 가능
- **분산 처리** → 여러 노드가 병렬로 쿼리 실행, 대용량 데이터도 빠르게 분석
- **대화형 분석** → Hive처럼 배치(분 단위) 대신, 초 단위 응답 목표

## 3. 아키텍처

- **Cluster**: 1개의 **Coordinator** + 여러 **Worker**
- **Node**: Trino 서버 1개 (Coordinator 또는 Worker 역할)
- **Coordinator**: 쿼리 파싱, 실행 계획 수립, Worker 관리 (뇌 역할)
- **Worker**: 실제 데이터 처리 담당 (Task 실행, 데이터 Fetch/Shuffle)
- **Client**: CLI/애플리케이션/BI 툴 → SQL 쿼리 제출 → 결과 수신
- **Plugin & Connector**: 다양한 데이터 소스(Hive, Iceberg, PostgreSQL, Cassandra 등) 연결
- **Catalog**: 특정 데이터 소스 접근을 위한 설정 묶음 (Connector + Credential + Endpoint)
- **Schema & Table**: 카탈로그 안의 데이터 조직 구조

## 4. 쿼리 실행 모델

- **Statement**: 사용자가 입력한 SQL 텍스트
- **Query**: Statement가 실행 계획으로 변환된 것
- **Stage**: 쿼리를 나눈 논리적 단위 (트리 구조)
- **Task**: Stage를 물리적으로 분할한 실행 단위 (Worker에서 실행)
- **Split**: Task가 처리하는 데이터 청크(작은 단위)
- **Driver**: Task 안에서 연산을 처리하는 최소 실행 단위 (연산자들의 조합)
- **Operator**: 실제 데이터 연산자 (Scan, Filter, Join 등)
- **Exchange**: Task/Stage 간 데이터 교환

## 5. 핵심 포인트

- Trino는 DB가 아님 → **쿼리 엔진**
- 데이터 원본은 건드리지 않고, 커넥터를 통해 조회
- Glue + Athena의 백엔드에도 Presto/Trino 엔진이 사용됨
- 분산 아키텍처(Stages → Tasks → Splits → Drivers → Operators)로 대규모 데이터를 빠르게 처리

## 6. 사용 예시

- **AWS Athena** → 내부적으로 Presto 기반
- **데이터 레이크 분석** → S3에 있는 Parquet/CSV 파일을 SQL로 조회
- **이종 데이터 소스 통합 분석** → MySQL + Cassandra + Kafka 데이터를 한 번에 조인
