## 📌 개념

- *`ipython-sql`*은 Jupyter Notebook / IPython 환경에서 **SQL 쿼리를 직접 실행할 수 있게 해주는 확장 라이브러리**
- Python 코드와 SQL을 같은 노트북에서 섞어 쓸 수 있음
- 쿼리 결과를 Pandas DataFrame으로 받아 바로 데이터 분석 가능

---

## ⚡ 주요 기능

- **마법 명령어(Magic Commands)**
    - `%sql` : 한 줄 SQL 실행
    - `%%sql` : 여러 줄 SQL 실행
- **다양한 DB 연결 지원**
    - SQLite, MySQL, PostgreSQL 등 → SQLAlchemy 기반이라 대부분의 DB 가능
- **쿼리 결과 → Pandas 변환**
    - 데이터프레임에 담아 시각화, 통계 분석 등 Python 생태계 활용 가능

---

## 🛠️ 사용법

```bash
# 설치
pip install ipython-sql
```

```python
# Jupyter에서 로드
%load_ext sql

# DB 연결 (SQLite 예시)
%sql sqlite:///example.db

# 한 줄 쿼리
%sql SELECT * FROM users LIMIT 5;

# 여러 줄 쿼리
%%sql
SELECT name, age
FROM users
WHERE age > 20;

```

---
