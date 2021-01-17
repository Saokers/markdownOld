# CSS3

#### transition浏览器兼容问题

1、transition代表css3中的过渡，可以使元素从bai一种样式逐渐改变为另一种的效果。

2、transition: height 2s;表示需要渐变的是元素高度height,渐变时间是2s。transition还有其他参数如下图：

3、-moz-,-webkit-,-o-这三个是厂商前缀，不同浏览的厂商,因为不同浏览器有不同的标准，所以为了兼容性，需要把常用的浏览器对应的厂商前缀加上。所以四个属性代表的是一个意思。
-moz- 是火狐浏览器厂商前缀
-webkit- 是谷歌浏览器厂商前缀
-o- 是opera浏览器厂商前缀

# Element



# BootStrap

##### container 类用于固定宽度并支持响应式的容器

##### container-fluid  类用于 100% 宽度，占据全部视口（viewport）的容器。

## 栅格系统

栅格系统用于通过一系列的行（row）与列（column）的组合来创建页面布局，你的内容就可以放入这些创建好的布局中。下面就介绍一下 Bootstrap 栅格系统的工作原理：

- “行（row）”必须包含在 `.container` （固定宽度）或 `.container-fluid` （100% 宽度）中，以便为其赋予合适的排列（aligment）和内补（padding）。
- 通过“行（row）”在水平方向创建一组“列（column）”。
- 你的内容应当放置于“列（column）”内，并且，只有“列（column）”可以作为行（row）”的直接子元素。
- 类似 `.row` 和 `.col-xs-4` 这种预定义的类，可以用来快速创建栅格布局。Bootstrap 源码中定义的 mixin 也可以用来创建语义化的布局。
- 通过为“列（column）”设置 `padding` 属性，从而创建列与列之间的间隔（gutter）。通过为 `.row` 元素设置负值 `margin` 从而抵消掉为 `.container` 元素设置的 `padding`，也就间接为“行（row）”所包含的“列（column）”抵消掉了`padding`。
- 负值的 margin就是下面的示例为什么是向外突出的原因。在栅格列中的内容排成一行。
- 栅格系统中的列是通过指定1到12的值来表示其跨越的范围。例如，三个等宽的列可以使用三个 `.col-xs-4` 来创建。
- 如果一“行（row）”中包含了的“列（column）”大于 12，多余的“列（column）”所在的元素将被作为一个整体另起一行排列。
- 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-md-*` 栅格类适用于与屏幕宽度大于或等于分界点大小的设备 ， 并且针对小屏幕设备覆盖栅格类。 因此，在元素上应用任何 `.col-lg-*` 不存在， 也影响大屏幕设备。

通过研究后面的实例，可以将这些原理应用到你的代码中。

##  



```
<div class="row">
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
　　<div class="col-md-1">.col-md-1</div>
</div>
<div class="row">
　　<div class="col-md-8">.col-md-8</div>
　　<div class="col-md-4">.col-md-4</div>
</div>
<div class="row">
　　<div class="col-md-4">.col-md-4</div>
　　<div class="col-md-4">.col-md-4</div>
　　<div class="col-md-4">.col-md-4</div>
</div>
<div class="row">
　　<div class="col-md-6">.col-md-6</div>
　　<div class="col-md-6">.col-md-6</div>
</div>
上面这段是我从Bootstrap官网复制下来的，每一个“row”代表一行，而内部的“col-md-数字”代表一个单元格；
　　　　
　　　　Bootstrap把每一行分成12等份，“col-md-数字”中的“数字”从1-12中取，数字等于几，就占几份；

　　　　合理的选择单元格的数字配置，再往单元格中添加我们想要的内容，这样一个栅格系统就完成了！
```

