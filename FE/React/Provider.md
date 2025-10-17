## Provider란?

> React에서 하위 컴포넌트 트리에 데이터를 전역적으로 전달하는 컴포넌트.
> 
- Context API의 `Context.Provider`에서 유래
- props를 거치지 않고 데이터를 트리 하위로 “공급(provide)”함
- 전역 상태 / 테마 / 언어 / 인증 / 세션 등에서 광범위하게 사용됨

```tsx
<MyContext.Provider value={{ theme: "dark" }}>
  <App />
</MyContext.Provider>

```
