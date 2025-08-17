### 1. DB(SQL)의 역할

- 대용량 데이터를 **저장**하고, **조회/집계**에 최적화되어 있음.
- `SELECT`, `JOIN`, `GROUP BY` 같은 쿼리로 데이터를 빠르게 필터링하고 합산 가능.

### 2. Pandas(DataFrame)의 강점

- **데이터 가공/전처리**
    - 결측치 처리, 새로운 컬럼 생성, 문자열/시간 변환이 편리하다.
- **통계/수학 연산**
    - `describe()`, `mean()`, `corr()` 같은 기본 통계 메서드 제공.
- **시각화와의 호환성**
    - Seaborn, Matplotlib 등 라이브러리가 DataFrame을 바로 입력받음.
- **머신러닝 연계**
    - 대부분의 ML 라이브러리(Scikit-learn, TensorFlow 등)는 DataFrame/Numpy 배열을 입력으로 사용.

### 3. 실무에서의 표준 흐름

1. **DB**: SQL로 필요한 데이터 추출
2. **Pandas**: 추출된 데이터를 DataFrame으로 불러오기
3. **분석 & 시각화**: Pandas + Seaborn/Matplotlib
4. **ML/통계 모델링**: DataFrame → ML 라이브러리

👉 즉, **DB는 저장과 조회**, **Pandas는 분석과 전처리**에 최적화되어 있기 때문에

데이터 분석 시에는 Pandas로 데이터를 읽어 활용하는 것이 일반적이다.
