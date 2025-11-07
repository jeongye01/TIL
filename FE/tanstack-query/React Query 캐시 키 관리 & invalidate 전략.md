## 개요

React Query는 `queryKey`를 기준으로 데이터를 캐싱한다.

따라서 **쿼리 키 설계**와 **invalidate 전략**은 데이터 일관성과 성능 모두에 직접적인 영향을 준다.

---

## 왜 쿼리 키 전략이 중요한가

- React Query는 `queryKey`를 캐시 식별자로 사용한다.
- 즉, `['users']`와 `['users', 1]`은 완전히 다른 캐시다.
- `useMutation` → `invalidateQueries`를 설계할 때도 **키 구조의 일관성**이 매우 중요하다.

---

## 기본 원칙

| 원칙 | 설명 |
| --- | --- |
| **1. 모든 쿼리 키는 중앙에서 관리** | `queryKeys` 객체로 export |
| **2. 리소스 단위 네이밍** | 예: `users`, `userDetail`, `posts` |
| **3. 부모 → 자식 계층 구조 유지** | `['users']`, `['users', id]`, `['users', id, 'comments']` |
| **4. invalidate는 “상위 키” 기준으로** | ex) `invalidateQueries(['users'])`
**상위 키를 invalidate하면 그 키를 prefix로 가지는 모든 하위 쿼리도 함께 무효화된다.** |
| **5. 항상 `as const` 사용** | 쿼리 키 widening 방지, 타입 안정성 확보 |

---

## `queryKeys` 중앙 관리 예시

```tsx
// src/lib/queryKeys.ts
export const queryKeys = {
  users: () => ['users'] as const,
  user: (id: number) => ['users', id] as const,
  posts: () => ['posts'] as const,
  post: (id: number) => ['posts', id] as const,
};
```

→ 모든 쿼리 키를 한 곳에서 정의하면:

- 오타 방지
- invalidate 시 참조 일관성 확보
- 자동완성 및 타입 추론 정확

---

## useQuery 예시

```tsx
useQuery({
  queryKey: queryKeys.user(userId),
  queryFn: () => getUser(userId),
});
```

---

## useMutation → invalidateQueries 패턴

### 1️⃣ 단일 리소스 수정

```tsx
const queryClient = useQueryClient();

const updateUserMutation = useMutation({
  mutationFn: updateUser,
  onSuccess: (_, variables) => {
    queryClient.invalidateQueries({
      queryKey: queryKeys.user(variables.id),
    });
  },
});

```

> 🔸 특정 리소스만 invalidate (효율적)
> 

---

### 2️⃣ 목록에 영향 주는 경우 (추가/삭제)

```tsx
const createUserMutation = useMutation({
  mutationFn: createUser,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: queryKeys.users() });
  },
});

```

> 🔸 상위 키(['users']) 무효화 → 목록 refetch
> 

---

### 3️⃣ 복합 구조 예시 (게시글 + 댓글)

```tsx
export const queryKeys = {
  posts: () => ['posts'] as const,
  post: (id: number) => ['posts', id] as const,
  comments: (postId: number) => ['posts', postId, 'comments'] as const,
};

```

- 댓글 추가 시 → `invalidateQueries(queryKeys.comments(postId))`
- 게시글 삭제 시 → `invalidateQueries(queryKeys.posts())`

> 구조적으로 일관되면 invalidate 대상이 명확해진다.
> 

---

## invalidate 대신 캐시 직접 수정 (`setQueryData`)

리렌더나 refetch가 과도하다면, 캐시를 직접 업데이트할 수 있다 👇

```tsx
const updateUserMutation = useMutation({
  mutationFn: updateUser,
  onSuccess: (data) => {
    queryClient.setQueryData(queryKeys.user(data.id), data);
  },
});

```

> 🔸 서버 호출 없이 즉시 UI 반영 (Optimistic Update 기반)
> 

---

## invalidate vs setQueryData

| 상황 | 추천 방식 |
| --- | --- |
| 서버 데이터 새로 받아야 함 | `invalidateQueries` |
| 일부 데이터만 수정, refetch 불필요 | `setQueryData` |
| 여러 쿼리 영향 있음 | `invalidateQueries(상위키)` |
| 낙관적 업데이트 필요 | `setQueryData` + rollback 처리 |

---

## 정리

| 구분 | 예시 | 설명 |
| --- | --- | --- |
| 상위 키 | `['users']` | 목록 캐시 |
| 하위 키 | `['users', 1]` | 상세 캐시 |
| invalidate | `invalidateQueries(['users'])` | 목록+상세 무효화 |
| setQueryData | `setQueryData(['users', 1], data)` | 특정 캐시 직접 갱신 |
| 공통 패턴 | `queryKeys.user(id)` | 일관된 키 생성 함수 |

---

## 실무 팁

- `queryKeys`는 **도메인별로 분리** (`users/queryKeys.ts`, `posts/queryKeys.ts`)
- 항상 **`as const`*로 튜플 타입 고정
- invalidate 시 **`exact` 옵션**으로 범위 조절
- 대형 프로젝트는 **QueryKey Factory 유틸**로 확장:
    
    ```tsx
    export const createKey = <T extends string>(...keys: readonly T[]) => keys as const;
    
    ```
    

---

## 💡 결론

> React Query에서 캐시 키 관리의 핵심은
> 
> 
> **"일관성 있는 키 구조 + 안전한 invalidate 범위 + as const를 통한 타입 안정성"** 이다.
> 

이 전략만 지켜도

- 캐시 깨짐 없이 안정적인 동작
- 불필요한 refetch 최소화
- 유지보수성이 비약적으로 향상된다.
