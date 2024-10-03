https://developer.mozilla.org/ko/docs/Web/API/EventTarget/dispatchEvent

### dispatchEvent와 addEventListener의 차이

`dispatchEvent`는 특정 요소에 **이벤트를 수동으로 발생시키는** 기능을 담당한다. 즉, 코드 상에서 이벤트를 직접 발생시켜야 할 때 사용하는 함수입니다.

- **사용 예시**:
    
    ```jsx
    
    const button = document.getElementById('myButton');
    
    // 이벤트 핸들러 등록
    button.addEventListener('click', function(event) {
      console.log('Button clicked!');
    });
    
    // 수동으로 click 이벤트를 발생시킴
    const event = new Event('click');
    button.dispatchEvent(event);
    
    ```
    
    위 코드는 사용자가 버튼을 클릭하지 않아도, 코드에서 `dispatchEvent`를 사용하여 `'click'` 이벤트를 강제로 발생시키는 예다.
    
- **기능**:
    - 지정된 요소에 대해 특정 이벤트를 **수동으로 트리거(trigger)** 할 수 있다.
    - 사용자가 실제로 행동(예: 클릭, 키보드 입력)을 하지 않아도, 그 행동을 흉내 내는 이벤트를 발생시킬 수 있다.
    - 이때 발생된 이벤트는 `addEventListener`에 등록된 이벤트 핸들러가 감지하여 실행된다.

### **핵심 차이점**

- `addEventListener`는 **이벤트 핸들러를 등록**하여 특정 이벤트가 발생했을 때 반응할 함수를 정의하는 역할을 한다.
- `dispatchEvent`는 **이벤트를 수동으로 발생**시키는 함수로, 코드 상에서 직접 이벤트를 트리거할 수 있다. 이를 통해 이벤트 핸들러가 호출되도록 만들 수 있다.

# 그냥 button.click() 이렇게 하는것과의 차이

### 1. **`button.click()`**

- DOM에서 제공하는 기본 함수로, 해당 요소에 대해 **프로그램적으로 클릭 이벤트를 발생**시킨다. 즉, 사용자가 실제로 버튼을 클릭한 것처럼 동작한다.
- `<input type="submit">` 버튼의 경우 `click()`을 호출하면 폼이 제출됨.
- `<a>` 태그에서 `click()`을 호출하면 링크가 실제로 이동됨.
- **예시**:
    
    ```jsx
    
    const button = document.getElementById('myButton');
    button.click();  // 마치 사용자가 버튼을 클릭한 것처럼 동작
    
    ```
    

### 2. **`dispatchEvent(new Event('click'))`**

- 이벤트 핸들러는 호출되지만, 브라우저의 기본 동작은 발생하지 않음.
- 이벤트 객체를 사용자 정의할 수 있어, 커스텀 이벤트를 생성하고 처리할 때 유용함.
- **예시**:
    
    ```jsx
    
    const button = document.getElementById('myButton');
    const event = new Event('click');
    button.dispatchEvent(event);  // click 이벤트 발생, 하지만 기본 브라우저 동작은 없음
    
    ```
