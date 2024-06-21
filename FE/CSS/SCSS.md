# SCSS?

SCSS(Sassy CSS)는 Sass(Syntactically Awesome Stylesheets)의 한 문법. Sass는 CSS 전처리기(preprocessor)로, CSS의 기능을 확장하여 더욱 효율적이고 관리하기 쉬운 스타일시트를 작성할 수 있도록 도와줌. SCSS는 CSS와 매우 유사한 문법을 사용하며, 기존 CSS 파일과의 호환성이 뛰어남.

# 주요 문법

- 변수
- Nesting
- @extend
- Mixins
- Responsive Mixins

# 설치

SCSS는 SASS의 문법 중 하나이므로, SCSS 파일을 컴파일하려면 SASS 컴파일러가 필요함. 이를 통해 SCSS와 SASS 두 가지 문법을 모두 컴파일할 수 있음.

```json
npm -D SASS 
```

# 예제

### 변수(Variables)

```scss
$primary-color: #333;

body {
  color: $primary-color;
}

```

### nesting

```scss
ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  gap: 10px;
  li {
    background-color: tomato;
    color: white;
    padding: 5px 10px;
    border-radius: 7px;
    &:hover {
      opacity: 0.8;
      a {
        color: gray;
      }
    }

    a {
      text-decoration: none;
      color: white;
      text-transform: uppercase;
    }
  }
}
```

### @extend

```scss

%alert {
  margin: 10px;
  padding: 10px 20px;
  border-radius: 10px;
  border: 1px dashed black;
}

.success {
  @extend %alert;
  background-color: green;
}

.error {
  @extend %alert;
  background-color: tomato;
}

.warning {
  @extend %alert;
  background-color: yellow;
}
```

### Mixins

```scss
@mixin alert($bgColor, $borderColor) {
  background-color: $bgColor;
  margin: 10px;
  padding: 10px 20px;
  border-radius: 10px;
  border: 1px dashed $borderColor;
}

.success {
  @include alert(green, blue);
}

.error {
  @include alert(tomato, white);
}

.warning {
  @include alert(yellow, black);
}
```

### **Responsive Mixins**

```scss
$breakpoint-sm: 480px;
$breakpoint-md: 768px;
$breakpoint-lg: 1024px;
$breakpoint-xl: 1200px;

@mixin smallDevice {
  @media screen and (min-width: $breakpoint-sm) {
    @content;
  }
}

@mixin mediumDevice {
  @media screen and (min-width: $breakpoint-md) {
    @content;
  }
}

@mixin largeDevice {
  @media screen and (min-width: $breakpoint-lg) {
    @content;
  }
}

@mixin xlDevice {
  @media screen and (min-width: $breakpoint-xl) {
    @content;
  }
}

body {
  @include smallDevice {
    background-color: blue;
  }

  @include mediumDevice {
    background-color: red;
  }

  @include largeDevice {
    background-color: purple;
  }

  @include xlDevice {
    background-color: pink;
  }
}
```
