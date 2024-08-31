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
