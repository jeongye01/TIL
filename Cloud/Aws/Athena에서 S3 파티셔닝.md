## 1. 파티셔닝이란?

- **데이터의 특정 속성을 기준으로 Amazon S3의 디렉터리(접두사) 구조를 나누어 저장하는 것**
- 이 기준이 되는 속성을 **파티션 키**라고 부름
- 일반적인 파티션 키: 날짜(`dt`), 연도(`year`), 월(`month`) 등
- 다중 파티션도 가능: `dt=2025-08-19/product_category=Toys/market=US`

---

## 2. 왜 중요한가?

- Athena 쿼리 시 **불필요한 데이터를 스캔하지 않게 해줌** → 비용 절감, 속도 향상
- 단, 파티션이 너무 많으면 메타데이터 오버헤드 발생, 너무 적으면 불필요한 전체 스캔 발생
- 따라서 **카디널리티가 낮고 자주 사용하는 컬럼**이 파티션 키로 적합
    1. **카디널리티 낮음 (Low cardinality)**
        - `성별` (Male / Female → 값이 2개)
        - `요일` (월~일 → 값이 7개)
        - `지역코드` (10개 정도 구분)
        
        👉 값의 종류가 적어서 "카디널리티가 낮다"
        
    2. **카디널리티 높음 (High cardinality)**
        - `사용자 ID` (수백만 개)
        - `주문 번호` (매번 다름)
        - `타임스탬프` (거의 모든 행이 다름)
        
        👉 값의 종류가 많아서 "카디널리티가 높다”
        

---

## 3. 파티션 테이블 생성

- `CREATE TABLE` → `PARTITIONED BY` 구문으로 지정
- `CREATE TABLE AS` → `WITH (partitioned_by = ARRAY['dt'])` 로 지정
- 성능을 위해 파티션 키는 보통 **STRING 타입**으로 설정

예시:

```sql
CREATE TABLE sales_partitioned (
    amount BIGINT,
    sold_at TIMESTAMP,
    product_category STRING
)
PARTITIONED BY (sales_date STRING);

```

---

## 4. 파티션된 테이블 쿼리

- Athena는 **WHERE 조건절을 통해 파티션 메타데이터를 먼저 필터링**
- 해당 파티션 디렉토리만 읽으므로 스캔 데이터량이 줄어듦

예시:

```sql
SELECT SUM(amount) AS total_revenue
FROM sales_partitioned
WHERE sales_date BETWEEN '2023-02-27' AND '2023-03-05'
AND product_category = 'Toys';

```

- Hive 테이블에서는 파티션 키와 컬럼을 모두 조건자로 넣어야 불필요한 스캔을 방지 가능
- Iceberg 테이블은 엔진이 컬럼-파티션 관계를 인식하므로 더 직관적 (파티션과 관련된 컬럼을 자동으로 인식)

| 항목 | Hive 테이블 | Iceberg 테이블 |
| --- | --- | --- |
| 파티션 관리 | S3 경로 기반 | 메타데이터 기반 |
| 파티션 등록 | 수동 (Crawler, MSCK 필요) | 자동 |
| 쿼리 조건 | 파티션 키 + 컬럼 둘 다 줘야 함 | 컬럼만 줘도 됨 |

---

## 5. 실험 결과 (내 프로젝트)

- 데이터 크기: 약 **10MB**
- **파티션 전:** `WHERE dateadded=20250819` → 실행 2.238s, 스캔 10.09MB
- **파티션 후:** `WHERE dt='2025-08-19'` → 실행 2.735s, 스캔 10.09MB

👉 작은 데이터셋에서는 오히려 파티션 메타데이터 처리 오버헤드 때문에 **속도가 더 느릴 수 있음**

👉 하지만 수백 MB~GB 이상으로 커지면 **비용/속도 절감 효과 극대화**

---

## 6. 오늘의 교훈

- 파티셔닝은 **작은 데이터셋에서는 체감이 안 되거나 느려 보일 수도 있다.**
- 하지만 데이터가 커질수록 **쿼리 성능과 비용 최적화에 필수적인 전략**이다.
- Athena + S3를 운영 환경에서 쓴다면, 초기에 파티션 설계를 잘 해두는 게 장기적으로 큰 차이를 만든다.

파티션 전

<img width="692" height="315" alt="image" src="https://github.com/user-attachments/assets/7a2d33b6-7375-41a3-b3db-1dd25e69c6b4" />


파티션 후

<img width="689" height="331" alt="image" src="https://github.com/user-attachments/assets/e0d9f66b-bae3-46d1-ac8d-6809023e5dd8" />


참고

https://docs.aws.amazon.com/ko_kr/athena/latest/ug/ctas-partitioning-and-bucketing-what-is-partitioning.html
