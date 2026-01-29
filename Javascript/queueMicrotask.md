## 개요

Window 또는 Worker 인터페이스의 queueMicrotask() 메서드는 브라우저의 이벤트 루프로 통제권이 넘어가기 전, 
안전한 시점에 실행할 마이크로태스크를 큐에 추가한다. 
마이크로태스크란 현재 태스크가 종료된 후, 그리고 실행 맥락의 통제권이 브라우저 이벤트 루프로 넘어가기 전에,
실행해야 하는 다른 코드가 없으면 처리되는 짧은 함수이다. 

```tsx
queueMicrotask(() => {
// 현재 JS 실행이 끝난 직후 실행됨
});

```

---

## 실행 시점

JavaScript 이벤트 루프에서 실행 순서는 다음과 같다.

```
동기JavaScript 실행
→Microtaskqueue (queueMicrotask,Promise.then)
→Rendering
→Macrotaskqueue (setTimeout,setInterval)

```

즉, `queueMicrotask`는 **렌더링 전에 실행되는 가장 빠른 비동기 작업**이다.

---

## 왜 필요한가

기존의 비동기 API는 실행 시점이 너무 늦거나 모호한 경우가 많았다.

- `setTimeout(fn, 0)`
    - 다음 macrotask
    - 렌더링 이후 실행
- `requestAnimationFrame`
    - 렌더링 직전 실행
    - UI 프레임과 강하게 결합됨

`queueMicrotask`는 이 사이에서

**“지금 실행이 끝나자마자”**라는 명확한 타이밍을 제공한다.

---

## `Promise.then`과의 관계

```tsx
queueMicrotask(fn);

// 거의 동일한 동작
Promise.resolve().then(fn);

```

차이점:

- `queueMicrotask`는 의도가 명확함
- Promise 체인을 만들지 않음
- 에러 전파가 단순함

그래서 마이크로태스크를 의도적으로 예약할 때는

`queueMicrotask`가 더 읽기 좋은 선택이다.

---

## 주요 사용 사례

- 현재 실행 이후의 최소 지연 작업
- 상태 업데이트 직후 타이밍 제어
- 성능 측정 코드
- DOM 변경 직후 후처리
- 이벤트 루프 흐름 제어

---

## 주의사항

- 마이크로태스크는 렌더링보다 우선 실행된다.
- 과도하게 사용하면 렌더링이 지연될 수 있다.
- 마이크로태스크 안에서 다시 `queueMicrotask`를 호출하면
    
    **무한 루프(microtask starvation)** 위험이 있다.


## 참고
- https://developer.mozilla.org/ko/docs/Web/API/Window/queueMicrotask
