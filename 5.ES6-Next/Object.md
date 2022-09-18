### Object
> 下述的方法都跟原型链无关，所以不去考虑原型链

**Object.keys(obj)** —— 返回一个包含该对象所有的键的**字符串数组。**
**Object.values(obj)** ——成员是参数对象自身的（**不含继承的**）所有**可遍历**属性的**键值**
**Object.entries(obj)** —— 返回一个数组，成员是参数对象自身的（不含继承的）所有**可遍历**属性的**键值对数组**
**Object.fromEntries(iterable)** -- 将 key:value(可以是map或者数组) 键值对转化为一个object


```js
let obj={name:"小白",age:18,sex:"男"}
let grid=Object.entries(obj)
console.log(grid)//[["name", "小白"],["age", 18],["sex", "男"]]

let newObj = Object.fromEntries(grid);
console.log(newObj); // {"name": "小白", "age": 18, "sex": "男"}
```

一些高级用法(把对象当成数组元素操控)
```js
const object1 = { a: 1, b: 2, c: 3 };

const object2 = Object.fromEntries(
  Object.entries(object1)
  .map(([ key, val ]) => [ key, val * 2 ])
);

console.log(object2);
// { a: 2, b: 4, c: 6 }
```