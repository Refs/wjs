# bootstrap_wjs项目实战

## bootstrap源码分析



### bootstrap字体图标

> glyphicons glyph:铭文（lol） icon:图标

> 出于性能的考虑，所有图标都需要一个**基类**和对应**每个图标的类**。把下面的代码放在任何地方都可以正常使用。`注意，为了设置正确的内补（padding），务必在图标和文本之间添加一个空格。`

#### 模仿bootstrap创建自定义的图标

```css
    /*bootstrap.css中*/
    @font-face {
        font-family: 'Glyphicons Halflings';

        src: url('../fonts/glyphicons-halflings-regular.eot');/*ie9+*/
        src: url('../fonts/glyphicons-halflings-regular.eot?#iefix') format('embedded-opentype')/*ie6-ie8*/, url('../fonts/glyphicons-halflings-regular.woff2') format('woff2'), /* chrome、firefox */ url('../fonts/glyphicons-halflings-regular.woff') format('woff'), url('../fonts/glyphicons-halflings-regular.ttf') format('truetype'), url('../fonts/glyphicons-halflings-regular.svg#glyphicons_halflingsregular') format('svg');
    }
    .glyphicon {
        position: relative;
        top: 1px;
        display: inline-block;
        font-family: 'Glyphicons Halflings';
        font-style: normal;
        font-weight: normal;
        line-height: 1;

        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }
    .glyphicon-asterisk:before {
        content: "\2a";
    }
```
* 引入图标，生成字体
    + 具体做法是利用@font-face, 

```css
    @font-face {
  font-family: 'wjs font';

  src: url('../fonts/MiFie-Web-Font.eot');
  src: url('../fonts/MiFie-Web-Font.eot?#iefix') format('embedded-opentype'), 
   url('../fonts/MiFie-Web-Font.woff') format('woff'), 
   url('../fonts/MiFie-Web-Font.ttf') format('truetype'), 
   url('../fonts/MiFie-Web-Font.svg#glyphicons_halflingsregular') format('svg');
}
```
* 创建一个基类，定义使用iconfont字体的的基本 样式（ex：bootstrap: glyphicon）如同常见的字体宋体与黑体的大小样式都不一样；

```css
.wjs_font {
  position: relative;
  top: 1px;
  display: inline-block;
  font-family: 'wjs font';
  font-style: normal;
  font-weight: normal;
  line-height: 1;

  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

* 定义每一个字体图标所对应的类 （ex:bootstap主页：glyphicon-home）
```css
    .wjs_phone::before{
        content: "\e908";
    }
    /*content的值对应的是svg的unicode,此处可以参照阿里图库的帮助文档*/
```

* 注意事项：
    + 不要与其它组件混合使用；即字体图标类不能与其它类在同一个元素上共同存在，一般做法是在元素内部嵌套一个span标签，然后将glyph类应用到span标签上；
    + 只对内容为空的元素起作用，图标类只能应用在不包括任何子元素与文本内容的元素上；

> 同一个网站我们可以引用多套不同的图标；第一步@font-face是font-family定义成不同的字体名，第二步基类引用响应的字体名，第三步与上面类似； 使用的时候一样； 这一点使用起来还是比较灵活的；

```css
    <div>
        /*使用bootstrap自带图标*/
        <span class='glyphicon glyphicon-asterrisk'></span>
        /*使用自定义图标*/
        <span class='wjs-font wjs-phone'></span>
    </div>
```

### boostrap项目的层次关系

* /css/*
    + /css/base.css 基础样式
    + /css/index.less less文件
    + /css/index.css 生成的css文件
* /font/* 自定义图标所在的文件夹，图标代码放在/css/base.css中
* /js/* 自定义的js代码

* lib目录 因为我们需要导入预定好的bootdtrap文件，为避免与自己的文件冲突，所以将bs文件放到lib目录中
    + /lib/css  /lib/js  /lib/fonts跟lib/css有相对路径关系，不可以随便去变动；

###  bootstrap使用组件修改样式

* 直接修改bootstrap.css的源文件（不建议）， 因为若修改源文件中某一个class重点额样式，页面中其它使用该class的标签样式会受到影响，即会存在一个覆盖问题；

* 为组件对应的html标签添加自己的class进行更改（不建议），因为bootstrap的组件嵌套关系比较复杂，虽然添加自己的class可以起到更改的作用，但需要找这个层次关系，比较麻烦；

* 建议用法：
    + 拷贝bootstrap的原生样式
    + 修改 拷贝出来的原生样式的类名
    + 在更改了类名的样式中修改css
    + 使用组件的地方，替换为自己的类名 
* 建议用法的好处：
    + 不会覆盖原生的css，由于使用的是自己的css所以不会造成覆盖问题；
    + 组件的层次结构即使再复杂也无所谓，因为我们是直接将原生的拷贝过来改，层次结构已经有了； 


### wjs轮播图分析

* wjs的轮播图鼠标放上去，会变成手的形状，且若用户感兴趣是可以通过点击进到里面去的；而bootstrap原生提供的轮播图组件是不可以点击的，其只是单纯的一张图片而已；

* 随着浏览器视口的改变，轮播图的图片内容向里面压缩高度始终不变，直接用自适应，宽度100%是不能做到这一步的；图片要做到缩放的时候不整体压缩，且要显示在中间，且要做到自适应，解决办法只有一个就是将图片当作背景去使用；

* 模仿bootstrap的less写法： 将公共的样式抽取为一个class，然后将特殊的样式单独抽取为一个class，然后在使用两个class的组合，进行less的书写；模仿这个书写习惯，因为bootstrap都是这样写的；

```css
.banner {
    /*抽取的公共样式*/
  .banner_a {
    height: 410px;
    /* 为了让中间的内容个保留 多余的部分 被自动切割 */
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    display: block;
    /*特殊样式与公共样式的交集组合写法*/
    &.image_a {
      background-image: url('../images/slide_01_2000x410.jpg');
    }
    &.image_b {
      background-image: url('../images/slide_02_2000x410.jpg');
    }
    &.image_c {
      background-image: url('../images/slide_03_2000x410.jpg');
    }
    &.image_d {
      background-image: url('../images/slide_04_2000x410.jpg');
    }
  }
}

```

* 上面是轮播图的默认版本，因为其高度是固定的，这在pc端使用自然是没有问题，但若放到移动端就会产生各式各样是问题，如ip5的高度是568px，若但轮播图就占了418px的高度，显然是不合理的；

* 理想的情况下是：在桌面端的视口中是一个非常完美的410px的高度（高度是固定的）；但是当我们将视口缩到足够小之后，其就变成一个自适应**（高度随着视口宽度进行同步的压缩）**，这样就不会出现屏幕分辨率小的手机轮播图占据整个屏幕的情况；
    + 具体做法是另作一套小图，专供小屏幕手机来使用，

* 现在同一个轮播图组件中要使用两套图片，并且这两套图片要在不同视口宽度下自动显示：实现这个效果主要是用到bootstrap的自适应工具hidden; 即平级放两个标签，上面一个标签放大图（背景），高度固定，视口大的时候显示，小的时候隐藏，下面的标签放小图，宽度100%，高度自适应，视口大的时候隐藏，视口小的时候显示，以此实现两套图片的使用；
 

### wjs信息区域分析 （改bootstrap媒体对象组件具体应用）

* 适配方向：
    + 最小屏幕隐藏
    + 大屏幕一行三项信息
    + 中等屏幕一行两项信息
    + 使用栅格进行布局
    + 信息的内容使用的是bootstrap的`媒体对象`组件
    + 当鼠标放到信息项上面之后，信息的文字会变色，且文字下面会出现下划线，即整个信息项应该都是被包裹在一个a标签下面；**但是如果这样做就会出现一个问题：a标签是不能嵌套的（a标签里面是不能嵌套a标签的，如果硬要写则a标签被浏览器解析出来的要不是嵌套关系，而是并列关系），**
    + 上面问题的解决思路是：bs中的媒体对象组件中a标签的默认属性如果是display:block（通过浏览器调试工具查看）,则组件中的a标签就可以用div来替换（组件中的样式是被加在类上面的，而不是加在标签上面，从视觉效果上看是没有任何问题的），如此整个组件就可以嵌套在a标签的里面； 
 
> 其实利用bs的自适应工具hidden与visible控制html元素的显示与隐藏的原理与使用css hack的原理类似；不同的是这是根据视口进行适配，而css hack是根据浏览器进行适配；
> 理解：bootstrap中组件的结构，一定要足够的复杂，因为只有足够复杂，才能够包揽各式各样的使用情景；假如自己在某种特定情形下用不到那么多，直接删某一部分就行了；但假如某一部分用到了，自己再加上去就麻烦了； 与其它在网上下载的插件类似，因为要考虑各种 


### wjs_点击切换（标签页）

* 组件中分为标签页导航与标签页内容两个部分，其中导航部分a标签的href值是与内容部分div的id属性值是关联了，即没增添一个导航，就得增加一个页，且上面两个值要一致；

* 组件中标签头与标签内容之间的‘点击切换关系’语气之间的层次没有任何关系，即两块区域是互相独立的，仅仅通过href与id关联，所以可以放置在任意不同的区域；

###  wjs_特别推荐页 优惠券分析

#### 优惠券布局分析


* 点击可以跳转，所以用a标签比较合适
* 优惠券分为左右两个部分，左或右定宽，另外一边自适应;
* 将浮动的html元素前置，避免浏览器自上到下解析造成的显示问题；
* 而此处的做法是 使用两个并列的div， 其中前一个div浮动，后面一个div overflow:hidden; 这样**由于之前的一个浮动元素，已经使用了整行中的一部分宽度，那么后一个元素就会使用后面的宽度，进行自适应布局**
* 自适应的盒子，内容布局使用栅格布局；

* 利用伪元素做优惠券上下的u型槽
* 利用字体图标做优惠券左上角“优惠的图案”，注意利用position:absolute脱标；此外由于该字体图标在定义字体时忘了定义图标的专有类（字体图标基类与图标项专有类），自己要手工加上；
* 优惠券上的进度条，利用bs组件，注意修改进度条的border-bottom 

### wjs_新闻

* 最左侧1根线右边的小圆圈，利用伪元素做，要加上背景，否则会出现线穿到圆圈中间的情况；右移伪元素的方法会出父元素的界；加背景是最稳的做法；

* 新闻中间div的做法
    + 主要用到的组件为bootstrap的标签页；
    + 圆中间的一条虚线利用一个空的height:100%的div做，利用绝对定位，定到中间；因为会存在线压盒子的问题，可以将线的div在html结构上挪到盒子上面，因为浏览器是从上向下渲染，这样后面的内容会将前面的内容压住，这是一个细节处理的好办法；
    + 标签页中的标签项(li)，原生是左浮动的，造成的结果就是标签项水平布置；而若想是水平排列的标签项垂直排列，好的方法是宽度100%;因为其宽度若盛满父盒子，其就会独占一行；
    + 标签页共有四项，若想让垂直排列的标签项，随着屏幕的变化变成水平布置，只需将各项宽度修改为25%（因为默认是左浮动）：这与上面的放在一起是一个技巧

```less
    @media screen and (max-width:1200px){
        wjs-newsTabs>li{
            width:25%;
        }
    }
```


## bs知识点

### .container 与 .container-fluid的区别

* 按照官方的说法：
    + .container 类用于固定宽度并支持响应式布局的容器
    + .container-fluid 类用于 100% 宽度，占据全部视口（viewport）的容器。

> 这里面的理解误区是： 所谓固定宽度是开发者可以设置该容器为固定宽度

* bootstrap.css中对.container的定义：

```css
.container{
    padding-right:15px;
    padding-left:15px;
    margin-right:auto;
    margin-left:auto
}

@media (min-width:768px){
    .container{
        width:750px
    }
}

@media (min-width:992px){
    .container{
        width:970px
    }
}

@media (min-width:1200px){
    .container{
        width:1170px
    }
}
        
```

> 所谓固定宽度并不是允许开发者自己设置容器的宽度，而是bootstrap内部根据屏幕宽度利用媒体查询，帮我们设置了固定宽度，并且是能够自适应的。所以，**无论何种情况下，请不要手动为响应式布局中的外层布局容器设置固定宽度值**

### 媒体对象

```css
    <div class="media">
        <a class="media-left" href="#">
            <img src="..." alt="...">
            /*若使用的不是图片而是字体图标
            <span class='glyphicon glyphicon-info'></span>
            */
        </a>
        <div class="media-body">
            <h4 class="media-heading">Media heading</h4>
            ...
        </div>
    </div>
```

### 栅格布局理解的误区

按照官方的说法：.row必须要放到.container或.container-fluid中；但官方并没有限制col-md或col-sm必须是放在.row中，这是自己理解的误区; 明白这一点之后，自己运用起来更自由；

```css
    <div class="wrapper">
        <div class=" col-md-6">
            我是left
        </div>
        <div class="col-md-6">
            我是right
        </div>
    </div>
```

### bs使用总结

* 默认能够使用的直接使用即可；
* html标签组成不对，审查元素进行删减；
* 样式不对：
    + 找到bs中 该组件的所有类名（层次结构）
    + 拷贝并替换类名
    + 修改为自己的组件类名
    + 进行样式修改

> 为什么bootstrap会将组件分到一块一块，而老师做项目的时候又将页面分为一块一块的写，就是为了告诉大家在实际做网站的时候，一定要将网站拆开来写；当我们将每一块都用一个大盒子包裹起来，则在我们最终拼合的时候就会非常的简单；
> 利用bootstrap去写响应网站，本质上是利用了媒体查询，公司若要求不使用bs ,则我们就需要使用媒体查询；其二者就类似于手动挡与自动挡的区别；

## html知识点

### a标签

> a标签的内部不能嵌套a标签，浏览器会将其解析成兄弟并列关系，即原有的嵌套关系会失效；



## css3知识点

* box-sizing: border-box的理解，当padding的值变化时，正常情况下，盒子的宽高是不会发生变化的，当但padding 的取值足够大时，盒子还是会被撑大的（即虽然我们想保持盒子的宽高不变，但若内容盛不下，盒子还是会被撑大），这一点可以用浏览器去调试；

* nth-child(n)与nth-of-type(n)的区别
    + nth-child(n)与nth-of-type(n)都可以选择父元素下的子元素；
    + ele:nth-child(n)指的是选择ele所在的父元素的第n个子元素，且子元素必须为ele否则选择失效；
    + ele:nth-of-type(n)指的是选择ele所在父元素下第n个type为ele的子元素；

```html
    <div class="wrapper">
        <ul>
            <p>title</p>
            <li>1</li>
            <li>2</li>
            <li>3</li>
        </ul>
    </div>
    <!--选中li{1}-->
     .wrapper ul li:nth-child(2){
            background-color: gray;
        }
    <!--选中li{2}-->
    .wrapper ul li:nth-of-type(2){
            background-color: #fff;
        }
```
* 阴影复习
    + 阴影是属于边框类的属性 box-shadow
    + <shadow> = inset? && <length>{2,4} && <color>?
    + inset为内阴影，不写则默认使用外阴影
    + length
        - length1: 水平偏移值
        - length2: 垂直偏移值
        - length3: 阴影模糊值 
        - length4: 阴影外延值 即盒子阴影以左上为原点进行缩放；
        - 实际使用中直接 0 0 3px 6px即可；

>使用model与使用原生的编码体验： bootstrap就是一个框架，与其它的框架一样，其是一块一块的，而每一块都封装了某一方向或某一功能的具体逻辑，类似于mvc中的model层，漆在封装的过程中上层开放了几个接口，而我们拿这几个接口区做事情，这一过程相当于mvc中的控制层过程；利用原生的东西写代码类似于我们开手动挡的车，利用框架写代码类似于我们开自动挡的车；其两者的区别是我们利用框架写代码就没必要去关注model层了，而利用原生写代码我们还要去自己封装model层；

> 即自己若利用框架写东西应该很随意，因为其model层都已经帮我们封装好了；这样写着就会很舒服；体验这一点关键是能有mvc的认知与感觉；

> 每个项目类似于之前做的数学题目，都有一个统一的大的做题模板，而除了这个大板子之外，还有很多的细节；

> 境界都是在每一次实战中提升的，不要认为某一项技能在改项目上用不到，尽量尝试着去运用，如boostrap中的less文件结构，自己在学习less的时候，应尽量尝试着去运用；用几遍就熟悉了；做项目本身不是目的，目的是练习；

> bootstrap的组件化编程思想：  bootstrap会将一组html层次结构，封装成一个组件；在页面需要的时候，随时就可以将组件引入，这一点其实类似于javsscript中的函数与less中的mixin; 自己在编程的时候也应该有这种意识，在编程的时候，思想向着上面去靠；如自己做得优惠券：这个层次结构，就可以反复被使用；

```html
<div class="wjs_coupon">
        <a href="">
            <div class="coupon_right">
                <p>8%</p>
                <p>年利率</p>
            </div>
            <div class="coupon_left">
                <span class="wjs_font wjs_E915"></span>
                <h3>新手体验标1002期</h3>
                <div class="col-xs-6">
                    <p>起投资金</p>
                    <p>0.01万</p>
                </div>
                <div class="col-xs-6">
                    <p>起投资金</p>
                    <p>0.01万</p>
                </div>
                <div class="col-xs-6">
                    <p>起投资金</p>
                    <p>0.01万</p>
                </div>
                <div class="col-xs-6">
                    <p>起投资金</p>
                    <p>0.01万</p>
                </div>
            </div>
        </a>
    </div>
```

<!--若想保证网页的兼容性，其中之一的办法是ie6做一套，-->

目的是做项目，若项目有这种需求，相关的知识点就会自动被聚合；现在自己对css hack与<!--[if lt IE 9]>ie注释没有认知，原因在于当前的项目中，没有这种需求，自己也没有聚合过，简而言之就是自己没有练习过；