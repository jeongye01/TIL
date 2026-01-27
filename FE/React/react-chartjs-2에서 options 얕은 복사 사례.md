### 배경

`react-chartjs-2`에서 차트를 생성할 때 아래와 같은 코드가 사용된다.

```tsx
options: options && { ...options }

```

처음 보면 단순한 문법처럼 보이지만, 이 한 줄에는 React와 Chart.js 사이의 역할 분리가 숨어 있다.

---

### 문제 상황

- React 컴포넌트에서 전달하는 `options`는 **props**이다.
- React의 원칙상 props는 **불변(immutable)** 이어야 한다.
- 하지만 Chart.js는 내부 동작 과정에서 `options` 객체를 **직접 수정(mutate)** 한다.
    - scale 계산
    - 내부 캐시 값 저장
    - 기본 옵션 병합 등

만약 원본 `options` 객체를 그대로 Chart.js에 넘기면:

- Chart.js가 props를 직접 수정하게 되고
- React의 불변성 가정이 깨지며
- 예측 불가능한 버그와 디버깅 난이도가 급격히 올라간다.

---

### 해결 방식

```tsx
options: options && { ...options }

```

이 코드는 다음을 의미한다.

- `options`가 존재하면
- **얕은 복사(shallow copy)** 를 만들어
- Chart.js에 전달한다.

결과적으로:

- React가 가진 `options` 객체는 보호된다.
- Chart.js는 복사본을 자유롭게 수정할 수 있다.
- React의 불변성과 Chart.js의 가변성이 분리된다.

---

### 왜 얕은 복사만 사용하는가

- `options` 객체는 구조가 깊고 크다.
- 매 렌더마다 deep copy를 하면 성능 비용이 크다.
- Chart.js는 주로 최상위 옵션이나 일부 내부 값만 변경한다.
- 따라서 얕은 복사로도 목적을 충분히 달성할 수 있다.

---

### 핵심 정리

- 이 코드는 성능 최적화 코드가 아니다.
- React props 훼손을 방지하기 위한 **안전장치**다.
- `react-chartjs-2`는 React의 불변성과 Chart.js의 가변성을 중간에서 조율하는 래퍼 역할을 한다.
