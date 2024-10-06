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

export function delay<T>(time:number,value:T):Promise<T> {
   return new Promise((resolve)=>setTimeout(()=>resolve(value),time));
}


interface File {
  name:string;
  size:number;
}

function getFile(name:string):Promise<File> {
   return delay(1000,{name, body:'...',size:100});
}

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
function* map<A,B>(f:(a:A)=>B,iterable:Iterable<A>):IterableIterator<B>{
   for(const a:A of iterable){
      yield f(a);
   }
}


async function concurrent2<T>(limit:number,fs:(()=>Promise<T>[])){
	  const result = 
	    await Array.fromAsync(
	       map(ps=>Promise.all(ps),
	       map(fs=>fs.map(f=>f()),chunk(limit,fs))
	     ))
	  return result.flat();
}


export async function main(){
    const files = await concurrent2(3,[
      ()=>getFile('file1.png'),
      ()=>getFile('file2.png'),
      ()=>getFile('file3.png'),
      ()=>getFile('file4.png'),
      ()=>getFile('file5.png'),
      ()=>getFile('file6.png'),
      ()=>getFile('file7.png'),
    ]);
}

console.log(files)

```
