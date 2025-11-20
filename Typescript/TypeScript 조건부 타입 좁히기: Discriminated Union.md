## 1. 문제 상황 (The Problem)

범용적인 `Table` 컴포넌트를 만들면서, **선택 모드(`rowSelectMode`)**에 따라 **이벤트 핸들러(`onRowSelect`)**의 타입이 달라져야 하는 상황이 발생했다.

- `single` 모드일 때: `(id: string, row: T) => void` (단일 값)
- `checkbox` 모드일 때: `(ids: string[], rows: T[]) => void` (배열 값)

처음에는 하나의 인터페이스에 모든 속성을  넣어서 처리하려고 했다.

```tsx
// ❌ 좋지 않은 패턴: 속성 간의 관계가 정의되지 않음
interface TableOptions<T> {
  rowSelectMode?: 'single' | 'checkbox';
  // 모드에 따라 인자가 달라져야 하는데, 유니온으로 뭉뚱그려짐
  onRowSelect?: (ids: string | string[], rows: T | T[]) => void;
}
```

### 문제점

1. **타입 추론 실패:** TypeScript는 `rowSelectMode`가 'single'이라고 해서 `onRowSelect`의 인자가 단일 값이라고 확신하지 못한다.
2. **불안전한 코드:** 개발자가 내부에서 `as`(Type Assertion)를 써서 강제로 타입을 지정해야 하거나, 사용하는 쪽에서 불필요한 타입 가드를 작성해야 한다.

## 2. 해결 방법: Discriminated Union (구별된 유니온)

서로 연관된 속성들을 **하나의 그룹**으로 묶고, 이를 구별할 수 있는 **판별자(Discriminant)**를 심어주는 패턴이다.

### 핵심 원리

1. *공통 속성(Base)**과 **개별 속성(Case)**을 분리한다.
2. 각 Case마다 고유한 **리터럴 타입의 판별자(`rowSelectMode`)**를 필수(Required)로 지정한다.
3. 이들을 `Base & (Case A | Case B)` 형태로 결합한다.

## 3. 리팩토링 코드

```tsx
// 1. 공통 속성 (변하지 않는 부분)
interface BaseTableOptions<TData> {
  data: TData[];
  manualSorting?: boolean;
  // ... 기타 공통 props
}

// 2. 단일 선택 모드 (Single)
interface SingleSelectOptions<TData> {
  rowSelectMode: 'single'; // 🔥 판별자
  onRowSelect?: (rowId: string, row?: TData) => void;
}

// 3. 다중 선택 모드 (Multi)
interface MultiSelectOptions<TData> {
  rowSelectMode: 'checkbox'; // 🔥 판별자
  onRowSelect?: (rowIds: string[], rows?: TData[]) => void;
}

// 4. 선택 기능 없음 (None - 옵션)
interface NoSelectOptions {
  rowSelectMode?: never;
  onRowSelect?: never;
}

// ✅ 최종 타입: Discriminated Union
export type TableOptionType<TData> = BaseTableOptions<TData> &
  (SingleSelectOptions<TData> | MultiSelectOptions<TData> | NoSelectOptions);
```

## 4. 결과 및 장점

이제 컴포넌트 내부에서 `if` 문 하나만으로 완벽하게 타입이 좁혀진다.

```tsx
const handleRowClick = (row: TData) => {
  // mode가 'single'인 경우, onRowSelect는 자동으로 단일 인자 함수가 됨
  if (props.rowSelectMode === 'single') {
    props.onRowSelect?.(row.id, row); // ✅ OK
  } 
  // mode가 'checkbox'인 경우, 자동으로 배열 인자 함수가 됨
  else if (props.rowSelectMode === 'checkbox') {
    props.onRowSelect?.([row.id], [row]); // ✅ OK
  }
};
```

### 이 패턴이 좋은 이유 (추상화 관점)

1. **불가능한 상태 방지 (Make Illegal States Unrepresentable):** 'single' 모드이면서 배열을 받는 함수를 넣는 실수 자체를 컴파일 단계에서 막아준다.
2. **관심사의 분리:** 사용하는 쪽과 구현하는 쪽 모두 `mode` 값 하나만 신경 쓰면 관련된 타입이 알아서 따라온다.
3. **확장성 (Open-Closed):** 나중에 `range` 모드가 생겨도 기존 코드를 건드리지 않고 타입(`RangeSelectOptions`)만 추가하여 유니온에 붙이면 된다.
