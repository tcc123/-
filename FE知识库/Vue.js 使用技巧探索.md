## 1. Vue对象的选项

```js
var vm = new Vue({
// 数据
    data: "声明需要响应式绑定的数据对象",
    props: "接收来自父组件的数据",
    propsData: "创建实例时手动传递props，方便测试props",
    computed: "计算属性",
    methods: "定义可以通过vm对象访问的方法",
    watch: "Vue实例化时会调用$watch()方法遍历watch对象的每个属性",
// DOM
    el: "将页面上已存在的DOM元素作为Vue实例的挂载目标",
    template: "可以替换挂载元素的字符串模板",
    render: "渲染函数，字符串模板的替代方案",
    renderError: "仅用于开发环境，在render()出现错误时，提供另外的渲染输出",
// 生命周期钩子
    beforeCreate: "发生在Vue实例初始化之后，data observer和event/watcher事件被配置之前",
    created: "发生在Vue实例初始化以及data observer和event/watcher事件被配置之后",
    beforeMount: "挂载开始之前被调用，此时render()首次被调用",
    mounted: "el被新建的vm.$el替换，并挂载到实例上之后调用",
    beforeUpdate: "数据更新时调用，发生在虚拟DOM重新渲染和打补丁之前",
    updated: "数据更改导致虚拟DOM重新渲染和打补丁之后被调用",
    activated: "keep-alive组件激活时调用",
    deactivated: "keep-alive组件停用时调用",
    beforeDestroy: "实例销毁之前调用，Vue实例依然可用",
    destroyed: "Vue实例销毁后调用，事件监听和子实例全部被移除，释放系统资源",
// 资源
    directives: "包含Vue实例可用指令的哈希表",
    filters: "包含Vue实例可用过滤器的哈希表",
    components: "包含Vue实例可用组件的哈希表",
// 组合
    parent: "指定当前实例的父实例，子实例用this.$parent访问父实例，父实例通过$children数组访问子实例",
    mixins: "将属性混入Vue实例对象，并在Vue自身实例对象的属性被调用之前得到执行",
    extends: "用于声明继承另一个组件，从而无需使用Vue.extend，便于扩展单文件组件",
    provide&inject: "2个属性需要一起使用，用来向所有子组件注入依赖，类似于React的Context",
// 其它
    name: "允许组件递归调用自身，便于调试时显示更加友好的警告信息",
    delimiters: "改变模板字符串的风格，默认为{{}}",
    functional: "让组件无状态(没有data)和无实例(没有this上下文)",
    model: "允许自定义组件使用v-model时定制prop和event",
    inheritAttrs: "默认情况下，父作用域的非props属性绑定会应用在子组件的根元素上。当编写嵌套有其它组件或元素的组件时，可以将该属性设置为false关闭这些默认行为",
    comments: "设为true时会保留并且渲染模板中的HTML注释"
});
```

## 2.   Vue中watch的高级用法

（1）监听$route

使用场景：当两个路由指向同一个组件是，修改路由视图并未更新，第二个组件的created未执行，这时需要监听$route

​                    eg:  

```js
created () {
    console.log(this.getStatus(this.$route.path))
},
methods: {
    getStatus (urlStr) {
        var urlStrArr = urlStr.split('/')
        return urlStrArr[urlStrArr.length - 1]
    }
},
watch: {
    '$route' (to, from) {
        console.log(this.getStatus(this.$route.path))
    }
}
```

   (2)  watch默认在数据第一次修改时开始监听，对对象的内部的属性监听不到，只能监听对象的引用。

```
          使用场景：1.组件要求第一次修改之前就开始监听 2. 深度去监听对象的属性。
```

​                      eg:

```js
data() {
    return {
        fullName: '',
        firstName: '',
        lastName: '',
        obj: {
            name: 'huoalong'   
        }
    }
},
watch: {
    firstName: {
        handler(newName, oldName) {
            this.fullName = newName + this.lastName;
        },
        immediate: true    //在第一次修改之前开始监听，
        deep: true,   //深度监听对象内部的属性
    }
}
```

deep的意思就是深入观察，监听器会一层层的往下遍历，给对象的所有属性都加上这个监听器，但是这样性能开销就会非常大了，任何修改obj里面任何一个属性都会触发这个监听器里的 handler,如果我们明确要观察哪个属性时可以这样做

```js
watch: {
    ‘obj.name’: {
        handler(newName, oldName) {
            this.fullName = newName + this.lastName;
        },
        immediate: true    //在第一次修改之前开始监听
    }
}
```

  (3)    通过组件的属性props设置一个动态变化的值时需要用到watch

​                    场景：父子组件通过props传动态值时，需要在子组件data里面重新定义一个变量对象来盛放props，然后用watch监听。

```js
父组件
<el-dialog title="消息列表" :visible.sync="dialogTableVisible" size="tiny">
        <MessageList :tabs="tabs"></MessageList>
</el-dialog>
 
 
子组件
  props: {
         tabs: {
             type: String,
         }
     },
     data() {
         return {
             //tabs: '课程报名申请',
             activeName2: 'first',
             tab: {
                 tabs: this.tabs,
             }
         };
     },
    watch: {
        tabs: function(value) {
            this.tab['tabs'] = value;
        }
    },
```

## 3.   在钩子函数里面用$refs定位DOM时遇到的坑      

​       在mounted函数里面用$refs定位dom一般会得到undefined的情况，mounted阶段，DOM结构准备就绪，但是这里的准备就绪需要特别说明一下：

​       DOM结构已经出来了，但是如果在DOM结构中的某个DOM节点使用了v-if、v-show或者v-for（即根据获得的后台数据来动态操作DOM，即响应式），那么这些DOM是不会再mounted阶段找到的。

此时的mounted阶段，一般是用于发起后端请求，拿回数据，配合路由钩子做一些事情，简单来说就是在mounted钩子中加载数据而已，加载回来的数据是不会再这个阶段更新的DOM中的

所以如果在mounted钩子中使用$refs，如果ref是定位在有v-if、v-for、v-show中的DOM节点，返回来的只能是undefined，因为在mounted阶段他们根本不存在！！

​        经过检验，上面端文字是错误的，$refs定位不到的主要原因是因为v-if、v-for、v-show这些语句如果依赖父组件传来的参数的话，该该参数是在mounted()阶段子还没获取得到~~~~！！！！

如果想要真正地在DOM加载完成后拿到数据，就需要调用VUE的全局api ： this.$nextTick(() => {})

​        如果说mounted阶段是加载阶段，那么updated阶段则是完成了数据更新到DOM的阶段(对加载回来的数据进行处理)，此时，ref、数据等等全部都挂载到DOM结构上去，在update阶段使用this.$refs.xxx，就100%能找到该DOM节点。

​        updated与mounted不同的是，在每一次的DOM结构更新，vue都会调用一次updated(){}钩子函数！而mounted仅仅只执行一次而已

简单来说，只要在调试的时候，能看到元素的存在，在updated阶段都可以使用this.$refs.xxx找到对应的DOM节点！

## 4.   组件之间传递参数的五种方法

​         （1）路由query  使用场景：根据url地址来传参 

```js
{
    path: '/workTaskEdit/:id',
    name: 'workTaskEdit',
    components: '',
 },
 <router-link :to="{path:'/workTaskEdit',query{id:id}}">
 
vm.$router.push({name:'workTaskEdit', query:{id:id}})
```

(2）props    使用场景：父子组件传参

 子组件

 props: [ 'a' ] 或者 props: {a:{type: 'Array'}}

父组件

<component  :a= 'data'> </component>

（3）vue Bus   使用场景：简单的非父子组件传参

 aa组件和bb组件为非父子组件，假设 bb 组件里面有个按钮，点击按钮，把 123 传递给 aa 组件

 在根组件定义Bus，用一个空的Vue实例作为中央事件总线，注册到根组件，避免局部作用域的影响。

```js
/ 根组件（this.$root)
new Vue({
  el: '#app',
  router,
  render: h => h(App),
  data: {
   // 空的实例放到根组件下，所有的子组件都能调用
    Bus: new Vue()
  }
})
 
 
 
 
bb 组件内调用事件触发↓
<button @click="submit">提交<button>
 
methods: {
   submit() {
     // 事件名字自定义，用不同的名字区别事件
      this.$root.Bus.$emit('eventName', 123)
    }
 }
 
aa 组件内调用事件接收↓
  // 当前实例创建完成就监听这个事件
  created(){
    this.$root.Bus.$on('eventName', value => {
      this.print(value)
    })
  },
 
  methods: {
    print(value) {
      console.log(value)
    }
  },
 
  // 在组件销毁时别忘了解除事件绑定
  beforeDestroy() {
     this.$root.Bus.$off('eventName')
  },
```

 补充： 利用Bus可以在Vue外部调用methods里面的方法：

在父组件中的methods的方法中定义一个方法，在方法里用$emit接收公共组件里的方法，注意父组件要用一个变量保存，var vm = new Vue({})

```js
var vm = new Vue({ 
    el: '#example', 
    data: { 
        msg: 'Hello Directive', 
        data: {} 
    }, 
    methods: { 
        getCardNum: function (data, on) { 
            eventHub.$emit('translate', data); 
        } 
    } 
}); 
```

在事件当前的组件中，在created中，用$on向公共的组件eventHub传递，translate是自定义的，getCardNum(data)是要在外部调用的方法;    

```js
eventHub.$on('translate', function (data) { 
       that.getCardNum(data); 
});
```

  最后就可以在vue组件外部，或者文件外部调用getCardNum（data）这个函数，比如在html中就可以 onclick = vm.getCardNum() 这样来调用；vm是父组件

（4）vuex      使用场景：复杂的非父子组件传参，各个组件共享数据

  复杂的项目中一般用vuex   参考 [https://vuex.vuejs.org/zh-cn/api.html

## 5.   **组件和route使用$router.params.xx耦合度太高，可尝试使用props解耦**

```js
//通过 props 解耦
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },
 
    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
 
//这样你便可以在任何地方使用该组件，使得该组件更易于重用和测试。
```

补充： **切记router.push的时候不要path和params一起用**，params会失效，建议用query，如果非要用params，可以使用组件的name。**而且一定要注意取数据的时候是route，不是router，千万不要被坑   了，**打印出来可以发现route是本路由的信息，而router是全局的router信息

## 6.   导航守卫      

###      完整的导航解析流程

1. 导航被触发。

2. 在失活的组件里调用离开守卫。

3. 调用全局的 `beforeEach` 守卫。

4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。

5. 在路由配置里调用 `beforeEnter`。

6. 解析异步路由组件。

7. 在被激活的组件里调用 `beforeRouteEnter`。

8. 调用全局的 `beforeResolve` 守卫 (2.5+)。

9. 导航被确认。

10. 调用全局的 `afterEach` 钩子。

11. 触发 DOM 更新。

12. 用创建好的实例调用 beforeRouteEnter守卫中传给 next 的回调函数。

    （1）组件内的守卫

    - beforeRouteEnter 不能访问this,但是在next里面有一个vm参数 可以访问组件实例  场景：在渲染该组件的对应路由被 confirm 前调用
    - beforeRouteUpdate   有this，不支持回调   在当前路由改变，但是该组件被复用时调用
    - beforeRouteLeave    有this，不支持回调   场景：这个离开守卫通常用来禁止用户在还未保存修改前突然离开

    （2）路由独享的守卫

    ​        routes: [ { path: '/foo', component: Foo, beforeEnter: (to, from, next) => { // ... } } ]

## 7.   mixins使用注意事项和高级用法，继承extends，二者类似

​        对比： mixins接受对象数组（多继承），extends接受对象或函数（单继承）

####           合并策略：

####          （1）值为对象的选项，如 methods, components 和 directives 将合并到同一个对象内。如果键冲突则组件的选项优先。

####          （2）同名钩子函数被并入一个数组，因而都会被调用。另外，混合的钩子将在组件自己的钩子之前调用

```js
继承钩子函数
const extend = {
 created () {
  console.log('extends created')
 }
}
const mixin1 = {
 created () {
  console.log('mixin1 created')
 }
}
const mixin2 = {
 created () {
  console.log('mixin2 created')
 }
}
export default {
 extends: extend,
 mixins: [mixin1, mixin2],
 name: 'app',
 created () {
  console.log('created')
 }
}
控制台输出
extends created
mixin1 created
mixin2 created
created
```

- 结论: 优先调用mixins和extends继承的父类，extends触发的[优先级](http://www.php.cn/code/6066.html)更高，相对于是队列
- push(extend, mixin1, minxin2, 本身的钩子函数)

**mixins**

​      调用方式: mixins: [mixin1, mixin2]

​      是对父组件的扩充，包括methods、components、directive等。。。

​      触发钩子函数时，先调用mixins的函数，再调用父组件的函数。

​      虽然也能在创建mixin时添加data、template属性，但当父组件也拥有此属性时以父为准，从这一点也能看出制作者的用心（扩充）。

​      data、methods内函数、components和directives等键值对格式的对象均以父组件/实例为准

**extends**

​      调用方式: extends: CompA

​      同样是对父组件的扩充，与mixins类似，但优先级均次于父组件

**extend**

​      扩展组件的构造器

​      当我们调用vue.component('a', {...})时自动调用

​      值得注意的是extend内的data为一个函数

## 8.  model指令高级

​        场景： 默认情况下v-model把value用作prop且把input作为event事件，而一些输入类型比如单选框和复选框可能想使用value另做它用，这时需要修改prop来达到不同的目的。

```js
Vue.component('my-checkbox',{
    model: {
        prop: 'checked',
        event: 'change',
    },
    props: {
        value: String,
        check: {
            type: 'Number',
            default: 0,
        }
    }
})
```

## 9.  is的使用

####      场景 ：运用在动态组件，且基于dom内模版的限制下工作

​      value: 已注册的组件名称或者一个组件的选项对象

​       结合keep-alive组件使用效果更好 创建一次组件缓存，避免重复渲染

```js
<keep-alive>
    <component :is='currentTab'></component>
</keep-alive>
```

```js
//变通
<table>
<tr :is='my-component'></tr>
</table>
```

## 10.  利用Object.freeze()提升性能

 场景： 对于纯展示的大数据

​        eg：  data: { list: Object.freeze([.........]) }

## 11.  插槽的使用

​         内容分发API <slot>

​          具名插槽：

```js
父组件
<templete slot="header">
    <h1>.....</h1>
</templete>
或者
<h1 slot='header'>...</h1>
 
 
子组件
<header>
    <slot name='header'></slot>
</header>
默认插槽
<slot>默认值</slot>
```

作用域插槽传值

```js
父组件
<templete slot-scope='{todo}'>
    {{ todo.text }}
</templete>
子组件
<slot :todo="todo">
     {{ todo.text }}
</slot>
```

## 10.  Vue对变动的数组和对象检测时,需要注意的地方

​          数组：以下两个变动的数组不能检测到

​               （1）利用索引直接设置一个项时，vm.items[indexOf] = newItem;

​                 (2) 修改数组的长度, vm.items.length = newLength;

​                解决办法：

​                 （1） Vue.set(items,index,newValue)

​                            Vue.items.splice(items,1,newValue)

​                           this.$set(items,index,newValue)

​                    (2)  items.splice(newLength)

​          对象：对象添加属性和删除属性时，vue不能检测到

​                   解决办法：

​                   Vue.set(object,newKey,newValue)

​                   this.$set(object,newKey,newValue)

​                   Object.assign({},object,{属性列表})