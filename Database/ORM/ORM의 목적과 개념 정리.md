> ORM의 주 목적은, “객체 중심으로 작성된 서버 코드와 테이블 중심의 SQL 언어 사이를 자동으로 연결해주는 중간 다리 역할을 하는 것.”
> 

---

## 개념 이해

### 1️⃣ 서버 코드의 세계 (객체 지향)

- TypeScript / Java / Python 등은
    
    `User`, `Post`, `Comment` 같은 객체(Object) 단위로 데이터를 다룸.
    
- 개발자는 보통 이렇게 생각함:
    
    ```tsx
    user.name;
    user.posts;
    
    ```
    

---

### 2️⃣ 데이터베이스의 세계 (관계형)

- SQL은 테이블, 행(row), 열(column) 중심 언어다.
    
    ```sql
    SELECT name FROM users WHERE id = 1;
    
    ```
    
- 즉,
    - 한쪽은 객체 단위,
    - 한쪽은 테이블 단위.
        
        → 두 언어의 사고방식이 다르다.
        

---

## ORM의 역할 = “자동 번역기 + 쿼리 빌더”

| 역할 | 설명 |
| --- | --- |
| 매핑(Mapping) | 클래스 ↔ 테이블, 속성 ↔ 컬럼을 자동으로 연결 |
| 쿼리 빌딩(Query Builder) | `findMany()`, `create()` 같은 메서드를 SQL로 변환 |
| 추상화(Abstract Layer) | DB마다 다른 문법(PostgreSQL, MySQL 등)을 통일 |
| 타입 보장(Type Safety) | 컴파일 시점에 잘못된 필드 접근 감지 (`user.emial ❌`) |

---

## 예시로 보기

### SQL 직접

```sql
SELECT * FROM users WHERE email = 'test@gmail.com';

```

### ORM 사용

```tsx
const user = await prisma.user.findUnique({
  where: { email: "test@gmail.com" },
});

```

내부적으로 Prisma가 SQL을 자동 생성해서 DB에 전달한다.

개발자는 SQL 대신 객체 조작 코드만 작성하면 된다.

---

## 결론 요약

| 구분 | SQL | ORM |
| --- | --- | --- |
| 사고 방식 | 테이블 중심 | 객체 중심 |
| 코드 스타일 | 문자열 기반 쿼리 | 메서드 기반 쿼리 |
| 목적 | DB 직접 제어 | 코드로 DB 다루기 |
| ORM의 본질 | SQL을 자동 생성해주는 중간 번역기 (쿼리 빌더 + 매핑 도구) |  |
