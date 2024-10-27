### 특징

- zustand는 **경량화**된 상태관리 라이브러리이다. 사내 프로젝트 빌드시 번들 사이즈의 약 0.07 % 정도 밖에 차지 하지 않았다. (mobx의 경우 2.6%를 차지했다. - 같은 기능을 구현했음)
- **선택적 구독**: 성능 최적화를 위해 필요한 컴포넌트만 리렌더링
- **비동기 로직 지원**: 비동기 상태 업데이트가 매우 간단하게 처리
- **확장성**: 로컬 스토리지 연동, Redux 개발자 도구 지원 등 다양한 기능을 쉽게 확장할 수 있음
- **이미 React 생태계에 자리 잡은 라이브러리 :** (but, react와 무관하게 사용할 수 있다)
- **간결한 API를 제공한다**
    
    ```jsx
    // 상태를 생성하고 업데이트 하는 코드가 간단함.
    import { create } from 'zustand'
    
    const useStore = create((set) => ({
      count: 1,
      inc: () => set((state) => ({ count: state.count + 1 })),
    }))
    
    function Counter() {
      const { count, inc } = useStore()
      return (
        <div>
          <span>{count}</span>
          <button onClick={inc}>one up</button>
        </div>
      )
    }
    ```
    
    - 같은 기능을 하는 redux 코드
        
        ### 1. **리듀서 및 액션 정의**
        
        ```jsx
        
        // actions.js
        export const INCREMENT = 'INCREMENT';
        
        export const increment = () => ({
          type: INCREMENT,
        });
        
        // reducer.js
        import { INCREMENT } from './actions';
        
        const initialState = {
          count: 1,
        };
        
        const counterReducer = (state = initialState, action) => {
          switch (action.type) {
            case INCREMENT:
              return {
                ...state,
                count: state.count + 1,
              };
            default:
              return state;
          }
        };
        
        export default counterReducer;
        
        ```
        
        ### 2. **Redux 스토어 생성**
        
        ```jsx
        
        // store.js
        import { createStore } from 'redux';
        import counterReducer from './reducer';
        
        const store = createStore(counterReducer);
        
        export default store;
        
        ```
        
        ### 3. **React 컴포넌트에서 Redux 사용**
        
        ```jsx
        
        // Counter.js
        import React from 'react';
        import { useSelector, useDispatch } from 'react-redux';
        import { increment } from './actions';
        
        function Counter() {
          const count = useSelector((state) => state.count);
          const dispatch = useDispatch();
        
          const handleIncrement = () => {
            dispatch(increment());
          };
        
          return (
            <div>
              <span>{count}</span>
              <button onClick={handleIncrement}>one up</button>
            </div>
          );
        }
        
        export default Counter;
        
        ```
        
        ### 4. **Redux Provider 설정**
        
        ```jsx
        
        // App.js
        import React from 'react';
        import { Provider } from 'react-redux';
        import store from './store';
        import Counter from './Counter';
        
        function App() {
          return (
            <Provider store={store}>
              <Counter />
            </Provider>
          );
        }
        
        export default App;
        
        ```
        
    

### Context API vs Zustand

zustand를 채택하는 이유가 보통 가볍고 사용하기 간단하다는 점인데, 이런 이유라면 전역상태를 관리함에 있어 context api를 선택하는게 낫지 않을까란 생각이 들 수 있다.

그러나 리렌더링 측면에서 이 두 가지는 차이가 있다.

- **Zustand**:
    - Zustand는 상태의 **선택적 구독** 기능을 제공한다.  특정 상태나 상태의 일부분에만 반응할 수 있어 불필요한 리렌더링을 최소화할 수 있다.
- **Context API**:
    - Context API는 상태 변경이 발생하면 **Context를 사용하는 모든 하위 컴포넌트가 리렌더링된다.** 즉, 상위 컴포넌트가 제공하는 상태가 변경될 때, 하위 컴포넌트 전체가 리렌더링될 수 있어 **성능 문제**가 발생할 수 있다.
- 자세히 보기
    
    ### 예시:
    
    ```jsx
    import create from 'zustand';
    
    // Zustand store 생성
    const useStore = create((set) => ({
      name: 'John',
      age: 30,
      updateName: (newName) => set({ name: newName }),
      updateAge: (newAge) => set({ age: newAge }),
    }));
    
    // 이름만 구독하는 컴포넌트
    function NameComponent() {
      const name = useStore((state) => state.name);  // 이름만 구독
      return <div>Name: {name}</div>;
    }
    
    // 나이만 구독하는 컴포넌트
    function AgeComponent() {
      const age = useStore((state) => state.age);  // 나이만 구독
      return <div>Age: {age}</div>;
    }
    
    ```
    
    위 코드에서:
    
    - `NameComponent`는 Zustand 상태에서 `name`만 구독하고 있다.
    - `AgeComponent`는 상태에서 `age`만 구독하고 있다.
    
    이제 `name`이 업데이트될 때 `NameComponent`만 리렌더링되고, `age`가 업데이트될 때 `AgeComponent`만 리렌더링된다. 상태 전체가 아닌 필요한 부분만 구독하기 때문에, 불필요한 컴포넌트의 리렌더링을 방지할 수 있다.
    
    ### `Context API`로 작성한 예시:
    
    ```jsx
    
    import React, { createContext, useContext, useState } from 'react';
    
    // Context 생성
    const StoreContext = createContext();
    
    // Context Provider 컴포넌트
    function StoreProvider({ children }) {
      const [name, setName] = useState('John');
      const [age, setAge] = useState(30);
    
      const updateName = (newName) => setName(newName);
      const updateAge = (newAge) => setAge(newAge);
    
      return (
        <StoreContext.Provider value={{ name, age, updateName, updateAge }}>
          {children}
        </StoreContext.Provider>
      );
    }
    
    // 이름만 사용하는 컴포넌트
    function NameComponent() {
      const { name } = useContext(StoreContext); // name만 구독
      return <div>Name: {name}</div>;
    }
    
    // 나이만 사용하는 컴포넌트
    function AgeComponent() {
      const { age } = useContext(StoreContext); // age만 구독
      return <div>Age: {age}</div>;
    }
    
    // 상태 업데이트를 위한 컴포넌트
    function UpdateComponent() {
      const { updateName, updateAge } = useContext(StoreContext);
      return (
        <div>
          <button onClick={() => updateName('Jane')}>Update Name</button>
          <button onClick={() => updateAge(25)}>Update Age</button>
        </div>
      );
    }
    
    // App 컴포넌트
    function App() {
      return (
        <StoreProvider>
          <NameComponent />
          <AgeComponent />
          <UpdateComponent />
        </StoreProvider>
      );
    }
    
    export default App;
    
    ```
    
    ### 차이점:
    
    - **전체 상태를 구독**: `useContext(StoreContext)`를 사용하여 필요한 상태(`name`이나 `age`)만 선택적으로 사용하는 것처럼 보이지만, `Context API`는 `StoreContext.Provider`의 값이 변경될 때 해당 `Context`를 사용하는 모든 컴포넌트가 리렌더링 된다.
        - 예를 들어, `updateName` 함수로 `name`이 변경되면 `NameComponent`뿐만 아니라 `AgeComponent`도 리렌더링 된다.
    - **Zustand와 비교**: Zustand는 상태의 일부만 구독할 수 있어 `name`이나 `age`가 변경될 때 필요한 컴포넌트만 리렌더링되지만, `Context API`는 상태 변경 시 더 많은 컴포넌트가 리렌더링 된다.
    

또한, 확장 기능이 필요한 경우 Context API는 이를 다루기 위해 복잡해질 수 있다.

- **Zustand**:
    - 비동기 로직, 상태 변경 추적 등의 고급 기능이 필요할 때는 미들웨어를 쉽게 추가할 수 있어 **복잡한 상태 관리**에도 적합하다.
- **Context API**:
    - Context API는 기본적으로 상태 전달 기능만을 제공하므로, **상태 관리가 복잡해질 경우** 확장성이 부족할 수 있다. 예를 들어, 상태를 로컬 스토리지에 저장하거나 비동기 API 호출을 관리하는 기능이 필요할 경우 추가적인 설정이 요구된다.

결론적으로, **Zustand는 번들 크기가 작고, 리렌더링 효율성이 높으며, 확장성까지 고려할 때** 전역 상태 관리에서는 **Context API**보다 더 나은 선택이 될 수 있을 것 같다.

### zustand 잘 쓰기

어떻게 하면 zustand를 잘 쓸 수 있을까?

- Zustand에서 **selectors**를 권장하는 이유는 선택적 구독 기능을 활용하여 **불필요한 리렌더링을 방지**하기 위함
