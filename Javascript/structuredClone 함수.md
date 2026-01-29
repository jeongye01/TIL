## 개요

`structuredClone`은 JavaScript에서 **객체를 안전하게 깊은 복사(Deep Copy)** 하기 위해 제공되는 표준 API다.

객체의 참조를 완전히 분리하여 원본 데이터가 변경되지 않도록 복제할 수 있다.

```tsx
const copy =structuredClone(original);

```

---

## 왜 `structuredClone`이 필요한가

기존에 깊은 복사를 위해 사용하던 방법들은 여러 한계가 있었다.


### 2. 얕은 복사(`{ ...obj }`, `Object.assign`)

- 1 depth만 복사됨
- 중첩 객체는 같은 참조를 공유
- 내부 객체 수정 시 원본 데이터 오염 가능


---

## `structuredClone`의 특징

### 지원하는 타입

- Object, Array
- Date
- Map, Set
- RegExp
- ArrayBuffer, TypedArray
- BigInt
- 순환 참조 객체



---

## 얕은 복사 vs 깊은 복사

### 얕은 복사

```tsx
const b = { ...a };

```

- 객체의 1 depth만 새로 생성
- 내부 객체는 동일한 참조를 유지
- 내부 값을 수정하면 원본도 함께 변경됨

### 깊은 복사 (`structuredClone`)

```tsx
const b =structuredClone(a);

```

- 중첩 구조 안쪽까지 전부 새 인스턴스로 복사
- 원본과 완전히 독립된 객체 생성

---

## 언제 사용해야 하는가

### 사용이 적합한 경우

- 중첩 객체가 있는 설정/옵션 객체
- 라이브러리가 내부에서 객체를 mutate하는 경우
- 상태 스냅샷 저장
- 실험 전/후 설정 값 분리
- Web Worker와 메인 스레드 간 데이터 전달

예:

```tsx
const safeOptions =structuredClone(defaultOptions);

```

---

## 성능 관점에서의 주의점

- 깊은 복사는 얕은 복사보다 비용이 큼
- 큰 객체를 자주 복사하면 GC 부담 증가
- 매 프레임, 반복 루프 안에서는 사용을 피해야 함

권장 패턴:

- **한 번 복사 → 여러 번 사용**
- 필요한 경우에만 깊은 복사 수행

---


## 요약

- `structuredClone`은 JavaScript 표준 깊은 복사 API다.
- 중첩 객체와 복잡한 타입을 안전하게 복사할 수 있다.
- 얕은 복사로 인한 사이드 이펙트를 방지할 수 있다.
- 성능 비용이 있으므로 필요한 경우에만 사용해야 한다.

> structuredClone은 안전한 도구지만, 남용하면 성능 문제가 될 수 있다.
필요한 순간에만 명확한 의도로 사용하는 것이 중요하다.
>
