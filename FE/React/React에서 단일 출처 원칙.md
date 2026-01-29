## 배경

React에서 상태를 관리할 때
“왜 파생 상태(derived state)를 권장할까?”라는 의문이 생긴다.
이 질문의 답은 **단일 출처 원칙(Single Source of Truth)**에 있다.

---

## 단일 출처 원칙이란

> 어떤 정보의 ‘진짜 기준이 되는 원본은 하나만 둔다’는 설계 원칙
> 
- 모든 파생 값은 원본에서 계산된다
- 수정은 항상 원본만 변경한다
- 파생 값은 언제든 다시 만들 수 있어야 한다

---

## React에서 상태를 두 번 정의하면 생기는 문제

### 좋지 않은 예시

```tsx
const [rawData, setRawData] =useState([]);
const [filteredData, setFilteredData] =useState([]);

```

이 구조의 문제점:

- `rawData`가 변경됨
- `filteredData` 업데이트를 깜빡할 수 있음
- 두 상태 간 값 불일치 발생
- 어느 쪽이 진짜인지 혼란스러워짐

즉, **진실이 두 개가 되는 순간 버그가 생긴다.**

---

## React에서 권장하는 구조

```tsx
const [rawData, setRawData] =useState([]);

const filteredData =useMemo(
() =>filter(rawData),
  [rawData]
);

```

이 구조의 특징:

- `rawData`가 단일 출처(SSOT)
- `filteredData`는 파생 상태
- 동기화 로직이 필요 없음
- 원본만 신뢰하면 됨

---

## 핵심 차이 정리

| 구분 | 중복 상태 | 파생 상태 |
| --- | --- | --- |
| 진실의 개수 | 여러 개 | 하나 |
| 동기화 필요 | 있음 | 없음 |
| 버그 가능성 | 높음 | 낮음 |
| 구조 복잡도 | 높음 | 낮음 |
