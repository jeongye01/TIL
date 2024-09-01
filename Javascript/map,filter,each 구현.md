```jsx
var users = {
   {id:1,name:'ID',age:36},
   {id:2,name:'AS',age:32},
   {id:3,name:'DF',age:32},
   {id:4,name:'ER',age:27},
   {id:5,name:'FG',age:24},
   {id:6,name:'WE',age:35},
   {id:7,name:'TH',age:23},
   {id:8,name:'TT',age:54},
}

// 1. 명령형 코드
  // 1. 30세 이상인 users를 거른다.
var temp_users = [];
for(var i = 0; i < users.length; i++){
  if(users[i].age >= 30){
    temp_users.push(users[i]);
  }
}
  // 2. 30세 이상인 users의 names를 수집한다.
var names = [];
for(var i =0; i<temp_users.length;i++){
  names.push(temp_users[i].name);
}
  // 3. 30세 미만인 users를 거른다.
var temp_users = [];
for(var i = 0; i < users.length; i++){
  if(users[i].age < 30){
    temp_users.push(users[i]);
  }
}
  // 4. 30세 미만인 users의 ages를 수집한다.
var ages = [];
for(var i =0; i<temp_users.length;i++){
  names.push(temp_users[i].age);
}
// 2. _filter, _map으로 리팩토링
function _filter(arr, predi) { // 응용형 함수 , 고차 함수
   var new_list = [];
   for(var i = 0; i < arr.length; i++){
     if(predi(arr[i])){
       new_list.push(arr[i]);
   }
   return new_list;
}
console.log(_filter(users,function(user){return user.age >= 30}))  

function _map(list,mapper){
   var new_list = [];
   for(var i = 0; i < arr.length; i++) new_list.push(mapper(arr[i]));
   return new_list;
}

// 3. each 만들기 

function _each(list,iter) {
   for(var i = 0; i < arr.length; i++){
       iter(list[i]);
   }
   return list;
}

function _filter(arr, predi) { // 응용형 함수 , 고차 함수
   var new_list = [];
   _each(arr,function(val){
  
     if(predi(var)){
       new_list.push(var);
     }
   })
   return new_list;
}

function _map(list,mapper){
   var new_list = [];
   _each(list, function(val){
      new_list.push(mapper(var))
   });
   return new_list;
}
```


## 다형성

map,filter 와 같은 함수들은 이미 자바스크립트에 있음. 그러나 이 함수들은 함수가 아니라 method임.

array가 아니면 사용할 수 없음. 즉 array-like 객체(ex. document.querySelectorAll)는 사용할수 없음. 

그러나 위에 구현한 _*filter,_map은 array-like 객체도 사용할수 있음*

그래서 보다 다형성이 높게 프로그래밍 할 수 있음. 

### 보조함수

predi(어떤 조건을 return),iter(반복적으로 돌면서 뭔가를 함),mapper(무언가 사이를 매핑)는 보조함수임. 함수형 프로그래밍에서는 두 번째 함수가 어떤 역할을 하는 함수인지에 따라 다양한 이름을 가질 수 있음. 보조함수에 역할을 위임하기 때문에 데이터형에 있어서 굉장히 자유롭고 다형성을 높이는데 유리함.
