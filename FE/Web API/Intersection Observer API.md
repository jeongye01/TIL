https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API

**Intersection Observer API**는 웹 페이지에서 요소(Element)가 뷰포트(Viewport)나 다른 요소와 교차할 때, 즉 화면에 나타나거나 사라질 때 그 변화를 감지하고 반응할 수 있게 해주는 JavaScript API입니다. 이 API를 사용하면 스크롤링이나 레이아웃 변화와 같은 이벤트를 처리하는 데 도움을 주며, 주로 다음과 같은 용도로 사용됩니다.

### 주된 용도:

1. **Lazy Loading (지연 로딩)**:
    - 이미지를 사용자 화면에 나타날 때만 로딩하도록 하여, 페이지의 성능을 향상시킵니다.
2. **무한 스크롤 (Infinite Scroll)**:
    - 사용자가 스크롤할 때 추가 콘텐츠를 불러오는 데 사용됩니다.
3. **요소의 가시성 감지**:
    - 특정 요소가 화면에 얼마나 보이는지를 감지하여 애니메이션을 트리거하거나, 분석 데이터를 수집할 수 있습니다.
4. **광고 또는 배너 노출 확인**:
    - 광고나 배너가 사용자의 뷰포트에 표시되었는지 감지하여 광고 노출 여부를 트래킹할 수 있습니다.

### 주요 개념:

- **IntersectionObserver** 객체를 생성하여 감시할 대상 요소(타겟)를 설정하고, 그 요소가 화면이나 다른 요소와 교차할 때마다 콜백을 실행합니다.

### 코드 예시:

```jsx

let options = {
  root: null, // 뷰포트가 기준
  rootMargin: "0px",
  threshold: 0.1 // 요소가 10% 이상 보일 때 콜백 호출
};

let observer = new IntersectionObserver((entries, observer) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      console.log('요소가 보이기 시작했습니다.');
      // 원하는 작업 실행
    }
  });
}, options);

// 감시할 요소 선택
let target = document.querySelector('.target-element');
observer.observe(target);

```

### 장점:

- **성능 최적화**: **스크롤 이벤트를 직접 사용하는 것보다 효율적입니다.** Intersection Observer는 브라우저가 효율적으로 처리하여 성능에 미치는 영향이 적습니다.
- **간결함**: 복잡한 뷰포트 계산이나 스크롤 이벤트 핸들링이 필요하지 않으며, 간단하게 요소의 가시성을 추적할 수 있습니다.

이 API는 특히 성능에 민감한 웹 애플리케이션에서 자주 사용됩니다.
