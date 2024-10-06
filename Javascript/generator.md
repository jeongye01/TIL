# 제너레이터 사용예시


generator는 iterator을 반환한다. 

iterator는 iterable 하므로 기본적으로 next()를 통해 순회할수있으며 전개 연산자로 사용할수도 있다.

```jsx
function* gen(){
   for(let i=0;i<4;i++){
     yield i;
   }
}

const iterator = gen();
console.log([...gen()]);

```

```tsx

function* take(length:number,iterable:Iterable<T>){
   const iterator = iterable[Symbol.iterator]();
   while(length-- > 0){
     const { value, done } = iterator.next();
     if(done) break;
     yield value;
   }
  
   

}
function* chunk(size:number,iterable:Iterable<T>){
  const iterator = iterable[Symbol.iterator()];
  while(true){
     const arr = yield [...take(size,{[Sysbol.iterator](){return iterator;}})];// 이터러블임을 보장
     if(arr.length) yield arr;
     if(arr.length<size)break;
  }
  
}



export async function main(){
    const iterator = take(2,[1,2,3,4,5,6,7]);
    chunk(3,[1,2,3,4,5,6]);
    // [[1,2,3],[4,5,6]]
}

```
