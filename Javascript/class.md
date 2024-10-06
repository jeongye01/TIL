

# class 사용예시
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

class FxIterator<T> {
  constructor(public iterable: Iterable<T>){}
  
  chunk(size:number){
    return fx(chunk(size,this.iterable));
  }
  map<U>(f:(a:T)=>U):FxIterator<U>{
    return fx(map(f,this.iterable));
  }
  to<U>(f:(iterable: Iterable<T>)=>U):U{
     return f(this.iterable);
  }

}

function fx<T>(iterable:Iterable<T>){
   return new FxIterator(iterable);
}

async function concurrent3<T>(limit:number,fs:(()=>Promise<T>[])){
	  return fx(fs)
	    .chunk(limit)
	    .map(fs=>fs.map(f=>f()))
	    .map(ps=>Promise.all(ps))
	    .to(fromAsync)
	    .then(arr=>arr.flat());
}

export async function main(){
    const files = await concurrent3(3,[
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
