cssAnimation
============

一个测试CSS不同方式制作动画的性能问题示例程序。观看效果链接 http://1.cssanimation.sinaapp.com/ 


目前WEB页面做动画的方式大的分两种
1.JS间隔时间不断修改元素属性值，这也是CSS3出来前常用的做法，貌似也是唯一的做法。
2.CSS3出来之后支持动画了。

大体的思路分这两种。要是各样功能组合细分就可以分出许多类来。本文主要介绍WEB页面做动画的一些方式以及性能分析。

我们的例子界面如下。
1.用好多个小方块的移动来测试不同方式动画的性能。
2.右边几个功能按钮。其中有个阻塞按钮，利用一个空循环阻塞主线程来测试不同效果。
3.画面主要是许多个小方块无规则的平移，没有使用其他类似缩放，旋转的动画等。主要是为了使代码更简单写也更简单理解。

我的这个例子全在chrome上实现的，其他浏览器可能有问题。最好使用chrome来测试，并且有些特性的测试（阻塞）其他浏览器可能不支持。

首先准备一下基本的代码，一些与动画无关的功能先准备好如下。

<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>CSS 动画测试 -- jquery animate</title>
</style>
</head>
<body>
<div id="main">
</div>
<div id="btnCnt">
    <button id="clear">Clear</button>
    <button id="add10">加10</button>
    <button id="add100">加100</button>
    <button id="block">阻塞2秒</button>
    <h1 id="prompt">阻塞中......</h1>
</div>

<script src="js/jquery-1.11.0.js"></script>
<script>
$(document).ready(function() {
    addNDiv(1);
    $("#clear").click(function() {
        $("#main div").remove();
    });
    $("#add10").click(function() {
        addNDiv(10);
    });
    $("#add100").click(function() {
        addNDiv(100);
    });
    $("#block").click(function() {
        $("#prompt").show();
        setTimeout(function() {
            var begin = new Date().getTime();
            while (new Date().getTime() - begin <= 2000) {
            }
            $("#prompt").hide();
        }, 15);
    });
});

function addNDiv(n) {
    var $container = $("#main");
    var i = 0;
    while (i < n) {
        addOneDiv($container);
        i++;
    }
}

function addOneDiv($container) {
}

function animateDom() {
}
</script>
</body>
</html>

需要说明的是阻塞的代码。JS是单线程并且没有sleep，所以只能用一个长循环来模拟阻塞。
还要注意的是，因为JS的执行线程和UI渲染线程是相互阻塞的，可以理解成是一个线程。加这个测试按钮主要为了测试下面一种写法。
另外需要注意的是要确保$("#prompt").show();提示文字显示出来后再模拟阻塞，所以我们加了个setTimeout 。否则的话立即执行阻塞将UI渲染也阻塞住了，提示文字可能就看不到了。

$("#prompt").show();
setTimeout(function() {
    var begin = new Date().getTime();
    while (new Date().getTime() - begin <= 2000) {
    }
    $("#prompt").hide();
}, 15);


下面开始动画部分的介绍。

第一种用jquery的animate
CSS3之前做开始做动画只能是间隔时间修改元素值，jquery的动画就是这样做法。因为jquery包装好了animate方法，故此我们拿它来举例。

这里不打算介绍这个方法的细节。具体细节还是看官方文档来的好。我们将动画函数功能写成如下。
1.平移的举例和速度（间隔）都是通过随机传递过来的。让各个小方块的运动看起来杂乱无章。
2.jquery的animate方式支持链式调用让各个动画挨个顺序执行，也支持结束后的回调函数了。
    我们在最后一个动画结束后的回调函数中做处理，再重新给自身方块重头执行一次动画，我们靠这个回调来做到无限动画下去。

function animateDom($dom, x, y, speed) {
    $dom.
    animate(
        { "left": "+=" + x + "px", "top": "+=" + y + "px" }, speed, "swing"
    ).
    animate(
        { "left": "-=" + x + "px", "top": "-=" + y + "px" },
        speed,
        "swing",
        function() {
            animateDom($(this), x, y, speed);
        }
    );
}

最后补上增加一个小方块的函数，做的事情很简单。
1.为了一点点视觉效果我们让他出现的位置和背景色都随机。
2.根据它初始的生产位置计算一下它最好往那边平移，给他生成一个随机平移的目标位置，防止他平移出大方框。
3.最后调用动画函数让其动起来。

function addOneDiv($container) {
    var mainHeight = 500,
        mainWidth = 800,
        top = Math.floor(Math.random() * mainHeight + 1),
        left = Math.floor(Math.random() * mainWidth + 1);

    var $div = $("<div></div>").addClass("animate").css({
        "top": top + "px",
        "left": left + "px",
        "background-color": '#' + Math.floor(Math.random()*16777215).toString(16)
    }).appendTo($container);

    var backX = left > (mainWidth / 2) ? -1 : 1;
    var absX = backX < 0 ? (mainWidth - left) : left;
    var tx = Math.floor(Math.random() * absX + 1) * backX;

    var backY = top > (mainHeight / 2) ? -1 : 1;
    var absY = backY < 0 ? (mainHeight - top) : top;
    var ty = Math.floor(Math.random() * absY + 1) * backY;

    animateDom($div, tx, ty, Math.floor(Math.random() * 2000 + 500));
}

第一个方式运行起来可以看到效果。200以内的情况下看起来都蛮流畅的。加上300个小方块的时候就会出现明显的卡顿掉帧情况。如果点阻塞按钮会出现画面停止。
后面会提到性能方面的问题，也会就这个方式性能方面的讨论。

CSS3提供两种方式来制作动画，一个是过渡（transition），一个是动画（animation）。
transition很容易理解，就是状态A到状态B的转换，不是瞬间完成而是提供一个过渡效果。比如方块从top:100移动到top:200这个变化我们可以加上过渡的控制。
而animation就是真正意义上的动画，它和过渡是有区别的。可以理解成transition只是控制动画的一小段。而动画是完整控制整个动画过程。

先用简单点的transition开始。
与刚开始第一个例子比起来，过渡不需要我们做任何的JS代码来控制整个过渡/动画过程。
第一种做法animate()方法内部实现是用JS间隔时间来修改元素的属性值。但是transition是CSS属性，由浏览器内核实现中间值的变化。
transition的用法相对简单，它可以控制某个属性要不要过渡效果，过渡的时间间隔等，变化速率曲线等。具体细节可以看文档。
比如说transition: top 2s; 就是给元素top属性的变化加上了2秒的过渡效果。

我们第二个例子与第一个例子相似，只是用CSS 的 transition来控制小方块的top left属性的变化了。

div.animate {
    position: absolute;
    width: 10px;
    height: 10px;
    background-color: red;
    -webkit-transition: top 2s, left 2s;
    transition: top 2s, left 2s;
}

另外我们对animateDom做了点调整，因为过渡的动画时间由CSS控制，为求简化代码我们固定过渡时间为2秒，于是speed参数给删除了。
另外几个参数也给删除了。这种做法效果和第一个例子有点差别的。第一个例子小方块的运行轨迹是固定的，但此例子中却是随机的。
function animateDom() {
    $(this).css({
        "top": Math.floor(Math.random() * 500 + 1),
        "left": Math.floor(Math.random() * 800 + 1)
    });
}

还有就是过渡是属性变化结束后就会停止，跑完一个过渡期就结束了。
但它结束后有个transitionend回调函数。我们在这个回调中再重新设置一个新属性，这样就会无限动画起来了。

对addOneDiv的修改如下。
function addOneDiv($container) {
    var mainHeight = 500,
        mainWidth = 800,
        top = Math.floor(Math.random() * mainHeight + 1),
        left = Math.floor(Math.random() * mainWidth + 1);

    var $div = $("<div></div>").addClass("animate").css({
        "position": "absolute",
        "top": top + "px",
        "left": left + "px",
        "background-color": '#' + Math.floor(Math.random()*16777215).toString(16)
    }).appendTo($container);

    $div.get(0).addEventListener("transitionend", animateDom.bind($div), true);
    setTimeout(function() {
        animateDom.call($div);
    }, 500);
}
$div.get(0).addEventListener("transitionend", animateDom.bind($div), true);这句话便是注册回调，在它结束后重新调用动画函数。

运行起来看效果，放上几百个试试，你会发现第二个例子相对第一个例子性能会好一点。其他原因我们不多说，至少第二例JS主线程不需要做太多的运算了。


CSS3带来的不光是transition和animation。还有transform。注意这个transform和transition过渡是两码事，虽然单词看起来很相似。
transform是CSS提供的对元素的变换，例如平移变化，缩放，旋转等。
举例说平移变化，可以设置元素从原来位置往上下左右平移多少像素，这个和调整top,left是差不多的效果。之所以提到它是因为它和动画性能的关系很大。

我们第二种例子使用的是transition  + top/left 来做动画。下面我们第三个例子将使用transition + transform做动画。
具体transform详细用法还是建议参考文档，这里不赘述了。

第三个例子和第二个非常相似，只是我们控制需要过渡的属性从top/left变为transform。

相应的修改很少，一个是CSS transition过渡控制的属性。
-webkit-transition: -webkit-transform 2s;
transition: -webkit-transform 2s;

一个是animateDom修改top/left，变成修改transform。
function animateDom() {
    var tx = Math.floor(Math.random() * 800 + 1) + "px";
    var ty = Math.floor(Math.random() * 500 + 1) + "px";

    $(this).css({
        "transform": "translate(" + tx + ", " + ty + ")"
    });
}

另外为了代码简单，我们addOneDiv函数将小方块出现的位置固定在左上角了，而不是随机。相应的代码修改如下。
function addOneDiv($container) {

    var $div = $("<div></div>").addClass("animate").css({
        "position": "absolute",
        "top": 0 + "px",
        "left": 0 + "px",
        "background-color": '#' + Math.floor(Math.random()*16777215).toString(16)
    }).appendTo($container);

    $div.get(0).addEventListener("transitionend", animateDom.bind($div), true);
    setTimeout(function() {
        animateDom.call($div);
    }, 500);
}

运行起来看看效果。再多放几百个小方块试试。会发现性能好许多，至少比第一种要好许多。
另外这个例子还有很关键的一点。测试阻塞按钮，你会发现主线程阻塞的时候还是有一小方块在跑。（这个最好在chrome上试，FF貌似不行）。这个问题后面的性能讨论会提到。


transform支持不光支持2D变化，还支持3D变化。它提供类似的translate3d这样的API写法，可以做到X,Y,Z三维空间上做变换。
我们可以使用3D变换的API来完成2D的变换，只需要在 Z 轴上保持不变即可。虽然效果上一样，但是这个小小的改动理论上却可以大幅改进动画的性能。
这得益于如果使用了3D变换API，那么浏览器会主动性的选择使用GPU来做渲染。如果只是使用2D转换API，那么浏览器可能使用也可能不用GPU，这个取决于浏览器的实现。

我们继续完成第四个例子，和第三个例子的差别仅仅在于使用3D 转换的API。
function animateDom() {
    var tx = Math.floor(Math.random() * 800 + 1) + "px";
    var ty = Math.floor(Math.random() * 500 + 1) + "px";

    $(this).css({
        "transform": "translate3d(" + tx + ", " + ty + ", 0)"
    });
}
仅仅需要把translate（100， 100）这样的属性值，改写成translate3d（100，100， 0）这样的即可。

在浏览器上起来测试（我这边用的是chrome），和第三例的效果相同，性能也感觉差不多。


最后我们来看CSS真正的动画animation
animation和过渡（transition）相比，它的控制力度更加精准。可以吧动画(animation)看成是多个过渡（transition）的组合。

动画animation的API相对过渡（transition）要复杂一点。
第一要先定义一个动画的过程，同时也给这个动画过程一个名字。

@keyframes anim01 {
    0%   {left:0px; top:0px;}
    50%  {left:50px; top:50px;}
    100% {left:100px; top:100px;}
}

最后根据这个名字引用此动画即可，规定这个动画的时间长，速度曲线，是否循环等。
div {
    animation: anim01 2s;
}


我们最后来看第五个例子。使用animation + transform3d来完成。
从上面的整个transition的例子可以看出，要完成我们的测试例子，animation也可以组合left + top, transform2d, transform3d这三种搭配。
但是从写法来说它都没什么差别，从性能角度来说，transition相关的三个例子可以参考，没必要再继续细分了。


首先我们得先加入一个最基础的动画CSS，一个@keyframes
@-webkit-keyframes anim01 {
    0%   {-webkit-transform:translate(10px, 10px);}
    100% {-webkit-transform:translate(500px, 500px);}
}

然后让小方块的样式使用此动画。
div.animate {
    position: absolute;
    width: 10px;
    height: 10px;

    -webkit-animation-name: anim01;
    -webkit-animation-duration: 2s;
    -webkit-animation-iteration-count: infinite;
    -webkit-animation-timing-function: linear;
}

因为CSS动画(animation)安全由CSS控制，所以我们可以将代码中所有控制动画的代码都删除掉。由CSS的一个class控制即可。
animateDom函数可以删除掉，调用它的代码都可以删除掉。

这里有个稍微麻烦点的问题。我们想要的效果是各个小方块随机杂乱的走动，但我们此时只定义了固定的一个@keyframes，现在运行起来每个小方块都是固定的路径，并且点add10, add100的时候可以看到小方块完全重叠了。
我们需要动态改变@keyframes关键帧。好在用JS控制@keyframes也是可行的，否则我们这个例子就做不到随机轨迹了。

那么如何动态修改@keyframes呢，用到的是WebkitCSSKeyFramesRule这个对象，它有三个接口，deleteRule, insertRule, findRule举例如下。
WebkitCSSKeyFramesRule.deleteRule("50%");
WebkitCSSKeyFramesRule.findRule("50%");
WebkitCSSKeyFramesRule.insertRule("50%", "{left: 0px}");
keyframesRule.insertRule("50% {left: 0px;}");
像KEY/VALUE一样操作就可以了。具体可以去查阅详细文档。

代码中我们新建一个函数，用于修改我们唯一的@keyframes
function updateAnimateRule() {
    var beginX = Math.floor(Math.random() * 800 + 1) + "px";
    var endX = Math.floor(Math.random() * 800 + 1) + "px";
    var beginY = Math.floor(Math.random() * 500 + 1) + "px";
    var endY = Math.floor(Math.random() * 500 + 1) + "px";

    var beginStr = "0% {-webkit-transform:translate3d(" + beginX + ", " + beginY + ", 0px);}";
    var endStr = "100% {-webkit-transform:translate3d(" + endX + ", " + endY + ", 0px);}";

    var keyframesRule = document.styleSheets[0].cssRules[5];
    keyframesRule.deleteRule("0%");
    keyframesRule.deleteRule("100%");
    keyframesRule.insertRule(beginStr);
    keyframesRule.insertRule(endStr);
}
每次添加完一个小方块就更新一次@keyframes，更新函数中小方块的路径随机，这样我们的需求就差不多了OK了。
function addOneDiv($container) {

    var $div = $("<div></div>").addClass("animate").css({
        "position": "absolute",
        "top": 0 + "px",
        "left": 0 + "px",
        "background-color": '#' + Math.floor(Math.random()*16777215).toString(16)
    }).appendTo($container);

    updateAnimateRule();
}

如果你现在运行起来发现还是没有想象中的效果，小方块还是重叠的。
要完全结束还需要一个小技巧，我们每次添加小方块的时候设置一个时间间隔就可以了。
addNDiv函数改成如下，用一个setInterval来排队添加小方块，关键是保证每次修改后的@keyframes都能生效。

function addNDiv(n) {
    var timer = null;
    var $container = $("#main");
    var i = 0;
    var timer = setInterval(function() {
        addOneDiv($container);
        i++;
        if (i >= n) {
            if (timer) {
                clearInterval(timer);
            }
        }
    }, 20);
}

现在运行起来就OK了。并且本例子和第四例不同之处在于，你点阻塞按钮试试看。无论何时点击都不会阻塞动画的运行。
这是因为我们动画执行过程中完全没有用到JS控制，一旦小方块添加完成了JS的任务就完成了，所以点击阻塞就不会妨碍小方块的动画。
但是第四例的时候，我们需要用JS来控制小方块的循环运行，所以第四例中阻塞还是可能妨碍一部分小方块的运行的。


现在来探讨一下性能的问题。
其实制作上面例子的过程中关于性能的问题要点基本都有提到。这里再来统一总结一下。
首先一个很重要的观点是，我们想CSS+JS来控制动画的性能，实际上我们能做的很有限。因为这个性能很大程度上取决于浏览器的实现。
我们使用不同的API，不同的用法是会使性能有所不同，但能做毕竟很少。并且做种还是要取决于浏览器如何解释并实现你写的代码。

总结上面的例子，我们做动画可以使用不同的做法，JS, animation, transition。我们可以组合属性，transform, transform3d。
大多组合上面的例子都有实例验证，以上无论用什么组合来实现，关键的是一下几点。
（JS） VS （animation 或 transition）： JS占用UI主线程，而后者不用。
transform VS (属性)  : transform的渲染不在UI主线程中，也就是它不会受到JS代码的影响（这个是比较新的版本才有的，还有其他浏览器可能要不支持）。
另外一点，改变属性可能会更多的引起浏览器的重绘。例如使用transform的缩放scale(2)和改变属性(height, width)相比，后者会引起浏览器的重绘。
transform VS transform3d : 后者可以让浏览器主动性选择GPU来渲染，也即是硬件加速。提高动画渲染效率。

明白了以上的要点，那么我们制作过程中选用哪种方式就心里有数了。其实对于普通动画要求不高的浏览器页面使用哪种方式都是OK的。
因为无论何种方式在PC版的浏览器跑都不是问题，你的需求肯定不会像我们上面的例子N多小方块一起动哪种。
但是如果是在移动终端跑的话就要注意了。我们的第一原则是尽量减少浏览器重绘，重绘是个比较耗时步骤。第二如果能用到transform3d提高性能则更好。
至于用不用JS来主动控制（类似第一例），这个关系到不是特别重要，因为整个动画过程中JS占的比重是很少的，例如仅仅调用JS修改一个元素的left, top是很快就可以完成的。
最终的性能关键还在于浏览器的渲染过程。当然你能不用理论上性能是好一点。但是用JS做动画却可以很方便的控制。


