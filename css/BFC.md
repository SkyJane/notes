### 什么是BFC？
#### BFC的概念
BFC全称Block Formatting Context ，直译“块级格式化上下文”，也有译作“块级格式化范围”。

它是 W3C CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。通俗的讲，就是一个div内部，我们用float和margin布局元素。
#### BFC布局规则    
1. 内置的Box会在垂直方向，一个接一个的放置。
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。
   * 每个元素的margin box的左边，与包含块border box的左边相接触（对于从左往右的格式化，否则相反）。即使存在浮动也是如此。
   * BFC的区域不会与float box重叠。
   * BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也是如此。
   * 计算BFC的高度时，浮动元素也参与计算。

### 怎么产生BFC?
1. 根元素
2. float 属性不为 none
3. position 为 absolute 或 fixed
4. display 为 inline-block, table-cell, table-caption, flex, inline-flex
5. overflow 不为 visible

### BFC的作用及原理
#### 自适应布局
```
<style>
    body { width: 300px; position: relative; }
    .aside {
        width: 100px; height: 150px;
        float: left; background: #f66;
    }
.main { height: 200px; background: #fcc; }
</style>
<body> 
<div class="aside"></div> 
<div class="main"></div>
</body>
```
效果如下：

![Alt](../images/bfc1.png)

> 根据BFC布局规则第3条：每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

![Alt](../images/bfc2.png)

因此，虽然存在浮动的元素aslide，但main的左边依然会与包含块的左边相接触。

> 根据BFC布局规则第四条：BFC的区域不会与float box重叠。

因此，我们可以通过通过触发main生成BFC， 来实现自适应两栏布局。

当触发main生成BFC后，这个新的BFC不会与浮动的aside重叠。因此会根据包含块的宽度，和aside的宽度，自动变窄。
效果如下：

![Alt](../images/bfc3.png)

#### 清除内部浮动
```
<style>
.par { border: 5px solid #fcc; width: 300px; }
.child {
    border: 5px solid #f66;
    width:100px;
    height: 100px;
    float: left;
}
</style>
 
<body>
    <div class="par">
        <div class="child"></div>
        <div class="child"></div>
    </div>
</body>
```
效果如下：

![Alt](../images/bfc4.png)

> 根据BFC布局规则第六条：计算BFC的高度时，浮动元素也参与计算

为达到清除内部浮动，我们可以触发par生成BFC，那么par在计算高度时，par内部的浮动元素child也会参与计算。

代码：
```.par { overflow: hidden; } ```
效果如下：

![Alt](../images/bfc5.png)

#### 防止垂直 margin 重叠
代码：
```
<style>
    p {
        color: #f55;
        background: #fcc;
        width: 200px;
        line-height: 100px;
        text-align:center;
        margin: 100px;
}
</style>
<body>  <p>Haha</p> <p>Hehe</p> </body>
```
效果：

![Alt](../images/bfc6.png)

两个p之间的距离为100px，发送了margin重叠。

> 根据BFC布局规则第二条：Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。

我们可以在p外面包裹一层容器，并触发该容器生成一个BFC。那么两个P便不属于同一个BFC，就不会发生margin重叠了。

代码：
```
<style>
    .wrap {
        overflow: hidden;
    }
    p {
        color: #f55;
        background: #fcc;
        width: 200px;
        line-height: 100px;
        text-align:center;
        margin: 100px;
}
</style>
<body>
    <p>Haha</p>
    <div class="wrap">
       <p>Hehe</p>
    </div>
</body>
```
效果：

![Alt](../images/bfc7.png)

### 总结
BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

因为BFC内部的元素和外部的元素绝对不会互相影响，因此， 当BFC外部存在浮动时，它不应该影响BFC内部Box的布局，BFC会通过变窄，而不与浮动有重叠。同样的，当BFC内部有浮动时，为了不影响外部元素的布局，BFC计算高度时会包括浮动的高度。避免margin重叠也是这样的一个道理。 

BFC消除浮动的影响：```display: flow-root;```