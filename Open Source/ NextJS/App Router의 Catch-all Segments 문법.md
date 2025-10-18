> [...something]은 “모든 하위 경로를 한 번에 포착(catch-all)”하는 동적 라우트 문법이다.
> 

---

## 기본 개념

Next.js(App Router)에서는

폴더나 파일 이름에 대괄호(`[]`)를 쓰면 **동적 경로(Dynamic Route)** 로 인식한다.

| 문법 | 설명 | 예시 URL | 매칭 결과 |
| --- | --- | --- | --- |
| `[id]` | 단일 파라미터 | `/user/123` | `{ id: "123" }` |
| `[...slug]` | 다중 파라미터 (Catch-All) | `/blog/post/2025/10` | `{ slug: ["post", "2025", "10"] }` |
| `[[...slug]]` | 선택적 Catch-All (Optional) | `/blog`, `/blog/post/1` | `{ slug: undefined }` or `{ slug: ["post", "1"] }` |

---

## `[...something]`은 "Catch-All Route"

> “이 경로 아래에 어떤 하위 경로가 들어와도 전부 여기로 모아라”는 뜻.
> 

예를 들어 👇

```
app/
└── api/
    └── blog/
        └── [...slug]/
            └── route.ts

```

요청이 다음과 같다면:

| 요청 URL | slug 값 |
| --- | --- |
| `/api/blog/post` | `["post"]` |
| `/api/blog/post/2025` | `["post", "2025"]` |
| `/api/blog/post/2025/10` | `["post", "2025", "10"]` |

즉, **“/api/blog/”로 시작하는 모든 경로**를 한 번에 처리한다.

---

## 실제 사용 예시 (API Route)

```tsx
// app/api/blog/[...slug]/route.ts
export async function GET(
  req: Request,
  { params }: { params: { slug: string[] } }
) {
  return Response.json({
    message: "Catch-all example",
    slug: params.slug,
  });
}

```

📌 예시 요청 결과:

```
GET /api/blog/post/2025
→ { "message": "Catch-all example", "slug": ["post", "2025"] }

```

---

## 선택적 Catch-All (`[[...slug]]`)

> 슬래시 뒤에 아무 것도 없어도 매칭되게 하고 싶을 때 사용.
> 

```tsx
// app/api/docs/[[...slug]]/route.ts

```

| URL | slug |
| --- | --- |
| `/api/docs` | `undefined` |
| `/api/docs/getting-started` | `["getting-started"]` |

즉, `/api/docs` 자체도 매칭시키고 싶을 때 사용한다.
