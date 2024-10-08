Flexbox叫弹性盒模型，它的使用场景主要是屏幕自适应布局和取代浮动布局。

细节性的知识需要大量实践，系统性的知识则需要真正理解系统。我认为Flexbox就属于系统性的知识。所以这篇文章从概念入手，力求做到只要阅读一遍，就可以让开发者心中有乾坤。

## 一维布局模型

你他妈可能是`三体`看多了吧，啥叫一维布局模型？

一维布局模型，简单讲就是，在`主轴`方向确定的情况下，只有`行`，没有`列`。

我们熟悉的二维布局模型有哪些呢？有`display: table`和`display: grid`。而我们今天要讲的`display: flex`就是妥妥的一维布局模型了。

直接看图。

![illustration](/flex/image/01.png)

可以看到，Flexbox有`行`的概念，却没有`列`的概念。这导致它的表达能力是受限的。

## 概念

我们如何确定一条短线是中文的`一`还是阿拉伯数字的`1`呢？

你可能觉得我在搞笑。一眼看过去，横向的不就是中文的`一`，纵向的不就是阿拉伯数字的`1`么。

是的。那是因为我们遵循着一些默认的约定：两眼连成的线确定了水平方向。

Flexbox也是这个道理。它是一个一维布局模型，我们就要找到确定仅有的维度的那根线。

这就引出了Flexbox的第一个概念：主轴(mian axis)与交叉轴(cross axis)。主轴就是那根仅有的维度线。两眼连成的线与主轴方向保持平行，这就是肉眼看待Flexbox的正确方式。否则有时候它会显得很别扭。

然后我们再来说弹性盒子。既然它是一个盒子，那肯定得有容器，也得有内容。

也就引出了Flexbox的第二个概念：弹性容器(flex container)与弹性项目(flex item)。

你可以将弹性容器理解为冷战时期的柏林，各方国际政治势力自然就是若干弹性项目了。几个国际大流氓聚在一起开会讨论什么呢？当然是开会讨论如何瓜分柏林咯。是的，弹性盒模型讨论的就是弹性项目如何瓜分弹性容器这一温馨的话题。

上一小节我们提到过，Flexbox是一维布局模型。它带来的结果就是Flexbox有`行`的概念，却没有`列`的概念。

所以引出Flexbox的第三个概念：行(line)。

直接看图。

![illustration](/flex/image/02.png)

## 最小长度

如果弹性容器有富余空间，那好说，大家分就是了。而如果弹性容器空间不够，弹性项目不仅没得分，大家还得挤一挤。那么问题就来了，挤不是无限制的挤，咱们就来探讨一下，挤到什么程度，是可忍孰不可忍？

举个例子。

```css
.container {
    display: flex;
    width: 850px;
    padding: 5px;
}
.container .item {
    width: 200px;
    height: 50px;
    margin: 5px;
}
.container .item:nth-child(2) {
    width: 1000px;
}
```

![illustration](/flex/image/03.png)

两个弹性项目的长度加起来的和已经超过了弹性容器，所以不得不挤压。挤压的比率咱们先不考虑，咱们先观察挤压的方式。有没有发现红色部分都有不同程度的收缩，但是黄色部分却岿然不动？

黄色部分是什么？是弹性容器的`padding`和弹性项目的`margin`。盒子模型咱们都了解吧，除了`padding`、`margin`和`border`之外，是不是只剩`content`了？

咱们可以大胆猜测，Flexbox只敢欺负盒子模型的`content`，其余都是大爷，惹不起。

别急，再来验证一下。

```css
.container {
    display: flex;
    width: 850px;
    padding: 5px;
}
.container .item {
    width: 200px;
    height: 50px;
    margin: 5px;
}
.container .item:nth-child(2) {
    width: 1000px;
    padding: 0 450px;
}
```

![illustration](/flex/image/04.png)

这下是不是很清楚了？第一个弹性项目的`content`长度已经变成了0；第二个也好不到哪去，因为盒子长度都被`padding`占据，它的`content`长度实际上也是0。都把人家挤破产了，却丝毫不敢动其他的属性，势利眼无疑了。

我们再来看一个有意思的例子。

```css
.container {
    display: flex;
    width: 850px;
    padding: 5px;
}
.container .item {
    width: 200px;
    height: 50px;
    margin: 5px;
}
.container .item:nth-child(2) {
    width: 1000px;
    padding: 0 300px;
}
.container .item .inner {
    width: 500px;
    height: 100%;
}
```

![illustration](/flex/image/05.png)

我在第二个弹性项目中放了一个长`500px`的元素，结果你猜怎么着，弹性项目的`padding`竟然有一部分和元素重合了。连子元素都未能幸免。

一般来说盒子模型的`content`都是被文字撑开的，我们最后再来看看文字的情况。

![illustration](/flex/image/06.png)

在弹性项目显式设置了宽度的情况下，弹性项目并不能完全包裹文字。也就是说文字也帮不了它，既然它声明了宽度，文字撑开的长度最多不能超过显式声明的宽度，超出的文字只能溢出。

![illustration](/flex/image/07.png)

而没有显式声明宽度的情况，文字的宽度就是弹性项目盒子模型的`content`，Flexbox也拿它没办法。

> 总结一下：当富余空间不够时，Flexbox只会挤压弹性项目的`content`，其余如`padding` 、`margin`和`border`完全不受影响。同时弹性项目没有显式声明宽度的情况下，Flexbox也不会挤压文字。

## display

从这里开始，我们就要讲具体的CSS属性了。

`display`有两个和Flexbox相关的属性，分别是`display: flex`和`display: inline-flex`。

对于容器内部的项目来说，效果是一样的。它们的区别在于容器自身应该以块元素还是行内元素的身份立命。包括`table`也有这样的区分，就不多讲了。

## flex-direction

这个属性声明的是主轴的方位和方向。

首先，主轴可不一定是水平的，主轴切换了那可就什么都变了。

其次，主轴声明的信息有两个，分别是：

- 它是水平的还是垂直的？
- 它在水平或垂直维度上是从左到右还是从右到左？

所以这里也涉及到四个属性值。

```css
.container {
    flex-direction: row(default) | row-reverse | column | column-reverse;
}
```

![illustration](/flex/image/08.png)

## flex-wrap

这个属性声明的是当容器中的项目一行放不下的时候，是让大家挤一挤呢，还是换行。

其实这里也包含两个信息：

- 要不要换行？
- 如果需要换行就会形成多行，多行是从上到下排列还是从下到上排列？

```css
.container {
    flex-wrap: nowrap(default) | wrap | wrap-reverse;
}
```

![illustration](/flex/image/09.png)

如果把`flex-direction`和`flex-wrap`结合起来，大家会不会懵逼？上上下下左左右右。其实不管它怎么reverse，`flex-direction`反转的是主轴的方向，`flex-wrap`反转的交叉轴的方向。

抓住一些概念性的东西，就不会懵逼了。

## flex-flow

这是一个集合属性，可以同时定义`flex-direction`和`flex-wrap`。

也就是说，这一个属性可以一站式声明主轴和交叉轴的特性。

```css
.container {
    flex-flow: row-reverse wrap-reverse;
}
```

## justify-content

这个属性声明每行内的项目如何水平对齐。

把弹性容器一行内的项目想象成一行行内元素，`justify-content`和`text-align`的食用方式是一样的。

```css
.container {
    justify-content: flex-start(default) | flex-end | center | space-between | space-around | space-evenly;
}
```

![illustration](/flex/image/10.png)

`flex-start`、`flex-end`和`center`非常表意，咱们按下不表。

解释一下后面三个属性值：

- `space-between`表示两头的项目对齐容器壁，项目与项目之间的空隙平均分配。所谓的`between`指的就是项目之间。
- `space-around`表示两头的项目与容器壁保留一个单位的空隙，项目与项目之间保持两个单位的空隙。`around`翻译成中文是`周围`，指的就是每个项目左右两边的空隙平均分配。
- `space-evenly`表示两头的项目与容器壁之间的空隙和项目与项目之间的空隙都保持一个单位。`evenly`翻译成中文是`均匀`，指的是所有空隙平均分配。

## align-items

这个属性声明每行内的项目如何垂直对齐。

```css
.container {
    align-items: stretch(default) | flex-start | flex-end | center | baseline;
}
```

![illustration](/flex/image/11.png)

这里有一个问题，如果弹性项目显式的声明了高度，那`stretch`将不再起作用。所以这里的例子，我往项目中加了一个子元素，把高度显式的声明在子元素上，这样项目的高度就是被撑开的。

```html
<div class="container">
    <div class="item"><div class="inner">1</div></div>
    <div class="item"><div class="inner">2</div></div>
    <div class="item"><div class="inner">3</div></div>
</div>
```

可以看到，默认情况下，行内的弹性项目会拉伸，相当于声明了高度`100%`。一旦项目显式的声明了高度，拉伸就不再起作用了。

## align-content

这个属性将容器的一行视为最小单位。它声明的是如果容器的交叉轴方向有富余空间，每行应该如何垂直对齐。

```css
.container {
    align-content: stretch(default) | flex-start | flex-end | center | space-between | space-around | space-evenly;
}
```

![illustration](/flex/image/12.png)

这里也一样，如果弹性项目显式的声明了高度，那`stretch`将不再起作用。

同时有两点需要注意：

- 弹性容器需要显式的声明高度，而且高度必须高于所有行的高度和，否则去哪来的富余空间呢？
- 如果容器内只有一行，也就是说不需要换行或者不允许换行，那`align-content`属性将无法生效，因为它是作用于行的，只有一行也敢叫老子跑一趟？

## 为什么没有`justify-items`

如果你同时了解`display: flex`和`display: grid`，也许你会发现它们都有`xx-items`和`xx-content`属性。但是别急，再进一步深究，发现Flexbox少了一个`justify-items`。

我们先来阐述一下`xx-items`和`xx-content`作用范围有什么区别。

- `xx-items`作用于项目，这意思很明朗。
- `xx-content`又是作用于什么呢？肯定是比项目更大的单位，又联想Flexbox的`align-content`是作用于行，我们可以大胆猜测`xx-content`作用于行或者列。

点破到这里，大家应该有点眉目了吧？Flexbox是一维布局模型，它根本没有列的概念。

你说不对呀，既然Flexbox没有列的概念，那不是应该没有`justify-content`属性，而应该有`justify-items`属性么？

话是这么说，我当初也是这么认为的。

但后来仔细想一想，Flexbox的`align-items`声明的是一行内的项目如何垂直对齐，与之相对，`justify-items`声明的就应该是一列内的项目如何水平对齐。好像更离谱。而如果将弹性容器一行内的每个项目都当做一列，`justify-content`似乎就说的通了。

这就好比讨论螃蟹的螯到底是不是手一样，怎么都觉得别扭。只能找一个相对更合理的说法了。

提出这个问题没别的意思，只是想加深大家对Flexbox的理解。

## order

从这里开始，涉及到的属性都是弹性项目自身的属性。在大的格局确定的情况下，项目之间也是可以有一些腾挪空间的。

这个属性声明的是弹性项目自身的次序。只要显式声明了不是默认值0的整数，项目显示的次序将会不同于源代码定义的次序。

这个主要是留给JavaScript动态控制项目的次序用的，非动态直接修改源代码的次序就好了。

```css
.item {
    order: <integer>; /* default is 0 */
}
```

![illustration](/flex/image/13.png)

## flex-grow

这个属性声明的是弹性项目是否要瓜分行内的富余空间，以及如何瓜分。

属性值只允许正整数。

```css
.item {
    flex-grow: <number>; /* default is 0 */
}
```

![illustration](/flex/image/14.png)

首先解释一下什么是富余空间：它是在弹性容器规则和弹性项目显式宽度或者内容的共同约束下，行内剩余的水平空间。比如图例中除去`margin`、`padding`的黄色部分。

行内的富余空间是如何被瓜分的呢？

首先，弹性项目要提出申请。比如第一个项目提出`flex-grow: 1`，意思是说它想要一份富余空间。至于一份富余空间是多少像素，目前还不能确定。

等所有项目都申请完毕，计算申请的总份数。已知富余空间长度和申请总份数，就能知道一份富余空间是多少像素。

最后根据每人提出申请的份数，分配富余空间。

> 只有当行内有富余空间时，`flex-grow`属性才会生效。行内空间已经预先被瓜分完甚至不够时，该属性就管不了了。

## flex-shrink

这个属性声明的是弹性项目是否要瓜分行内的负债空间，以及如何瓜分。

属性值只允许正整数。

```css
.item {
    flex-shrink: <number>; /* default is 1 */
}
```

![illustration](/flex/image/15.png)

同样，解释一下什么是负债空间：它是在弹性容器规则和弹性项目显式宽度或者内容的共同约束下，行内短缺的水平空间。此时如果不换行的话，就要求挤压弹性项目的长度。

行内的负债空间的瓜分规则与富余空间的瓜分规则大致相同。

首先，弹性项目要提出申请。只不过这时的份额就不再是我要多少，而是我还多少。

等所有项目都申请完毕，计算申请的总份数。

这里的问题在于，计算负债空间的长度稍微比较复杂。我们在文章一开始探讨过弹性项目最小长度的话题，这里再次总结一下：

- 弹性容器只会挤压弹性项目的`content`，其余如`padding`、`margin`和`border`不受影响。
- 如果弹性项目内有文本或者固定宽度的子元素，这又分两种情况。第一种是项目本身没有显式声明宽度，则最小长度以子元素的长度为准；第二种是项目本身显式声明了宽度，则最小长度以子元素的长度为准，但不能超过项目本身的宽度。结合上面图例中最后两个例子，更容易理解(我知道你已经懵逼了)。

最后根据每人提出申请的份数，分配负债空间。就是还债。

> 为什么`flex-grow`属性的默认值是0，而`flex-shrink`属性的默认值是1呢？
>
> 因为默认情况下，如果有富余空间我可以不要的，但是有负债空间又无法换行的话，我不得不要。所以`flex-shrink`属性的默认值是1，意思就是默认情况下，如果空间不够则大家平均的被挤压。
>
> 你可以将所有项目的`flex-shrink`属性值设置为0，如此这般所有项目都铁骨铮铮、不畏强权了。见上面图例的第四个例子。

## flex-basis

这个属性声明的是预先分配给弹性项目的长度。它是`width`属性的替代品，优先级比`width`高。

```css
.item {
    flex-basis: <length> | auto; /* default is auto */
}
```

![illustration](/flex/image/16.png)

如果`width`和`flex-basis`都显式的声明了一个非`auto`的值，那么`flex-basis`的优先级更高。否则，哪个显式声明了就以哪个为准。

确实不太清楚制定`flex-basis`属性标准的意义何在。

它们的区别好像仅限于属性值为0的情况。`width: 0`我们都知道表示没有宽度，见上面图例的第二个例子；而`flex-basis: 0`表示以内容的宽度为宽度，见上面图例中的第三个例子。

## flex

这是一个集合属性，可以同时定义`flex-grow`、`flex-shrink`和`flex-basis`。

你可以集合三个属性的值，也可以只写`flex-grow`一个属性的值。

```css
.item {
    flex: <'flex-grow'> | <'flex-grow'> <'flex-shrink'> <'flex-basis'>;
}
```

## align-self

这个属性声明的是弹性项目自身在行内的垂直对齐方式。

“我就是我，是颜色不一样的烟火”。

```css
.item {
    align-self: auto(default) | stretch | flex-start | flex-end | center | baseline;
}
```

![illustration](/flex/image/17.png)

除了`auto`之外，`align-self`的属性值和`align-items`的属性值是一样的，效果也一样。

`align-items: auto`是说我默认服从集体，不自搞一套，所以才会多这么个属性值。

> 为什么有`align-self`属性而没有`justify-self`属性呢？
>
> 这个问题我们讨论过吧？因为没有`jusifty-items`属性，所以`justify-self`属性也无从谈起。
>
> 你看人家Grid就有`justify-self`属性。

## 其他

有一个小游戏 [Flexbox Froggy](https://flexboxfroggy.com/) 可以帮助你轻松的实践Flexbox的各项特性。
