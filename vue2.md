Vue2
一、Vue简介
1. 介绍
2. 特点
二、HelloWorld
三、v-bind指令
四、v-model指令
五、el与data的两种写法
5.1 el的两种写法
5.2 data的两种写法
六、MVVM模型
七、Object.defineProperty方法
八、数据代理
8.1 数据代理概念
8.2 Vue中的数据代理
九、事件处理
9.1 基本使用
9.2 $event
9.3 事件修饰符
9.3.1 prevent事件
9.3.2 stop事件
9.3.3 once事件
9.3.4 self事件
9.4 按键修饰符
十、计算属性
10.1 基本概念
10.2 简写形式
一、Vue简介
1. 介绍
Vue 是一套用来动态构建用户界面的渐进式 JavaScript 框架

构建用户界面：把数据通过某种办法变成用户界面
渐进式：Vue 可以自底向上逐层的应用，简单应用只需要一个轻量小巧的核心库，复杂应用可以引入各式各样的 Vue 插件
2. 特点
2.1 采用组件化模式，提高代码复用率、且让代码更好维护

每一个组件由三部分组成：HTML、CSS、JS，如下图：


2.2 使用虚拟 DOM 和 Diff 算法，尽量复用 DOM 节点

如果数据修改，不需要重新拼串，在虚拟 DOM 层会通过 Diff 算法比较与原数据的差异，从而复用原数据，如下图：


2.3 声明式编码，让编码人员无需直接操作 DOM，提高开发效率

不需要像命令式编码一样将每一步操作都通过代码编写，如下图：


二、HelloWorld
首先需要引入Vue，通过本地文件引入，或通过网络引入：https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js
想要让Vue工作，就需要创建一个Vue实例，给这个实例传入一些数据，比如 data（定义数据）、methods（定义方法） 等，并将这个实例绑定给页面的 div 标签（称为容器）
在容器的标签中通过 {{}} （插值表达式）编写 JS 表达式可以直接获取 data 中的数据，一旦 data 中的数据发生改变，那么页面中用到该数据的地方也会自动更新
代码示例：

<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8" />
        <title>初识Vue</title>

        <!-- 通过网络引入Vue -->
        <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
    </head>
    <body>
        
        <!-- 准备好一个容器 -->
        <div id="root">
            <!-- 获取Vue实例中data中的数据 -->
            <h1>Hello，{{name}}，{{address}}</h1>
        </div>
    
        <script type="text/javascript" >
            //创建Vue实例
            new Vue({
                el:'#root', //el用于指定当前Vue实例与哪个容器绑定，此处绑定id值为root的容器
                data:{ //data中用于存储数据，数据供el所指定的容器去使用
                    name:'周杰伦',
                    address:'台北'
                }
            })
        </script>
    </body>
</html>


运行结果：


注意：

容器中的代码依然符合 HTML 规范，只不过可以写一些 Vue 的语法
容器和 Vue 实例是一一对应的，可以有多个容器和Vue实例一一对应（但是一般只写一个容器和一个Vue实例，其余内容配合组件一起使用）
三、v-bind指令
基本语法：v-bind:属性名 = "JS表达式"，表示将 JS 表达式的值赋值给此属性（单向绑定）

简写：:属性名 = "JS表达式"

代码示例：

<div id="root">
    <!-- 插值表达式获取data中的属性值 -->
    <h3>你好，{{name}}</h3>
    <!-- 单向绑定data中的属性值赋值给href属性 -->
    <a v-bind:href="school.url.toUpperCase()">点我去{{school.name}}学习</a>
    <!-- 简写方式 -->
    <a :href="school.url">点我去学习</a>
</div>
</body>

<script type="text/javascript">

    //阻止vue在启动时生成生产提示
    Vue.config.productionTip = false 
    
    new Vue({
        el:'#root',
        data:{
            name:'Jack',
            school:{
                name:'霍格沃茨',
                url:'http://www.baidu.com',
            }
        }
    })
</script>


运行结果：


注意：

如果 data 中的属性值发生变化，单向绑定的数据也会自动的随之变化

插值表达式一般写在标签体中，单向绑定一般用于给标签的属性绑定值

四、v-model指令
基本语法：v-model:value = "JS表达式"，表示双向绑定，也就是 data 中的值被修改，页面的值也会被修改；页面的值被修改，对应的 data 属性值也会随之修改

简写：v-model = "JS表达式"，即双向绑定默认操作的属性就是 value

代码示例：

<body>
    <div id="root">

        <!-- 普通写法 -->
        双向数据绑定：<input type="text" v-model:value="name"><br/>
    
        <!-- 简写 -->
        双向数据绑定：<input type="text" v-model="name"><br/>
    
        <div> data属性的值：{{name}} </div>
    </div>
</body>

<script type="text/javascript">
    new Vue({
        el:'#root',
        data:{
            name:'Bruno Mars'
        }
    })
</script>

运行结果：


注意：

双向绑定只能使用在表单类元素上，也就是标签必须有 value 属性，比如输入框、单选框、多选框等
插值表达式和单向绑定数据只能从 data 流向页面，而双向绑定还可以使数据从页面流向 data
五、el与data的两种写法
5.1 el的两种写法
5.1.1 第一种写法

<script type="text/javascript">
    new Vue({
        el: '#root', //绑定id为root的容器
        data: {
       		//定义数据 
    	}
    })
</script>

5.1.2 第二种写法

<script type="text/javascript">
    const vm = new Vue({
        data: {
       		//定义数据 
    	}
    })
    vm.$mount('#root') //绑定id为root的容器
</script>

两种方式的区别：

第一种创建Vue对象的时候就要绑定容器；第二种先创建Vue对象，可以做一些其他操作后再绑定容器。

5.2 data的两种写法
5.2.1 对象式

<script type="text/javascript">
    new Vue({
        el: '#root',
        //定义data对象，然后在其中定义属性
        data: {
       		name:'Nicki Minaj' 
    	}
    })
</script>

5.2.2 函数式

<script type="text/javascript">
    new Vue({
        el: '#root',
        //定义data函数，然后在返回值中定义属性
        data(){
            return{
                //返回值是定义的数据
                name:'Nicki Minaj' 
            }
        }
    })
</script>

注意：

使用组件时，必须使用函数式定义数据
由 Vue 管理的函数不能写成箭头函数，如果使用箭头函数，则函数中的 this 表示的是 window，如果使用普通函数格式，则 this 表示 Vue对象
六、MVVM模型
M：模型 (Model) ：data中的数据
V：视图 (View) ：模板代码
VM：视图模型 (ViewModel)：Vue实例
图示：


图中 Vue 实例的双向箭头表示：

Data Bindings：数据存放在 Model 中，经过 Vue 实例将数据绑定在页面上
DOM Listeners：页面上的数据会被 Vue 实例监听，比如双向绑定：页面数据发生变化，对应 Model 中的数据也跟着变化

代码示例：

<body>
    <div id="root">
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>

        <!-- VM身上的所有数据，view中都可以直接获取 -->
        <h1>测试一下2：{{$options}}</h1>
        <h1>测试一下3：{{$emit}}</h1>
    </div>
</body>

<script type="text/javascript">

    //一般使用vm这个变量名表示Vue实例
    const vm = new Vue({
        el:'#root',
        data:{
            name:'清华大学',
            address:'北京',
        }
    })
    // 控制台输出Vue实例
    console.log(vm)
</script>


运行结果1：

控制台输出 Vue 实例：


运行结果2：

页面展示：


结论：

data 中所有的属性，最后都出现在了 Vue 实例中
Vue 实例身上所有的属性以及 Vue 原型所有的属性，都可以直接在 View 中使用
七、Object.defineProperty方法
先看一段 JS 代码，将一个变量的值赋值给一个属性：

let number = 18
let person = {
    name:'张三',
    sex:'男',
    age:number //age值为18
}

如果通过这种方式给 age 赋值，只有第一次代码执行到赋值语句时才会将 age 的值修改为 18，如果以后 number 的值发生变化，则 age 不会跟着变化。

方法介绍：

此方法用于给对象定义属性使用，参数1表示给哪个对象定义属性，参数2表示给对象中的哪个属性赋值，参数3写赋予值的配置对象（对属性值进行一些高级的操作），如下所示：

<script type="text/javascript" >

    let person = {
        name:'张三',
        sex:'男',
    }
    
    Object.defineProperty(person,'age',{
        value:18, //这个属性值为18
        enumerable:true, //控制属性是否可以枚举（被遍历），默认值是false
        writable:true, //控制属性是否可以被修改，默认值是false
        configurable:true //控制属性是否可以被删除，默认值是false
    })

</script>

运行结果：


get()、set()方法：

<script type="text/javascript" >

    let number = 12
    let person = {
        name:'张三',
        sex:'男',
    }
    
    Object.defineProperty(person,'age',{
    
        //当有人读取person的age属性时，get函数（getter）就会被调用，且返回值就是age此时的值
        get(){
            console.log('有人读取age属性，get方法被调用')
            return number //返回定义的number变量值
        },
    
        //当有人修改person的age属性时，set函数（setter）就会被调用，且参数是要赋予age属性的值
        set(value){
            console.log('有人修改age属性，set方法被调用，修改值为',value)
    
            //age的值取决于number，修改number的值就可以修改age的值
            //并没有直接修改age属性值
            number = value 
        }
    })
</script>


运行结果：


上述代码完成了一种功能：person 对象中有 age 这个属性，但是 age 属性的值并不是固定的，可以随时读取，随时修改。

八、数据代理
8.1 数据代理概念
数据代理：通过一个代理对象，操作另一个对象中的数据，而不是直接操作该对象。

代码示例：

<!-- 想要通过obj2获取、修改obj中的数据，而不是直接操作obj -->
<script type="text/javascript" >

    let obj = {
        x:100
    }
    let obj2 = {
        y:200
    }
    
    Object.defineProperty(obj2,'x',{
        get(){
            return obj.x
        },
        set(value){
            obj.x = value
        }
    })
</script>


运行结果：


8.2 Vue中的数据代理
观察下述代码：

<body>
    <div id="root">
        <h2>名称：{{name}}</h2>
        <h2>地址：{{address}}</h2>
    </div>
</body>

<script type="text/javascript">
    const vm = new Vue({
        el:'#root',
        data:{
            name:'Jay',
            address:'台湾'
        }
    })
</script>

运行结果1：


运行结果2：


运行结果3：


总结：

Vue 中的数据代理指的就是通过 Vue 实例（Vue实例中也会有上述的 name、address 属性）来代理 data 对象中 name、address 属性的操作

如果没有数据代理，那么在 View 中获取 data 中的数据只能通过上述的 _data 来获取

Vue 使用数据代理的目的就是可以编码时简写，比如直接写 name 就可以拿到 'Jay' 这个值（编码获取的是 Vue 中的 name，最终通过 getter() 方法读取到的是 data 中的 name）
Vue 使用数据代理还可以将数据的改变同步到页面上，比如修改 vm.name 值，最终通过 setter() 方法修改的是 data 中的数据，data 中的数据发生了变化，最终会直接影响到页面上的数据
只要 data 中的数据发生变化，Vue 就会重新解析模板（View），以更新模板中的值，如果解析模板的过程中，插值表达式中有调用函数，这个函数一定会被重新调用
通过 Object.defineProperty() 方法把 data 对象中的所有属性添加到 Vue 实例上，并且为每个属性自动的添加 getter/setter，通过这些方法修改 data 中的属性值

具体见下图：


可以发现编码时写的 data 没有出现在 Vue 实例中，都转换成了 _data，所以最终读取的都是 _data 中的数据。

只有 data 中的数据会做数据代理

九、事件处理
9.1 基本使用
基本语法：v-on:事件名 = "xxx"，xxx 表示事件发生时要执行的函数，也可以写一些简单的逻辑语句

简写：@事件名 = "xxx"

**事件对象：**事件触发的时候会执行某个函数，该函数的形参（如果只有一个）会自动接收一个事件对象（即使调用函数时没有传递任何实参），这个事件对象中包含这个事件的各种信息

代码示例：

<body>
    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>
        <!-- 调用函数时没有传递任何参数 -->
        <button @click="showInfo1">点我提示信息1</button>
    </div>
</body>

<script type="text/javascript">
    const vm = new Vue({
        el:'#root',
        data:{
            name:'霍格沃茨',
        },

        //定义的方法要写在methods对象中
        methods:{
            showInfo1(event){
                
                //event表示事件对象，target表示发生这个事件时的标签
                //event可以更改成其他的任何字母（都表示事件对象）
                console.log(event.target)
                
                //innerText表示标签中的文本
                console.log(event.target.innerText)
            },
        }
    })
</script>


运行结果1：


运行结果2：


9.2 $event
如果想要事件发生时既要传递实参又要在函数中使用事件对象，那么就必须在调用函数时使用 $event 来表示传递的是事件对象，如下代码所示：

<div id="root">
    <h2>欢迎来到{{name}}学习</h2>
    <!-- $event表示这个参数是事件对象而不是自定义的实参 -->
    <button @click="showInfo1($event, 22)">点我提示信息1</button>
</div>
</body>

<script type="text/javascript">
    const vm = new Vue({
        el:'#root',
        data:{
            name:'霍格沃茨',
        },
        methods: {
            showInfo1(event, number) {
                // event表示事件对象，number表示接收的实参
                console.log(event.target)
                console.log('参数数字是' + number)
            },
        }
    })
</script>

运行结果：


总结：

methods 中定义的方法属于被 Vue 实例所管理的方法：

如果使用箭头函数，this 表示 window
如果使用普通函数，this 表示 Vue 实例或者组件实例对象
9.3 事件修饰符
事件修饰符有6种：

prevent：阻止事件的默认行为
stop：阻止事件冒泡（使用在内部标签上）
事件冒泡：内部标签的事件触发时，外层标签如果有相同的事件，那么这个外部标签的事件会被自动触发
once：事件只触发一次
self：只关心自己标签上触发的事件，不监听事件冒泡
capture：使用事件的捕获模式
passive：事件的默认行为立即执行，无需等待事件回调执行完毕
9.3.1 prevent事件
代码示例：

<body>
    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>
        <!-- a标签的默认行为是跳转到指定的页面，prevent阻止跳转，只执行函数 -->
        <a href="http://www.baidu.com" @click.prevent="showInfo">点我提示信息</a>
    </div>
</body>

<script type="text/javascript">
    new Vue({
        el:'#root',
        data:{
            name:'霍格沃茨'
        },
        methods:{
            showInfo(){
                alert('同学你好！')
            }
        }
    })
</script>

点击之后只弹出提示信息，没有跳转到百度页面。

9.3.2 stop事件
代码示例：

<body>
    <div id="root">
        <h2>欢迎来到{{name}}学习</h2>
        <!-- div标签是外层标签，拥有与内层标签一致的点击事件 -->
        <div @click = "showInfo">
            <!-- 默认点击按钮之后，内部标签的事件触发，外部标签的事件也会随之触发（事件冒泡） -->
            <!-- 使用stop阻止了事件冒泡，不会触发外层标签的同一事件 -->
            <button @click.stop = "showInfo"> 按钮 </button>
        </div>

    </div>
</body>

<script type="text/javascript">
    new Vue({
        el:'#root',
        data:{
            name:'霍格沃茨'
        },
        methods:{
            showInfo(){
                alert('同学你好！')
            }
        }
    })
</script>

只弹出一次提示信息，如果不阻止事件冒泡，则会弹出两次提示信息。

9.3.3 once事件
<!-- 事件只会执行一次，之后再点击也不会触发事件了 -->
<button @click.once="showInfo"> 按钮 </button>

9.3.4 self事件
<!-- div标签是外层标签，拥有与内层标签一致的点击事件 -->
<!-- 使用了self事件，只有点击了外部事件才会触发，不监听事件冒泡 -->

<div @click.self = "showInfo">
    <button @click= "showInfo"> 按钮 </button>
</div>

点击了按钮之后，只弹出一次提示信息。

注意：事件修饰符可以连续写，比如：@click.prevent.stop = "method" 表示既阻止事件的默认行为又阻止事件冒泡的发生。

9.4 按键修饰符
使用方式：@事件名.按键修饰符名称 = "要执行的方法"

作用：用来和按键事件绑定在一起，修饰特定按键事件

click：点击事件
keyup：按键抬起事件
keydown：按键按下事件
常用的按键修饰符有9个

enter：回车
delete：删除或退格
esc：退出
space：空格
tab：换行（键盘按下就会失去焦点，所以必须配合 keydown 使用）
up：上
down：下
left：左
right：右
代码示例：

<body>

    <div id="root">
        <!-- 只有按下并抬起回车键后才会执行对应的方法 -->
        <input type="text" placeholder="请按下对应的按键" @keyup.enter="showInfo"></input>
    </div>
</body>

<script type="text/javascript">
    new Vue({
        el:'#root',
        methods:{
            showInfo(event){
                alert(event.target.value)
            }
        }
    })
</script>


获取按键名

```js
<script type="text/javascript">
    new Vue({
        methods:{
            // event是事件对象
            showInfo(event){
                //获取按键名
                console.log(event.key)
                //获取按键编码
                console.log(event.keyCode)
            }
        }
    })
</script>
```


可以使用上述方式得到所有按键的按键名，故可以使用的按键修饰符不仅仅是默认的9个。

注意：如果想要使用非默认的9个按键修饰符，使用时必须将对应的按键名进行转换，比如：CapsLock 转换成 caps-lock。

十、计算属性
10.1 基本概念
如果一个属性要通过已有的属性计算出来，那么可以使用计算属性。计算属性的读取和修改必须通过定义 getter/setter 来进行。

注：计算属性的底层借助了 Objcet.defineproperty 方法提供的 getter 和 setter 来完成读取和修改

代码示例：

```js
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"> <br/><br/>
        名：<input type="text" v-model="lastName"> <br/><br/>
        <!-- 调用两次计算属性 -->
        全名：<span>{{fullName}}</span> <br/><br/>
        全名：<span>{{fullName}}</span>
    </div>
</body>

<script type="text/javascript">


    Vue.config.productionTip = false
    
    const vm = new Vue({
        el:'#root',
        data:{
            firstName:'张',
            lastName:'三',
        },
        methods: {
        },
    
        // 定义计算属性必须使用computed对象
        computed:{
            // 一个新的属性fullName，需要通过已有的属性计算出来
            fullName:{
                // 读取计算属性的值
                get(){
                    console.log('计算属性被调用了')
                    // this表示Vue实例，读取data中的属性
                    // 计算属性fullName依赖已有的属性
                    return this.firstName + '-' + this.lastName
                },
                // 修改计算属性的值
                set(value){
                    console.log('set',value)
                    const arr = value.split('-')
                    // 修改data中的属性（依赖的已有属性）
                    this.firstName = arr[0]
                    this.lastName = arr[1]
                }
            }
        }
    })

</script>
```

运行结果1：

可以直接在 View 中读取计算属性：


运行结果2：

初始时，尽管调用了两次计算属性，但是控制台仅显示一次提示，也就是计算属性只调用了1次 get 方法。

原因：计算属性使用了缓存机制，如果已经有了计算属性的值，之后读取的都是缓存值（效率更高）。

运行结果3：


修改输入框内容的同时计算属性的 get 方法会被多次调用。

原因：get 方法何时被调用？（不使用缓存）

第一次读取计算属性时
当依赖的已有属性值发生变化时
运行结果4：


修改计算属性的值时，set 被调用，然后由于修改所依赖的已有的属性值，故 get 方法也会随之调用。

10.2 简写形式
当计算属性只读不改的时候可以使用简写形式。

代码示例：

```js
<script type="text/javascript">
    const vm = new Vue({
        el:'#root',
        computed:{

​        //完整写法
​        /* fullName:{
​				get(){
​					console.log('get被调用了')
​					return this.firstName + '-' + this.lastName
​				}
​			} */

​        //简写
​        //fullName直接当作get方法来用
​        fullName(){
​            console.log('get被调用了')
​            return this.firstName + '-' + this.lastName
​        }
​    }
})
</script>
```

注意：

使用简写形式之后，fullName 仍然是一个属性而不是方法，所以调用的时候不能加小括号。