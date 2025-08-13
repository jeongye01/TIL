## 📌 Path Parameter

- **정의**: URL 경로의 일부로 포함되는 값
- **용도**: **특정 리소스를 식별**할 때 사용
- **REST 원칙**: 리소스의 고유 식별자는 Path에 포함
- **예시**:
    
    ```

    GET /users/123           # ID가 123인 사용자 조회
    PUT /products/456        # ID가 456인 상품 수정
    DELETE /orders/789       # ID가 789인 주문 삭제
    
    ```
    
- **특징**:
    - URL만 보고도 어떤 리소스를 가리키는지 명확
    - 계층 구조를 표현하기 좋음 (`/users/123/posts/5`)

---

## 📌 Query Parameter

- **정의**: URL 뒤 `?` 이후에 key=value 형태로 붙는 값
- **용도**: **리소스 검색, 필터링, 정렬, 페이징** 등 조건을 지정할 때 사용
- **REST 원칙**: 리소스 자체를 식별하지 않고, 조회 조건이나 옵션을 지정
- **예시**:
    
    ```

    GET /users?role=admin&active=true    # 관리자이면서 활성인 사용자 목록
    GET /products?category=shoes&page=2&sort=price_desc
    
    ```
    
- **특징**:
    - 파라미터 조합으로 다양한 검색 조건을 표현 가능
    - 선택적(optional) 파라미터로 활용 가능

---

## 📊 비교 표

| 구분 | Path Parameter | Query Parameter |
| --- | --- | --- |
| **목적** | 리소스 식별 | 리소스 조건/옵션 지정 |
| **위치** | URL 경로 | `?key=value` 형태 |
| **REST 권장 사용** | ID, 고유값, 계층 구조 | 필터, 정렬, 페이징 |
| **예시** | `/users/123` | `/users?role=admin` |
| **필수 여부** | 필수 | 선택적 가능 |
