`Promise.race()`는 여러 개의 Promise 중 **가장 먼저 완료된 Promise**의 결과를 반환하는 메서드이다. 말 그대로 "race"(경주)처럼, Promise들이 동시에 실행되지만, 그중 **가장 빨리 처리된 하나의 Promise**만 반환하고 나머지 Promise들은 무시된다.

### **동작 방식**

- `Promise.race()`는 배열이나 **이터러블(iterable)** 객체를 인수로 받아, 그중 **가장 먼저 이행되거나 거부된** Promise의 결과를 반환한다.
- 먼저 완료된 Promise가 **이행(fulfilled)**되면 그 값을 반환하고, **거부(rejected)**되면 그 오류를 반환한다.

### **기본 문법**

```jsx

Promise.race([promise1, promise2, promise3])
  .then((result) => {
    console.log(result); // 가장 먼저 완료된 Promise의 결과 출력
  })
  .catch((error) => {
    console.error(error); // 가장 먼저 실패한 Promise의 에러 출력
  });

```

### **예시: Promise.race() 사용**

다음은 3개의 Promise 중 가장 빨리 완료된 Promise의 결과만 반환하는 예시.

```jsx
javascript
코드 복사
const promise1 = new Promise((resolve) => setTimeout(() => resolve('첫 번째 완료'), 3000));
const promise2 = new Promise((resolve) => setTimeout(() => resolve('두 번째 완료'), 1000));
const promise3 = new Promise((resolve) => setTimeout(() => resolve('세 번째 완료'), 2000));

Promise.race([promise1, promise2, promise3])
  .then((result) => {
    console.log(result);  // '두 번째 완료' 출력
  })
  .catch((error) => {
    console.error('오류 발생:', error);
  });

```

이 예시에서:

- `promise2`가 1초 후에 먼저 완료되기 때문에 `Promise.race()`는 **'두 번째 완료'**를 출력한다.
- `promise1`과 `promise3`는 완료되더라도 무시된다.

### **실패 처리**

Promise 중 **가장 먼저 실패한** Promise가 있으면 그 에러가 반환된다.

```jsx

const promise1 = new Promise((resolve) => setTimeout(() => resolve('첫 번째 완료'), 3000));
const promise2 = new Promise((resolve, reject) => setTimeout(() => reject('두 번째 실패'), 1000));
const promise3 = new Promise((resolve) => setTimeout(() => resolve('세 번째 완료'), 2000));

Promise.race([promise1, promise2, promise3])
  .then((result) => {
    console.log(result);
  })
  .catch((error) => {
    console.error('오류 발생:', error);  // '두 번째 실패' 출력
  });

```

이 경우 `promise2`가 1초 후에 **실패**했기 때문에 `Promise.race()`는 즉시 그 **에러**를 반환하고 **'두 번째 실패'**가 출력된다.

```jsx
// 어떨때 사용하면 좋을까요?

// 1.
// 특정 process가 허용시간보다 걸릴때 
interface File {
  name:string;
  size:number;
}

function getFile(name:string):Promise<File> {
   return delay(1000,{name, body:'...',size:100});
}

export async function main(){
   const result:stirng|File = await Promise.race([
      await getFile('file1.png');
      delay(4000,'timeout');
   ]);
   
   if(result === 'timeout'){
     console.log('네트워크 환경을 확인해주세요.');
   }else {
     console.log(result);
   }
}

```
