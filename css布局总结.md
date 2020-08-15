## css布局
css布局还是有很多细节的，比如以下的一部分：
* BFC
* display、position、float的联系
* 清除浮动
* 水平居中、垂直居中等
* 常见的css布局
* flex和grid布局

### BFC-块级格式化上下文
BFC是一个独立布局环境，内部元素不受外部元素的影响，外部元素也不会影响到内部元素，产生了一个隔离的块。<br>
> 如何创建BFC：
1. 根元素或包含根元素的元素
2. 浮动元素float = left | right | inherit（≠none）
3. 绝对定位元素position = absolute | fixed
4. display = inline-block | flex | inline-flex | table-cell | table-caption
5. overflow = hidden | auto | scroll(≠visible)

> BFC的特性：
* BFC区域不会和float box 发生重叠
* BFC能够识别并包含浮动元素，当计算其区域的高度时，浮动元素也可以参与计算

> BFC的作用：
* 在容器中创建BFC，清除浮动
* BFC中的元素margin会重叠
* 不同BFC的margin不会重叠

*** 

### display、position、float的联系
```
display:
    block|inline|inline-block|table|flex|grid
position:
    relative|absolute|static|fixed|sticky
float:
    left|right|none

三者之间可以理解为有一种优先级的关系存在
1. 首先，如果display：none，元素不显示，position和float不影响
2. 然后，如果position：fixed|absolute，则float变为none，display改变为table或block
3. 然后，如果float不为none，则该元素浮动并且display改变为table或block
4. 然后，如果元素是根元素，则display还是按照上面规则改变，如果不是，则display保持不变。
```

*** 

### 清除浮动
> 为什么需要清楚浮动：浮动元素脱离了原本的文档流，会影响内联元素的布局，不影响块级元素的布局，当包含框的高度小于浮动元素时，会出现高度坍塌。清除浮动就是为了避免这种情况。

清除浮动的方式：<br>
1. 使用clear属性(伪元素、空标签)
```
.clear::after{
    content:'';
    display:block;//也可以是'table\list-item'
    clear:both;
}
//clear属性只有块级元素才有效的，而::after等伪元素默认都是内联水平，这就是借助伪元素清除浮动影响时需要设置display属性值的原因。

``` 
2. 在父级元素创建BFC
```
//父级元素
overflow:hidden 会遮挡多出的内容

zoom：1 （兼容ie6）触发haslayout属性
```
3. 父级元素定义高度,能够覆盖住浮动元素
4. 父级元素也使用float

*** 

### 水平居中、垂直居中
> 水平居中
```
* 行内元素：text-align:center
* 块级元素（width确定）：
    ** margin：0 auto
    ** {    //绝对定位 + margin-left：负宽度/2
        position:absolute;
        left:50%;
        margin-left:-width/2
        }
* 块级元素（不确定）
    ** display：table + margin: auto
    ** display:inline-block + text-align:center
    ** {    //绝对定位+transform
        position:absolute;
        left:50%;
        transform:translateX(-50%);
    } 
    ** {    //flex布局
        display:flex;
        justify-content:center;
    }
```

> 垂直居中
```
* line-height: 适合文字内容垂直居中
* 跟水平居中类似：使用绝对定位 + margin 实现垂直居中
* 同样使用 绝对定位 + transform 实现垂直居中
* 父级元素设置display：table + 子元素设置 display：table-cell vertical-align：middle。注意：vertical-align只适合行内元素（inline）或表格单元格（table-cell）元素。
* flex布局： {
            display:flex;
            align:-items:center;
        }
```

> 垂直水平居中
```
* position + margin（宽高确定）：
父元素{
    position:relative;
}
子元素{
    width:100px;
    height:100px;
    position:absolute;
    left:50%;
    top:50%;
    margin-left:-50px;
    margin-top:-50px;
}

* 绝对定位 + 各个方向为0 + margin：auto（宽高确定）
父元素{
    positon:relative;
}
子元素{
    width:100px;
    height:100px;
    position:absolute;
    top:0;
    bottom:0;
    left:0;
    right:0;
    margin:auto;
}

* position + transform (宽高未知)
父元素{
    position:relative;
} 
子元素{
    position:absolute;
    left:50%;
    top:50%;
    transfrom:translate(-50%,-50%);
}

* flex布局
{
    display:flex;
    justify-content:center;
    align-items:center;
}
```

*** 

### 常见的css布局
> 两列布局：左边固定，右边自适应
```
1. 左元素浮动 + 右元素 margin-left
.left{
    float:left:
    width:200px;
    background:red;
}
.right{
    margin-left:200px;
    width:auto;     //默认值为width，填满整个空间
    background:blue;
}

2. 左元素绝对定位 + 右元素margin-left
.left{
    width:200px;
    position:absolute;
    background:red;
}
.right{
    margin-left:200px;
    background:blue;
}

3. 右元素绝对定位 
.left{
    width:200px;
    background:red;
}
.right{
    position:absolute;
    left:200px;
    top:0;
    right:0;
    bottom:0;
    background:blue;
}

4. flex 布局
.parent{
    display:flex;
} 
.left{
    flex:0 0 200px;
    background:red;
}
.right{
    flex:auto;  // 1 1 auto
    background:blue;
}

5. grid 布局 
.parent{
    display:grid;
    grid-template-columns:200px 1fr;
}
.left{
    background:red;
}
.right{
    background:blue;
}
```

> 三列布局：
```
1. 左右float + 中间margin 。注意：中间一栏需要放到left和right的html元素后，不然会抢占右元素的位置。
.parent{
    height:100px;
}
.left{
    float:left;
    width:200px;
    height:100px;
    background:red;
}
.right{
    float:right;
    width:200px;
    height:100px;
    background:blue;
}
.middle{
    height:100px;
    margin:0 200px;
    background:green;
}

2. 左右绝对定位 + 中间margin
.parent{
    position:relative;
    height:100px;
}
.left{
    position:absolute;
    width:200px;
    height:100px;
    background:red;
}
.right{
    position:absolute;
    width:200px;
    height:100px;
    right:0;
    background:blue;
}
.middle{
    height:100px;
    margin:0 200px;
    background:green;
}

3. flex 布局。html元素需要顺序摆放
.parent{
    height:100px;
    display:flex;
}
.left{
    height:100px;
    flex:0 0 200px;
    background:red;
}
.right{
    height:100px;
    flex: 0 0 200px;
    background:blue;
}
.middle{
    flex:auto;
    height:100px;
    background:green;
}

4. grid 布局。html元素需要顺序摆放
.parent{
    height:100px;
    display:grid;
    grid-template-columns:200px 1fr 200px;
}
.left{
    height:100px;
    background:red;
}
.right{
    height:100px;
    background:blue;
}
.middle{
    height:100px;
    background:green;
}

5. 圣杯布局。注意：middle部分的html需要在left和right的前面。

* left、right、middle都为float。middle的width：100%，自适应
* left、right的margin-left为负值，回到middle的同一行
* 通过设置parent的padding-left和padding-right空出空间
* 通过相对定位让left和right移动到两边。
* 注意：middle部分的宽度不能小于left，不然left和right会掉到下一行

.parent{
    height:100px;
    padding:0 220px;
}
.left{
    float:left;
    width:200px;
    height:100px;
    margin-left:-100%;
    position:relative;
    left:-220px;
    background:red;
}
.right{
    float: left;
    width:200px;
    height:100px;
    margin-left:-200px;
    position:relative;
    right:-220px;
    background:blue;
}
.middle{
    height:100px;
    background:green;
    width:100%;
    float:left;
}

6. 双飞翼布局。在圣杯布局的基础上进行了一定的优化,左右位置的保留是通过中间列的margin来实现，而不是父元素的padding，本质上，也是通过浮动和外边距负值来实现的。
** html 实现
<div class="parent">
    <div class="middle">
        <div class="inner">middle</div>
    </div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
** css 实现
.parent{
    height:100px;
}
.left{
    float:left;
    margin-left:-100%;
    width:200px;
    height:100px;
    background:red;
}
.right{
    float:left;
    margin-left:-200px;
    height:100px;
    width:200px;
    background:blue;
}
.middle{
    float:left;
    width:100%;
    height:100px;
    background:green;
}
.middle .inner{
    margin:0 200px;
}
```

*** 

### flex布局和grid布局
flex布局和gird布局都是很强大的布局，可以应对很多需求。<br>

> flex布局
采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。

```
容器的属性：
* flex-direction：row|row-reverse|column|column-reverse 用来决定主轴的方向。
* flex-wrap：nowrap|wrap|wrap-reverse 决定换行的方式
* flex-flow：<flex-direction>||<flex-wrap> 是flex-direction和flex-wrap的简写，默认值为row nowrap
* justify-content：flex-start|flex-end|ce nter|space-between|space-around 决定了项目在主轴上的对齐方式
* align-items：flex-start|flex-end|center|baseline|stretch 决定了项目在交叉轴上的对齐方式
* align-content：flex-start|flex-end|center|baseline|stretch 决定了多条轴线的对齐方式，如果只有一条轴线，则不起作用

项目的属性：
* order：<integer> 定义了项目的排列顺序，数值越小越靠前
* flex-grow：<number> 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
* flex-shrink：<number> 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
* flex-basis:<length>|auto 定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
* flex: none|[<'flex-grow'><'flex-shrink'>?||<'flex-basis'>] 是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。即默认不放大，会缩小，原本大小。有快捷值：auto (1 1 auto) 和 none (0 0 auto)。
* align-self：auto|flex-start|flex-end|center|baseline|stretch 允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch
```

> grid布局
跟flex布局类似，采用网格布局的区域，称为"容器"（container）。容器内部采用网格定位的子元素，称为"项目"（item）。

```
对行和列、单元格、网格线有基本了解

容器属性：
* display：grid|inline-gird 注意，设为网格布局以后，容器子元素（项目）的float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效。
* grid-template-columns|grid-template-rows: 指定行和列的数目和宽高

** 这里补充一些关键字使得设置columns和rows时更加方便：
** repeat(<number>,要重复的模式)，例如：repeat(2,50px),repeat(3,20px 30px 50px)
** auto-fill：有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用auto-fill关键字表示自动填充。
** fr：表示剩余空间的比列关系，例如：grid-template-rows:50px 1fr 2fr
** minmax() 确定一个范围，例如：minmax(50px,1fr)表示列宽在50px到1fr之间
** auto： 有浏览器自己决定
** 自定义网格线名称：grid-template-columns属性和grid-template-rows属性里面，还可以使用方括号，指定每一根网格线的名字，方便以后的引用。例如：grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];并且允许同一根线有多个名字，比如[fifth-line row-5]。

* row-gap：用于设置行的间距。
* column-gap ：用于设置列的间隔。
* grid-gap: <grid-row-gap> <grid-column-gap> 简写，第二个值不存在时，使用第一个值。

* grid-template-areas: 用于指定grid中的区域，如果某些区域不需要，可以使用“.”来表示。

* grid-auto-flow：row|column|row-dense|row-column 按照顺序自动放置网格，默认是“先行后列”。row-dense和column-dense 用于尽可能填满，不出现空格。

* justify-items：start|end|center|stretch 设置单元格内容的水平位置
* align-items：start|end|center|stretch 设置单元格内容的垂直位置
* place-items： <align-items> <justify-items> 简写，第二个值不存在时，使用第一个值。

* grid-auto-columns/gird-auto-rows: 跟grid-template-columns和gird-template-rows用法一致。当制定项目在超出网格的位置时，浏览器会自动生成多余的网格来进行放置，如果不指定这两个属性，则浏览器根据单元格内容的大小决定宽高

* grid-template属性是grid-template-columns、grid-template-rows和grid-template-areas这三个属性的合并简写形式。为了易读易写，不建议使用。
* grid属性是grid-template-rows、grid-template-columns、grid-template-areas、 grid-auto-rows、grid-auto-columns、grid-auto-flow这六个属性的合并简写形式。为了易读易写，不建议使用。


项目属性：

* grid-column-start：左边框所在的垂直网格线
* gird-column-end: 右边框所在的垂直网格线
* grid-row-start: 上边框所在的水平网格线
* grid-row-end: 下边框所在的水平网格线
* grid-column：<start-line> / <end-line>;简写。
* grid-row：<start-line> / <end-line>;简写。

* grid-area：指定项目放在哪一个区域，跟容器属性中的grid-template-area相联系。

* justify-self：start|end|center|stretch 设置单元格内容的水平位置（左中右）
* align-self: start|end|center|stretch 设置单元格内容的垂直位置（上中下）
* place-self：<align-self><justify-self> 简写。
```


*** 

### 参考：
<a href='http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html'>CSS Grid 网格布局教程</a>
<a href='http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html'>Flex 布局教程：语法篇</a>
<a href='https://www.cnblogs.com/jackyWHJ/p/3756087.html'>position跟display、margin collapse、overflow、float这些特性相互叠加后会怎么样？</a>
<a href='https://juejin.im/post/6844904031769329678#heading-31'>重整旗鼓，2019自结前端面试小册【CSS + HTML】</a>
<a href='https://github.com/CavsZhouyou/Front-End-Interview-Notebook/blob/master/Css/Css.md'>CSS 面试知识点总结</a>

