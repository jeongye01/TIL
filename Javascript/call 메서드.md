```jsx
Function.prototype.call(thisArg[, arg1[,arg2[, ...]]])
```

**call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령**이다. 이때 call 메서드의 첫번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다. 

함수를 그냥 실행하면 this는 전역 객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다. 

```jsx
var func = function (a,b,c) {
  console.log(this,a,b,c);
}

func(1,2,3); // Window{...} 1 2 3
func.call({x:1}, 4 ,5 ,6); // { x:1 } 4 5 6
```

객체의 메서드를 그냥 호출하면 this는 객체를 참조하지만 call 메서드를 이용하면 임의의 객체를 this로 지정할 수 있다. 

```jsx
var obj = {
    a : 1,
    method: function(x,y){
       console.log(this.a,x,y);
    }
};

obj.method(2,3); // 1 2 3
obj.method.call({a:4}, 5, 6); // 4 5 6
```
