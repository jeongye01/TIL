## 개요

`as const`는 **값을 리터럴(literal) 타입으로 고정하고, 수정 불가능한 상수로 취급**하도록 만드는 TypeScript 문법이다.

즉, “**이 값은 절대 바뀌지 않아. 이 형태 그대로 타입으로 써줘.**” 라고 컴파일러에 알려주는 역할(타입을 좁히는 것(컴파일타임))을 한다.

---

## 기본 개념

```tsx
const mood = "happy";
// 타입: string

const mood = "happy" as const;
// 타입: "happy"
```

- 기본적으로 TypeScript는 `"happy"`를 **일반 문자열(`string`)**로 추론한다.
- `as const`를 붙이면 **리터럴 타입(`"happy"`)**으로 고정된다.
- 값 변경 가능성도 차단되어 **readonly**로 간주된다.

---

## 객체와 배열에 적용

### 객체에 적용

```tsx
const ROLE = {
  ADMIN: "admin",
  USER: "user",
} as const;

// ROLE.ADMIN 타입 → "admin"
// ROLE.USER 타입 → "user"

```

> as const를 쓰지 않으면 "admin"과 "user"가 단순히 string으로 추론된다.
> 

이걸 이용하면 아래처럼 안전한 타입을 생성할 수 있다 👇

```tsx
type Role = typeof ROLE[keyof typeof ROLE];
// "admin" | "user"
```

---

### 배열에 적용

```tsx
const moods = ["happy", "sad", "angry"] as const;

// 타입: readonly ["happy", "sad", "angry"]
type Mood = typeof moods[number];
// "happy" | "sad" | "angry"

```

- 배열 요소가 string[]이 아닌 **리터럴 튜플**로 인식된다.
- `typeof arr[number]`를 사용하면 요소 리터럴 유니온 타입을 얻을 수 있다.

---

## 실무 예시

### enum 대체

```tsx
const STATUS = ["RUNNING", "STOPPED", "FAILED"] as const;
type Status = typeof STATUS[number];
// "RUNNING" | "STOPPED" | "FAILED"

```

---

## 요약

| 구분 | `as const` 없음 | `as const` 있음 |
| --- | --- | --- |
| 문자열 | `string` | `"literal"` |
| 숫자 | `number` | `42` |
| 배열 | `string[]` | `readonly ["a", "b"]` |
| 객체 | `{ key: string }` | `{ readonly key: "a" }` |

---

## 기억할 점

- `as const`는 **리터럴 타입 고정 + readonly 처리**를 동시에 수행한다.
- enum 대체, 상수 정의, key-value 기반 타입 유도에 자주 쓰인다.
- **타입 안정성을 높이고, 오타나 잘못된 key 접근을 컴파일 타임에 방지**할 수 있다
