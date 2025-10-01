## 📌 개요

- Radix UI에서 제공하는 `Slot`은 **부모 컴포넌트의 props/속성을 자식 엘리먼트에 합성해서 전달**하는 도우미.
- `asChild` 옵션과 함께 자주 쓰임.
- 목적: Radix의 접근성/이벤트 로직을 유지하면서, **내가 만든 컴포넌트(Button 등)를 그대로 사용**할 수 있게 함.

---

## ✅ 동작 방식

1. 부모 컴포넌트가 `asChild`를 true로 주면, 내부에서 `<Slot>`을 렌더링한다.
2. `<Slot>`은 자식 엘리먼트를 받아서:
    
    ```tsx
    React.cloneElement(child, {
      ...child.props,
      ...parentProps, // Radix가 넣어야 할 onClick, aria-*, data-* 속성
    })
    
    ```
    
    이런 식으로 props를 합쳐서 반환한다.
    
3. 최종 DOM에는 자식 엘리먼트만 렌더링되며, 필요한 속성은 모두 합쳐져 있다.

---

## 📝 예시

### Slot 없이

```tsx
<DialogTrigger>
  <Button>열기</Button>
</DialogTrigger>

```

→ DOM:

```html
<button class="radix-trigger">
  <button class="my-button">열기</button>
</button>

```

❌ 잘못된 `<button>` 중첩 발생

---

### Slot 사용 (asChild)

```tsx
<DialogTrigger asChild>
  <Button>열기</Button>
</DialogTrigger>

```

→ DOM:

```html
<button class="my-button radix-trigger-props">열기</button>

```

✅ 중첩 없음 + Radix props 합성

---

## ⚡ 장점

- **접근성 보장**: Radix가 제공하는 `aria-*`, `data-*` 속성 흡수 가능
- **디자인 시스템 통합**: 내 Button/Link 컴포넌트를 Radix와 쉽게 결합
- **중복 태그 방지**: 잘못된 `<button><button/></button>` 같은 구조 피할 수 있음

---

## 💡 정리

- Radix의 `Slot`은 **props 합성기 + DOM 중첩 방지 장치**
- `asChild`와 세트로 쓰이며, Radix 로직과 내 컴포넌트를 깔끔하게 연결해준다.
- shadcn/ui에서 Button 등 대부분의 컴포넌트가 `asChild` 옵션을 제공하는 이유가 바로 이것
