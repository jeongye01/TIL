# 제너레이터 사용예시
```
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
