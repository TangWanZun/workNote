# 对于VUE使用死循环的测试检验报告
## Watch循环
检测对象，并在watch中改变对象本身		
如果改变对象属性的时候，是一个固定值，则不会造成死循环。
``` javascript
export default class Activity extends Vue {
	obj: any = {
		a: "123123",
		b: "123123"
	};
	@Watch("obj", {
		deep: true
	})
	objWatch(val: any) {
		console.log(val);
		val.a = val.b;
	}
}
```
这个时候，我们的代码没有发生死循环	
但是如果 a 使用了自增加，则就会发生死循环
``` javascript
val.a =+ 1;
```
另外，如果我们在watch中赋值一个对象或者数组的时候	
对象数组的地址被改变了，就会触发当前对象的更新，进而诱发死循环
``` javascript
export default class Activity extends Vue {
  obj: any = {
    arr: ["a", "b", "c"]
  };
  @Watch("obj", {
    deep: true
  })
  objWatch(val: any) {
    val.arr = ["1", "2", 3];
  }
}
```

即便是他的子属性中包含了对象或数组引用地址发生变化都会导致死循环
``` javascript
export default class Activity extends Vue {
  obj: any = {
    a: "abc",
    b: "def",
    c:{
      cc:[]
    }
  };
  @Watch("obj", {
    deep: true
  })
  objWatch(val: any) {
    console.log(val);
    val.c.cc = [];
  }
}
</script>
```

当然不仅仅是地址值的改变 可以引发页面刷新的响应行为都是会引起Watch死循环的
例如：push、splice等
## 父子组件公用同一个对象来进行通信

首先我们看父组件的代码	
``` html
<template>
  <div>
    <child-vn :arr-list="arr" @change="arr = $event">
    </child-vn>
    <div v-for="(item, index) in arr" :key="index">{{ item }}</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import ChildVn from "./1_1.vue";
@Component({
  components: {
    ChildVn
  }
})
export default class Activity extends Vue {
  arr: any = [];
  created(){
    setTimeout(()=>{
      this.arr =[1,2,3,4,5,6]
    },500)
  }
}
</script>
```

这个是子组件的代码
``` html
<template>
  <div>
    <button @click="setObj">更改arr</button>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop } from "vue-property-decorator";

@Component({
  components: {}
})
export default class Activity extends Vue {
  @Prop({
    type: Array,
    default() {
      return [];
    }
  })
  readonly arrList!: any[];

  thisArrList: any[] = this.arrList;

  @Watch("objLint")
  objLintWatch(val: any) {
    this.thisArrList = val;
  }
  @Watch("thisArrList")
  thisArrListWatch(val: any) {
    console.log(val);
    this.$emit("change", val);
  }

  setObj() {
    this.thisArrList = ["a", "b", "c", "d"];
  }
}
</script>
```
上面的代码是一个典型的 父组件获取服务器数据，然后将数据打包成一个数组 传递到
子组件，然后由子组件对这个对象进行行为逻辑处理，之后返回父组件，由父组件进行
对数据展示。	
这里可以看到对父子组件之间的通信，为了保证通信顺利，我们采用了双Watch的方式，
这个时候我们点击按钮更改arr 我们发现页面顺利改变了，没有发生死循环

在这种情况下 子组件的arrList和我们定义的本地的thisArrList就形成了双向绑定的行为，
也就是说，这两个值已经是引用了同一个地址了。这个时候，如果我们做出了在上一个模块中Watch造成死循环的行为，那么这个代码就会发生
死循环

例如: 我们在子数组中的传入参数中改变thisArrList的引用地址的时候。死循环就发生了
``` javascript
@Watch("objLint")
objLintWatch(val: any) {
	this.thisArrList = [].concat(val);
}
```
同样在传出Watch中动态改变这个值也会导致死循环
``` javascript
@Watch("thisArrList")
thisArrListWatch(val: any) {
	// console.log(val);
	this.$emit("change",  [].concat(val));
}
```

当然push数据等操作也会引发这个问题

这个情况下 我们不能对双向绑定的值在Wtach中进行改变自身的对象，数组更改的操作。
但是实际中这种例子会出现的。	
例如我们写一个多图片上传功能
服务器传给我们string[] 类型的，里面是图片地址。但是我们在这个组件中展示的时候，
需要附加其他值 ，这个string[]类型我们就需要转换成 obj[]类型的，但是我们传出的
时候，还是string[]类型的

下面这个就是这么一个例子  
``` html
<!-- 父组件 -->
<template>
  <div>
    <child-vn :arr-list="arr" @change="arr = $event">
    </child-vn>
    <div v-for="(item, index) in arr" :key="index">{{ item }}</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import ChildVn from "./1_1.vue";
@Component({
  components: {
    ChildVn
  }
})
export default class Activity extends Vue {
  arr: any = [];
  obj:any={} ;
  created(){
    setTimeout(()=>{
      this.arr =['父组件数据1','父组件数据2']
    },500)
  }
}
</script>
```
``` html
<!-- 子组件  用来多图片上传的-->
<template>
  <div>
    <button @click="setObj">上传图片</button>
    <div style="border:1px solid red">
      子组件
      <div v-for="(item, index) in thisArrList" :key="index">{{ item }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop } from "vue-property-decorator";

@Component({
  components: {}
})
export default class Activity extends Vue {
  @Prop({
    type: Array,
    default() {
      return [];
    }
  })
  readonly arrList!: any;

  thisArrList: any = [];

  @Watch("arrList")
  arrListWatch(val: any) {
    let list = [];
    for(let item of val){
      list.push({
        name:`${item}.jpg`,
        url:item
      })
    }
    this.thisArrList = list;
  }
	
	@Watch("thisArrList")
	thisArrListWatch(val: any) {
		let list = [];
		for(let  item of val){
		  list.push(item.url)
    }
    this.$emit('change',list);
	}
  
  setObj() {
		let url = "子组件添加";
    this.thisArrList.push({
			name:`${url}.jpg`,
			url:url
		});
  }
}
</script>
```

这个时候我们如果在Watch中更改，那么就会陷入死循环了，所以我们可以调整一个思路

``` html
<template>
  <div>
    <button @click="setObj">上传图片</button>
    <div style="border:1px solid red">
      子组件
      <div v-for="(item, index) in thisArrList" :key="index">{{ item }}</div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch, Prop } from "vue-property-decorator";

@Component({
  components: {}
})
export default class Activity extends Vue {
  @Prop({
    type: Array,
    default() {
      return [];
    }
  })
  readonly arrList!: any;

  thisArrList: any = [];

  @Watch("arrList")
  arrListWatch(val: any) {
    let list = [];
    for(let item of val){
      list.push({
        name:`${item}.jpg`,
        url:item
      })
    }
    this.thisArrList = list;
  }

  setObj() {
    let list = [];
    for(let  item of this.thisArrList){
      list.push(item.url)
    }
    list.push("子组件添加的");
    this.$emit("change", list);
  }
}
</script>
```
从上面代码上可以看出 我们只在必要的时候，来对外传值