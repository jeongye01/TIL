### 🔍 배경

`Object.entries()`, `Object.keys()`, `Object.values()` 같은 자바스크립트 내장 함수는

타입스크립트에서 사용할 때 **key의 타입이 `string`으로 widen(확장)** 되는 문제가 있다.

```tsx
type InstanceRole = 'PRIMARY' | 'STANDBY';

const data: Record<InstanceRole, number[]> = {
  PRIMARY: [1, 2, 3],
  STANDBY: [4, 5, 6],
};

for (const [key, value] of Object.entries(data)) {
  // key: string ❌ (원래는 "PRIMARY" | "STANDBY" 여야 함)
  // value: number[] ✅ (제네릭 덕분에 정확히 추론됨)
}

```

> 💡 즉, Object.entries()는 value는 잘 추론하지만 key를 정확히 추론하지 못한다.
> 
> 
> 이건 JS 런타임에서 모든 객체 키가 문자열로 변환되기 때문이다.
> 

---

### ⚙️ 원인

TypeScript 표준 타입 정의(lib.es5.d.ts):

```tsx
entries<T>(o: { [s: string]: T } | ArrayLike<T>): [string, T][];
```

즉:

- key 타입은 **항상 `string`**
- value 타입은 **제네릭 `T`**

따라서 `Record<'PRIMARY' | 'STANDBY', number[]>` 같은 구조라도,

entries를 돌리면 key가 widen되어 string으로 추론된다.

---

### 💡 해결 방법: `typedEntries`

이 문제를 해결하기 위해 key 타입을 유지한 채 `Object.entries()`를 감싸는

**타입 안전 유틸리티 함수(Type-safe Utility Function)** 를 만든다.

```tsx
/**
 * 🔧 typedEntries
 *
 * Object.entries()는 [string, T][]를 반환하므로
 * key 타입이 widen되어 'string'으로 변한다.
 *
 * 이 함수를 사용하면 key를 keyof T로, value를 T[keyof T]로 유지시켜
 * 타입 손실 없이 안전하게 entries를 순회할 수 있다.
 */
export const typedEntries = <T extends Record<string, unknown>>(
  obj: T
): [keyof T, T[keyof T]][] => {
  return Object.entries(obj) as [keyof T, T[keyof T]][];
};

```

---

### ✅ 사용 예시

```tsx
type InstanceRole = 'PRIMARY' | 'STANDBY';
const instanceMap = {
  PRIMARY: [1, 2, 3],
  STANDBY: [4, 5, 6],
};

for (const [role, instances] of typedEntries(instanceMap)) {
  // role: "PRIMARY" | "STANDBY" ✅
  // instances: number[] ✅
  console.log(role, instances);
}

```

> Object.entries()를 직접 썼을 때처럼 key가 string으로 widen되지 않고,
> 
> 
> `'PRIMARY' | 'STANDBY'`로 정확히 추론된다.
>
