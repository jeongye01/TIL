# WeakMap과 WeakSet
 JavaScript 엔진은 값이 "접근 가능"하고 잠재적으로 사용될 수 있을 때까지 메모리에 유지합니다.

예를 들어:

```jsx

let john = { name: "John" };

// 객체는 접근 가능하며, john이 그 객체를 참조하고 있습니다.

// 참조를 덮어씁니다.
john = null;

// 객체는 메모리에서 제거됩니다.

```

일반적으로 객체의 프로퍼티나 배열의 요소 또는 다른 데이터 구조의 요소는 해당 데이터 구조가 메모리에 있는 동안 메모리에 유지됩니다.

예를 들어, 객체를 배열에 넣으면 배열이 살아있는 동안 그 객체도 살아있게 됩니다. 다른 참조가 없어도 말이죠.

예:

```jsx

let john = { name: "John" };

let array = [ john ];

john = null; // 참조를 덮어씀

// 이전에 john이 참조했던 객체는 배열에 저장되어 있습니다.
// 따라서 가비지 컬렉션되지 않습니다.
// array[0]을 통해 접근 가능합니다.

```

이와 유사하게, 객체를 일반 Map의 키로 사용하면 Map이 존재하는 한 그 객체도 존재합니다. 이는 메모리를 차지하고 가비지 컬렉션되지 않을 수 있습니다.

예:

```jsx

let john = { name: "John" };

let map = new Map();
map.set(john, "...");

john = null; // 참조를 덮어씀

// john은 map 내부에 저장되어 있으며,
// map.keys()를 사용해 접근할 수 있습니다.

```

WeakMap은 이 점에서 근본적으로 다릅니다. WeakMap은 키 객체의 가비지 컬렉션을 방지하지 않습니다.

예시로 그 차이를 살펴보겠습니다.

### WeakMap

Map과 WeakMap의 첫 번째 차이점은 키가 반드시 객체여야 한다는 점입니다. 원시 값은 사용할 수 없습니다.

```jsx

let weakMap = new WeakMap();

let obj = {};

weakMap.set(obj, "ok"); // 객체 키로 작동함

// 문자열을 키로 사용할 수 없음
weakMap.set("test", "Whoops"); // 에러, "test"는 객체가 아님

```

WeakMap에서 객체를 키로 사용하고, 그 객체에 대한 다른 참조가 없으면 메모리에서 (그리고 WeakMap에서도) 자동으로 제거됩니다.

```jsx

let john = { name: "John" };

let weakMap = new WeakMap();
weakMap.set(john, "...");

john = null; // 참조를 덮어씀

// john은 메모리에서 제거됨!

```

일반 Map 예제와 비교해보면, WeakMap에서 john이 유일하게 존재하는 곳이 WeakMap의 키로 사용된 경우, 메모리에서 자동으로 삭제됩니다.

WeakMap은 반복(iteration)과 keys(), values(), entries() 메서드를 지원하지 않습니다. 따라서 모든 키나 값을 얻을 수 있는 방법이 없습니다.

WeakMap이 지원하는 메서드는 다음과 같습니다:

- `weakMap.set(key, value)`
- `weakMap.get(key)`
- `weakMap.delete(key)`
- `weakMap.has(key)`

이러한 제한은 기술적인 이유 때문입니다. 객체가 모든 참조를 잃으면 (위 예시의 john처럼) 가비지 컬렉션 대상이 되며, 정확히 언제 청소가 이루어질지는 명확하게 정의되지 않았습니다.

JavaScript 엔진이 언제 메모리 청소를 할지 결정합니다. 즉시 청소할 수도 있고, 더 많은 삭제가 일어날 때까지 기다릴 수도 있습니다. 따라서 WeakMap의 현재 요소 개수는 알 수 없습니다. 엔진이 이를 청소했는지, 부분적으로 했는지 알 수 없습니다. 이러한 이유로 모든 키/값에 접근하는 메서드는 지원되지 않습니다.

### 사용 사례: 추가 데이터

WeakMap의 주요 사용 사례는 추가 데이터 저장입니다.

어떤 객체가 다른 코드에 속하거나 제3자 라이브러리에서 제공될 때, 해당 객체가 살아있는 동안에만 존재해야 하는 데이터를 저장하고 싶다면, WeakMap이 적합한 도구입니다.

객체를 키로 사용하여 데이터를 WeakMap에 넣고, 객체가 가비지 컬렉션되면 그 데이터도 자동으로 사라집니다.

```jsx

weakMap.set(john, "비밀 문서");
// john이 사라지면, 비밀 문서도 자동으로 삭제됩니다.

```

### 사용 사례: 캐싱

또 다른 일반적인 사용 사례는 캐싱입니다. 함수의 결과를 저장(캐싱)하여 동일한 객체에 대한 이후 호출에서 이를 재사용할 수 있습니다.

```jsx

let cache = new WeakMap();

function process(obj) {
  if (!cache.has(obj)) {
    let result = /* 객체에 대한 계산 결과 */ obj;

    cache.set(obj, result);
    return result;
  }

  return cache.get(obj);
}

let obj = {/* 객체 */};

let result1 = process(obj);
let result2 = process(obj);

obj = null;

// WeakMap이므로 cache.size를 알 수 없습니다.
// 하지만 obj가 가비지 컬렉션되면, 캐시 데이터도 함께 삭제됩니다.

```

### WeakSet

WeakSet도 비슷하게 동작합니다. Set과 유사하지만 객체만 추가할 수 있습니다. 객체는 다른 곳에서 접근 가능한 동안에만 존재합니다.

```jsx

let visitedSet = new WeakSet();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

visitedSet.add(john);
visitedSet.add(pete);

alert(visitedSet.has(john)); // true
alert(visitedSet.has(mary)); // false

john = null; // 자동으로 제거됨

```


복잡한 연산을 수행하고 그 결과를 객체에 대해 캐싱할 때, `WeakMap`을 사용할 수 있습니다. 객체가 더 이상 필요하지 않으면 캐시도 자동으로 제거됩니다.

```jsx

let cache = new WeakMap();

function process(obj) {
  // 객체에 대한 캐시가 있는지 확인
  if (!cache.has(obj)) {
    // 캐시에 없는 경우, 결과를 계산하고 저장
    let result = obj.value * 2; // 예를 들어, 값을 2배로
    cache.set(obj, result);
    return result;
  }
  // 캐시에 있는 경우, 저장된 값을 반환
  return cache.get(obj);
}

let obj1 = { value: 10 };
let obj2 = { value: 20 };

console.log(process(obj1)); // 20 (계산 후 캐싱)
console.log(process(obj1)); // 20 (캐시된 값 반환)

obj1 = null; // obj1이 더 이상 필요 없을 때, 캐시에서 자동 제거

```

이 예시에서는 `WeakMap`을 통해 객체가 더 이상 필요하지 않을 때 캐시된 데이터가 자동으로 정리되므로 메모리 누수를 방지할 수 있습니다.

이처럼 `WeakMap`은 객체를 키로 사용하는 추가 데이터 저장 및 메모리 관리를 자동화할 수 있는 훌륭한 도구입니다.




WeakMap과 WeakSet의 가장 큰 제한 사항은 반복(iteration)이 없고, 현재 내용을 얻을 수 없다는 것입니다. 하지만 객체가 다른 곳에서 관리되는 동안 추가적인 데이터를 저장하는 주요 역할을 수행하는 데는 충분합니다.

### 요약

- WeakMap은 객체만을 키로 사용할 수 있는 Map과 유사한 컬렉션으로, 키 객체가 접근 불가능해지면 메모리와 함께 자동으로 삭제됩니다.
- WeakSet은 객체만을 저장하는 Set과 유사한 컬렉션으로, 객체가 접근 불가능해지면 자동으로 삭제됩니다.

이들은 객체에 대한 약한 참조를 사용하므로 가비지 컬렉터가 쉽게 제거할 수 있다는 장점이 있습니다.
