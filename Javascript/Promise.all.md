`Promise.all()`은 **여러 개의 Promise**를 동시에 실행하고, **모든 Promise가 성공적으로 완료될 때** 하나의 새로운 Promise를 반환하는 메서드이다. 주로 **병렬**로 비동기 작업을 처리하고, 모든 작업이 완료되었을 때 후속 작업을 처리할 때 사용된다.

### **동작 방식**

- `Promise.all()`은 **배열**이나 **이터러블(iterable)** 객체를 인수로 받으며, 배열 안의 모든 Promise가 **이행(fulfilled)**되면 결과 값들의 배열을 반환한다.
- 만약 하나의 Promise라도 **거부(rejected)**되면, 전체가 거부된 것으로 간주하고 첫 번째로 거부된 Promise의 **에러를 반환한다**.

### **기본 문법**

```jsx

Promise.all([promise1, promise2, promise3])
  .then((results) => {
    // 모든 promise가 성공적으로 완료된 후 실행
    console.log(results); // 결과는 [result1, result2, result3] 형태로 반환됨
  })
  .catch((error) => {
    // 하나라도 실패한 경우 이 블록이 실행됨
    console.error(error);
  });

```

### **예시: Promise.all() 사용**

```jsx

const promise1 = new Promise((resolve) => setTimeout(() => resolve('첫 번째 작업 완료'), 1000));
const promise2 = new Promise((resolve) => setTimeout(() => resolve('두 번째 작업 완료'), 2000));
const promise3 = new Promise((resolve) => setTimeout(() => resolve('세 번째 작업 완료'), 3000));

Promise.all([promise1, promise2, promise3])
  .then((results) => {
    console.log(results);  // ['첫 번째 작업 완료', '두 번째 작업 완료', '세 번째 작업 완료']
  })
  .catch((error) => {
    console.error('오류 발생:', error);
  });

```

반환하는 프로미스의 이행 값은 매개변수로 주어진 프로미스의 순서와 일치하며, 완료 순서에 영향을 받지 않는다.

### **실패 처리**

Promise 중 하나라도 실패하면 전체가 실패 처리된다.

```jsx
javascript
코드 복사
const promise1 = new Promise((resolve) => setTimeout(() => resolve('첫 번째 작업 완료'), 1000));
const promise2 = new Promise((resolve, reject) => setTimeout(() => reject('두 번째 작업 실패'), 2000));
const promise3 = new Promise((resolve) => setTimeout(() => resolve('세 번째 작업 완료'), 3000));

Promise.all([promise1, promise2, promise3])
  .then((results) => {
    console.log(results);
  })
  .catch((error) => {
    console.error('오류 발생:', error);  // '두 번째 작업 실패' 출력
  });

```


// Promise.all를 사용해 보신적이 있으신가요?
// 어떨때 사용하면 좋을까요?


interface File {
  name:string;
  size:number;
}

function getFile(name:string):Promise<File> {
   return delay(1000,{name, body:'...',size:100});
}


// api limit가 있을때 사용할 함수(부하 조절)
async function concurrent(limit:number,fs:(()=>Promise<T>[])){
   const result:T[][] = [];
   for(let i=0;i<fs.length/limit ; i++){
      const tmp:Promise<T>[] = [];
      for(let j=0;j<limit;j++){
         const f=fs[i*limit+j];
         if(f){//undefined error 방지,런타임에서 터지는 에러는 타입스크립트도 막을 수 없음
            tmp.push(f());
         }
      }
      result.push(await Promise.all(tmp));
      
   }
   return result.flat();
}


# 사용 case

```jsx
// Promise all을 사용해보신적 있으신가요?

export function delay<T>(time:number,value:T):Promise<T> {
   return new Promise((resolve)=>setTimeout(()=>resolve(value),time));
}

export async function main() {
   console.log(await delay(1000,10));
   console.log(await delay(1000,20));
   console.log(await delay(1000,30));
}

// Promise.all를 사용해 보신적이 있으신가요?
// 어떨때 사용하면 좋을까요?

interface File {
  name:string;
  size:number;
}

function getFile(name:string):Promise<File> {
   return delay(1000,{name, body:'...',size:100});
}

// api limit가 있을때 사용할 함수(부하 조절)
async function concurrent(limit:number,fs:(()=>Promise<T>[])){
   const result:T[][] = [];
   for(let i=0;i<fs.length/limit ; i++){
      const tmp:Promise<T>[] = [];
      for(let j=0;j<limit;j++){
         const f=fs[i*limit+j];
         if(f){//undefined error 방지,런타임에서 터지는 에러는 타입스크립트도 막을 수 없음
            tmp.push(f());
         }
      }
      result.push(await Promise.all(tmp));
      
   }
   return result.flat();
}

export async function main(){
   // 잘못된 코드
   console.time();
   const files = await concurrent(3,[
      getFile('file1.png'), //<- promise가 여기서 다 실행이 되버림
      getFile('file2.pdf'),
      getFile('file3.png'),
   ]);
   console.timeEnd();
   // 고친 코드 -> 함수를 보냄 
   console.time();
   const files = await concurrent(3,[
      ()=>getFile('file1.png'), 
      ()=>getFile('file2.pdf'),
      ()=>getFile('file3.png'),
   ]);
   console.timeEnd();
   
   
   console.log(files)
}

```

