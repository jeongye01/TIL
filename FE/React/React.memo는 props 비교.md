## 개요

`React.memo`는 컴포넌트의 불필요한 리렌더링을 막기 위한 최적화 도구다.

렌더링 여부를 판단하기 위해 **이전 props와 다음 props를 비교**하지만,

이 비교 방식은 **깊은 비교(deep compare)가 아니다.**

---

## React.memo의 기본 비교 방식

> React.memo는 props를 얕은 비교(shallow comparison)로 비교한다.
> 
- 내부적으로 `Object.is` 기반 비교를 사용
- props의 **최상위 값만 비교**
- 중첩 객체의 내부 값은 확인하지 않음

---

## 타입별 비교 방식

| props 타입 | 비교 방식 |
| --- | --- |
| primitive (number, string, boolean) | 값 비교 |
| object, array, function | 참조(reference) 비교 |

즉, 객체의 **내용이 같은지는 보지 않고**,

**같은 참조인지**만 확인한다.

---

## 동작 예시

### 1. 내용은 같지만 참조가 다른 경우 (리렌더 발생)

```tsx
<MyComponent data={[1,2,3]} />

```

- 렌더링마다 새로운 배열 생성
- 이전 props와 참조가 다름
- `React.memo` 무효 → 리렌더 발생

---

### 2. 내용은 바뀌었지만 참조가 같은 경우 (리렌더 발생 안 함)

```tsx
const data =useRef({count:0 });
data.current.count +=1;

<MyComponentdata={data.current} />

```

- 객체 참조는 동일
- 내부 값만 변경됨
- `React.memo`가 변경을 감지하지 못함

---

## 왜 깊은 비교를 하지 않는가

React.memo가 깊은 비교를 하지 않는 이유는 명확하다.

1. **성능 문제**
    - 깊은 비교는 비용이 큼
    - 렌더마다 실행하면 오히려 성능 저하
2. **예측 가능성**
    - 참조 기반 비교는 동작이 단순하고 명확함
    - React의 불변성 모델과 잘 맞음
3. **책임 분리**
    - props 안정화는 개발자의 책임
    - React는 비교만 수행

---

## 커스텀 비교 함수

`React.memo`는 두 번째 인자로 비교 함수를 받을 수 있다.

```tsx
React.memo(Component,(prevProps, nextProps) => {
return prevProps.value === nextProps.value;
});

```

- `true` 반환 → props 동일 → 리렌더 방지
- `false` 반환 → props 변경 → 리렌더

단, 깊은 비교를 직접 구현하면

**성능 비용과 복잡도가 급격히 증가**하므로 신중해야 한다.
