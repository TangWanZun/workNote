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