### 🧩 핵심 개념

`!!`는 **값을 boolean으로 강제 변환(boolean coercion)** 하는 문법이다.

두 번의 논리 부정을 통해 어떤 값이든 `true` 또는 `false`로 변환한다.

```tsx
!!value === Boolean(value);

```

---

### ⚙️ truthy / falsy 란?

JavaScript에서는 **모든 값이 조건문에서 자동으로 boolean으로 평가**된다.

이때 `true`처럼 평가되는 값을 **truthy**,

`false`처럼 평가되는 값을 **falsy** 라고 부른다.

### 🔸 falsy 값 (딱 7개)

이 값들은 조건문에서 모두 `false`로 평가된다.

```tsx
false
0
-0
0n        // BigInt 0
""        // 빈 문자열
null
undefined
NaN

```

### 🔹 truthy 값

위 7개를 제외한 **모든 값**은 truthy다.

```tsx
"hello"
123
[]
{}
() => {}
"0"       // 문자열이므로 truthy

```

---

### 🧠 `!`, `!!`, `Boolean()` 동작 비교

| 연산자 | 설명 | 예시 |
| --- | --- | --- |
| `!` | 논리 부정 (NOT): 값의 반대 boolean 반환 | `!true → false`, `!0 → true` |
| `!!` | 두 번 부정: boolean으로 변환 | `!!"text" → true`, `!!0 → false` |
| `Boolean()` | 명시적 변환 함수 | `Boolean([]) → true` |

---

### 🔍 Truthy / Falsy 값 변환 표

| 값 | `!value` | `!!value` | `Boolean(value)` |
| --- | --- | --- | --- |
| `true` | `false` | `true` | `true` |
| `false` | `true` | `false` | `false` |
| `1`, `-1`, `123` | `false` | `true` | `true` |
| `0` | `true` | `false` | `false` |
| `""` | `true` | `false` | `false` |
| `"hello"` | `false` | `true` | `true` |
| `null` | `true` | `false` | `false` |
| `undefined` | `true` | `false` | `false` |
| `NaN` | `true` | `false` | `false` |
| `[]` | `false` | `true` | `true` |
| `{}` | `false` | `true` | `true` |
| `() => {}` | `false` | `true` | `true` |

---

### 💡 활용 예시

```tsx
// 1. truthy / falsy 체크
const user = null;
console.log(!!user); // false (null은 falsy)

// 2. 옵셔널 데이터 존재 여부 확인
const isLoaded = !!response?.data;

// 3. 조건부 렌더링
{ !!list.length && <List items={list} /> }

```

---

### ⚠️ 주의사항

- `!!`는 **boolean 변환용으로만** 사용해야 한다.
- 복잡한 조건식에서는 `Boolean(value)`를 사용하는 것이 더 명확하다.

```tsx
const isActive = Boolean(userInput); // ✅ 가독성 좋음

```

---

### 🧭 요약

> !!는 truthy/falsy 값을 명확한 boolean 값으로 변환하는 연산자이다.
> 
> 
> truthy / falsy 개념을 이해하면 조건문, 렌더링, 데이터 존재 여부 판별에서
> 
> 더 직관적이고 안전한 코드를 작성할 수 있다.
>
