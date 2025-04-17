# Portal
## 🎯 Portal을 사용하는 이유
Portal은 부모 레이아웃(overflow, position, z-index 등) 의 제약을 완전히 무시하고,
내가 원하는 위치에 컴포넌트를 자유롭게 띄우기 위해 사용하는 기술이다.

예를 들어보자.

### ❌ Portal 없이 렌더링하는 경우
```
<div style={{ overflow: hidden }}>
  <Dropdown />
</div>
```
부모 요소가 overflow: hidden이면 드롭다운이 부모를 벗어나면서 잘려서 보이지 않는다.

아무리 드롭다운을 펼치려 해도, 부모 레이아웃에 갇혀버리는 문제가 생긴다.

### ✅ Portal을 사용하면
드롭다운이 body 하위에 렌더링된다.

부모의 overflow, position, z-index 같은 레이아웃 속성을 전혀 신경 쓰지 않고 화면 최상단에 자유롭게 펼칠 수 있다.

### 🚀 Portal이 필요한 대표적인 UI 컴포넌트

컴포넌트	Portal 필요 이유
드롭다운 (Dropdown)	펼칠 때 부모 때문에 잘리는 걸 방지
모달 (Modal)	전체 화면을 덮기 위해 필요
토스트 알림 (Toast)	항상 화면 최상단에 띄우기 위해
툴팁 (Tooltip)	부모 스크롤이나 레이아웃과 무관하게 정확한 위치에 띄우기 위해

### ✅ Portal을 사용하는 이유 정리

- 부모 레이아웃 제약 탈출	overflow: hidden, position: relative, z-index에 영향받지 않음
- 자유로운 화면 배치	스크롤, 트랜스폼(transform) 등의 레이아웃 속성 영향을 무시할 수 있음
- 일관된 레이어 관리	모든 레이어 컴포넌트를 body 하위로 모아 깔끔하게 관리 가능
### ❗ Portal을 쓰지 않으면 생기는 문제들

### 문제	예시
- Dropdown이 잘려서 안 보임	부모가 overflow: hidden이면 드롭다운이 잘림
- 모달이 부모 영역에 갇힘	화면 전체를 덮지 못하고 부모 안에 제한
- z-index 충돌	부모보다 낮은 z-index 때문에 뒤로 가려짐
- 포지션 계산 오류	부모에 transform: scale() 등이 걸려 있으면 위치 어긋남
### ✨ 한 줄 요약
Portal은 "부모 레이아웃 제약 없이 자유롭게 레이어를 띄우기 위한 기술"이다.

### 🔥 추가로
Radix UI 같은 라이브러리들은 기본적으로 Portal을 적용해서 이런 문제를 미리 방지한다.

필요하다면 Portal을 끄고 직접 렌더링하는 것도 가능하지만, 특별한 이유가 없는 한 Portal을 사용하는 것이 안정적이다.

