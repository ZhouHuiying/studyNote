获取元素：
    getElementById();
    getElementByTagName();
    getElementByClassName();
    document.querySelector(); 返回指定选择器的第一个元素对象  .box-类选择器 #-id选择器 li
    document.querySelectorAll();
    
改变 HTML：
    document.getElementById(id).innerHTML = new text
    document.getElementById(id).attribute = new value
    document.getElementById(id).style.property = new style

获取特殊元素：body, html
    document.body;
    document.documentElement;

事件：
    事件三要素：事件源，事件类型，事件处理程序
    获取元素，注册事件，处理程序；

HTML 事件的例子：https://www.w3school.com.cn/js/js_htmldom_events.asp
    当用户点击鼠标时
    当网页加载后
    当图像加载后
    当鼠标移至元素上时
    当输入字段被改变时
    当 HTML 表单被提交时
    当用户敲击按键时

    onclick=''
    onchange='upperCase()':
        当用户改变输入字段内容时，会调用 upperCase() 函数。
    onmouseover 和 onmouseout 事件

事件监听程序：
    addEventListener:
        js中——document.getElementById("myBtn").addEventListener("click", function() { alert("Hello World!"); });
        事件冒泡还是事件捕获？addEventListener(event, function, useCapture); 使用“useCapture”参数来规定传播类型，默认值是 false，将使用冒泡传播
    removeEventListener()：
        删除已通过 addEventListener() 方法附加的事件处理程序：
   
    操作元素内容：
        innerText(); innerHTML();
    操作常见元素属性：
        src,href
        id alt title

    表单元素的属性操作：
        type, value, checked, selected, disabled
        <input type="text" value="输入内容">
        var input=document.querySelector('input');
        btn.onclick = function(){
            input.value='被点击了';
        如果想要某个表单被禁用，用disabled 按钮被禁用
        btn.disabled = true;
        this.disabled=true;  this指向的是事件函数的调用者
        }

    样式属性操作：
        element.style(); this.style.backgroundColor='purple';
                        this.style.width='250px'; 产生的是行内样式
        element.className();  this.className='change';
    
获得元素的属性值：
    element.属性；  div.id; 获取元素自带的属性；
    element.getAttribute('属性'); 可以获取元素自定义的属性；
设置元素的属性值：
    element.属性='值'; div.id='test';
    element.setAttribute('属性','值')
移除属性：
    div.removeAttribute('index');  移除index属性
自定义属性：以data-开头命名；
            div.dataset;   dataset是一个集合里面存放了所有以data开头的自定义属性；

节点操作：
    节点至少拥有nodeType,nodeName,nodeValue三个基本属性
    元素节点nodeType为1，属性节点nodeType为2，文本节点nodeType为3
    主要操作的是元素节点。

节点层级：父子兄层级关系；
        父节点：node.parentNode  得到的是离元素最近的父节点，找不到父节点返回为空
        子节点：
            parentNode.childNodes  得到所有子节点，包含元素节点文本节点等等
            parentNode.children: 获取所有的子元素节点 常用的
            parentNode.firstChild 获取第一个节点 不管是元素节点还是文本节点
            parentNode.lastChild 获取最后节点 不管是元素节点还是文本节点
            //
            parentNode.firstElementChild 获取第一个子元素节点
            parentNode.lastElementChild 获取最后一个子元素节点
        兄弟节点：
            node.nextSibling    得到下一个兄弟节点，包含元素节点文本节点等等
            node.previousSibling 
            //
            node.nextElementSibling 得到下一个兄弟元素节点
            node.previousElementSibling

创建和添加节点：创建和添加元素
    document.createElement('tagname')
        例子：
            var para = document.createElement("p");
            var node = document.createTextNode("这是新的文本。");
            para.appendChild(node);
            var element = document.getElementById("div1");
            element.appendChild(para);
    node.appendChild(child)  后面追加元素 node父级 child子级
    node.insertBefore(child,指定元素)
    替换元素：parent.replaceChild(para, child);

删除节点：
    node.removeChild(child) 删除父节点里的子节点

复制节点：
    node.cloneNode();  浅拷贝：只复制节点本身，不克隆里面的子节点
    node.cloneNode(true); 都复制