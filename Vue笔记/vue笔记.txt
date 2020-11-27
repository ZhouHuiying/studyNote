VUE:

el:挂载点  建议使用ID选择器 可以使用其他标签 不要使用HTML
data:数据对象 

示例：
    <div id="app">     
        </div>

        <!-- 开发环境版本，包含了有帮助的命令行警告 -->
        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>
        var app = new Vue({
            el: '#app',  
            data: {
            },
            methods:{

            }
            })  
        </script>

Vue指令：
    v-text:设置文本
    v-html:设置元素的innerHTML

    v-on:为元素绑定事件 @click  v-on:click=''  传递自定义参数，事件修饰符

    v-show:根据表达式的真假，切换元素的显示和隐藏 频繁操作的时候用
    v-if:根据表达式的真假，切换元素的显示状态(操纵dom元素)  表达式的值为真存在于dom树中

    v-bind:设置元素的属性( class title src),都写在元素的内部  v-bind:属性名=表达式；
    v-for:根据数据生成列表结构
    v-model:获取和设置表单元素的值，双向数据绑定

官网笔记：
    https://cn.vuejs.org/v2/api/

    模板语法：
        v-bind缩写：
            <a v-bind:href="url">...</a>  
            <a :href="url">...</a>
            <a :[key]="url"> ... </a>
        v-on缩写：
            <a v-on:click="doSomething">...</a>
            <a @click="doSomething">...</a>
            <a @[event]="doSomething"> ... </a>


    计算属性，

    class与style绑定：
        绑定HTML Class：通过v-bind绑定 可以用在对象和数组上
        绑定内联样式： 通过v-bind:style 绑定 可以用在对象和数组上

    条件渲染： 条件性地渲染一块内容
        v-if
        v-else
        v-else-if
        v-show

    列表渲染：
        v-for

    监听事件：
        v-on
            事件处理方法
            事件修饰符：例如 <a v-on:click.stop="doThis"></a>
            按键修饰符：例如 <input v-on:keyup.enter="submit">  按键码
            系统修饰符：.ctrl .alt .shift .meta
        
                <!-- 阻止单击事件冒泡 -->
                <a v-on:click.stop="doThis"></a>
                <!-- 提交事件不再重载页面 -->
                <form v-on:submit.prevent="onSubmit"></form>
                <!-- 修饰符可以串联  -->
                <a v-on:click.stop.prevent="doThat"></a>
                <!-- 只有修饰符 -->
                <form v-on:submit.prevent></form>
                <!-- 添加事件侦听器时使用事件捕获模式 -->
                <div v-on:click.capture="doThis">...</div>
                <!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
                <div v-on:click.self="doThat">...</div>

                <!-- click 事件只能点击一次，2.1.4版本新增 -->
                <a v-on:click.once="doThis"></a>
                
    表单输入绑定：
        v-model:在表单 <input>、<textarea> 及 <select> 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。
        
    v-loading加载:  https://www.jianshu.com/p/b0bd0d7db3cb
        //全局loading
        <template>
            <div v-loading="loading"> </div>
        </template>
    
        在data 中定义初始化， loading: false，同时在mounted()中将 this.loading设置为true,再去请求接口
        在接口的回调函数中，将 this.loading 设为false，到达效果。
    
    组件注册：组件注册的两种方式：全局注册（全局注册的组件都可以用）、局部注册（使用的时候需要引用）

    prop的大小写：
        HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。这意味着当你使用 DOM 中的模板时，camelCase (驼峰命名法) 的 prop 名需要使用其等价的 kebab-case (短横线分隔命名) 命名：
        在html中，传参数使用小写  :report-json=""
        可以传递静态或者动态的参数 可以传递很多类型 

        单向数据流：所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解。
        

