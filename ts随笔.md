# 方法参数的默认值

## 非对象参数默认值
```typescript
function fun(name: string, age: number = 10): string {
  return `${name} -- ${age}`;
}
console.log(fun("张三"));//张三 -- 10
console.log(fun("张三",15));//张三 -- 15
```

## 对象参数默认值
```typescript
function fun(para?: { name: string; age?: number }) {
  let { name="佚名", age = 10 } = para || {};
  return `${name} -- ${age}`;
}
console.log(fun({ name: "张三" }));//张三 -- 10
console.log(fun());//佚名 -- 10
console.log(fun({ name: "张三", age: 15 }));//张三 -- 15
```


# 方法的重载
> JavaScript本身是个动态语言。 JavaScript里函数根据传入不同的参数而返回不同类型的数据是很常见的。
``` typescript
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}
```

# 接口
>interface 接口其实就是提供一个规范，让你必须按照规范来定义方法，定义变量

```typescript

/**
* 这里就是强制规定了fun当为方法的时候，需要传入什么值，返回什么值
* 这里名字可以不同 比如 key 可以是key2  但是位置必须相同
*/
interface Fun {
  (key: number, name: string): string;
}

let fun: Fun = function(key: number, name: string): string {
  return key+name;
};



/**
* 这里就是强制规定了必须使用number 作为索引值，string类型为value内容。
* 这里可以有多种实现方式，可以是对象，也可以是数组
*/

interface ArrList {
  [key: number]: string;
}

let arr: ArrList = ["0", "1", "2"];

let arrObj: ArrList = {
  1: "0",
  2: "1"
};

```


# 泛型

``` typescript
interface ConfigFun{
  <T>(name:T):T
}

let fun:ConfigFun = function<T>(name:T){
  return name
}

fun<string>("张三"); 
```