cssAnimation
============

һ������CSS��ͬ��ʽ������������������ʾ�����򡣹ۿ�Ч������ http://1.cssanimation.sinaapp.com/ 


ĿǰWEBҳ���������ķ�ʽ��ķ�����
1.JS���ʱ�䲻���޸�Ԫ������ֵ����Ҳ��CSS3����ǰ���õ�������ò��Ҳ��Ψһ��������
2.CSS3����֮��֧�ֶ����ˡ�

�����˼·�������֡�Ҫ�Ǹ����������ϸ�־Ϳ��Էֳ����������������Ҫ����WEBҳ����������һЩ��ʽ�Լ����ܷ�����

���ǵ����ӽ������¡�
1.�úö��С������ƶ������Բ�ͬ��ʽ���������ܡ�
2.�ұ߼������ܰ�ť�������и�������ť������һ����ѭ���������߳������Բ�ͬЧ����
3.������Ҫ������С�����޹����ƽ�ƣ�û��ʹ�������������ţ���ת�Ķ����ȡ���Ҫ��Ϊ��ʹ�������дҲ������⡣

�ҵ��������ȫ��chrome��ʵ�ֵģ�������������������⡣���ʹ��chrome�����ԣ�������Щ���ԵĲ��ԣ�������������������ܲ�֧�֡�

����׼��һ�»����Ĵ��룬һЩ�붯���޹صĹ�����׼�������¡�

<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>CSS �������� -- jquery animate</title>
</style>
</head>
<body>
<div id="main">
</div>
<div id="btnCnt">
    <button id="clear">Clear</button>
    <button id="add10">��10</button>
    <button id="add100">��100</button>
    <button id="block">����2��</button>
    <h1 id="prompt">������......</h1>
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

��Ҫ˵�����������Ĵ��롣JS�ǵ��̲߳���û��sleep������ֻ����һ����ѭ����ģ��������
��Ҫע����ǣ���ΪJS��ִ���̺߳�UI��Ⱦ�߳����໥�����ģ�����������һ���̡߳���������԰�ť��ҪΪ�˲�������һ��д����
������Ҫע�����Ҫȷ��$("#prompt").show();��ʾ������ʾ��������ģ���������������Ǽ��˸�setTimeout ������Ļ�����ִ��������UI��ȾҲ����ס�ˣ���ʾ���ֿ��ܾͿ������ˡ�

$("#prompt").show();
setTimeout(function() {
    var begin = new Date().getTime();
    while (new Date().getTime() - begin <= 2000) {
    }
    $("#prompt").hide();
}, 15);


���濪ʼ�������ֵĽ��ܡ�

��һ����jquery��animate
CSS3֮ǰ����ʼ������ֻ���Ǽ��ʱ���޸�Ԫ��ֵ��jquery�Ķ�������������������Ϊjquery��װ����animate�������ʴ�����������������

���ﲻ����������������ϸ�ڡ�����ϸ�ڻ��ǿ��ٷ��ĵ����ĺá����ǽ�������������д�����¡�
1.ƽ�Ƶľ������ٶȣ����������ͨ��������ݹ����ġ��ø���С������˶��������������¡�
2.jquery��animate��ʽ֧����ʽ�����ø�����������˳��ִ�У�Ҳ֧�ֽ�����Ļص������ˡ�
    ���������һ������������Ļص������������������¸���������ͷִ��һ�ζ��������ǿ�����ص����������޶�����ȥ��

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

���������һ��С����ĺ�������������ܼ򵥡�
1.Ϊ��һ����Ӿ�Ч�������������ֵ�λ�úͱ���ɫ�������
2.��������ʼ������λ�ü���һ����������Ǳ�ƽ�ƣ���������һ�����ƽ�Ƶ�Ŀ��λ�ã���ֹ��ƽ�Ƴ��󷽿�
3.�����ö����������䶯������

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

��һ����ʽ�����������Կ���Ч����200���ڵ�����¿��������������ġ�����300��С�����ʱ��ͻ�������ԵĿ��ٵ�֡����������������ť����ֻ���ֹͣ��
������ᵽ���ܷ�������⣬Ҳ��������ʽ���ܷ�������ۡ�

CSS3�ṩ���ַ�ʽ������������һ���ǹ��ɣ�transition����һ���Ƕ�����animation����
transition��������⣬����״̬A��״̬B��ת��������˲����ɶ����ṩһ������Ч�������緽���top:100�ƶ���top:200����仯���ǿ��Լ��Ϲ��ɵĿ��ơ�
��animation�������������ϵĶ��������͹�����������ġ���������transitionֻ�ǿ��ƶ�����һС�Ρ����������������������������̡�

���ü򵥵��transition��ʼ��
��տ�ʼ��һ�����ӱ����������ɲ���Ҫ�������κε�JS������������������/�������̡�
��һ������animate()�����ڲ�ʵ������JS���ʱ�����޸�Ԫ�ص�����ֵ������transition��CSS���ԣ���������ں�ʵ���м�ֵ�ı仯��
transition���÷���Լ򵥣������Կ���ĳ������Ҫ��Ҫ����Ч�������ɵ�ʱ�����ȣ��仯�������ߵȡ�����ϸ�ڿ��Կ��ĵ���
����˵transition: top 2s; ���Ǹ�Ԫ��top���Եı仯������2��Ĺ���Ч����

���ǵڶ����������һ���������ƣ�ֻ����CSS �� transition������С�����top left���Եı仯�ˡ�

div.animate {
    position: absolute;
    width: 10px;
    height: 10px;
    background-color: red;
    -webkit-transition: top 2s, left 2s;
    transition: top 2s, left 2s;
}

�������Ƕ�animateDom���˵��������Ϊ���ɵĶ���ʱ����CSS���ƣ�Ϊ��򻯴������ǹ̶�����ʱ��Ϊ2�룬����speed������ɾ���ˡ�
���⼸������Ҳ��ɾ���ˡ���������Ч���͵�һ�������е���ġ���һ������С��������й켣�ǹ̶��ģ�����������ȴ������ġ�
function animateDom() {
    $(this).css({
        "top": Math.floor(Math.random() * 500 + 1),
        "left": Math.floor(Math.random() * 800 + 1)
    });
}

���о��ǹ��������Ա仯������ͻ�ֹͣ������һ�������ھͽ����ˡ�
�����������и�transitionend�ص�����������������ص�������������һ�������ԣ������ͻ����޶��������ˡ�

��addOneDiv���޸����¡�
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
$div.get(0).addEventListener("transitionend", animateDom.bind($div), true);��仰����ע��ص����������������µ��ö���������

����������Ч�������ϼ��ٸ����ԣ���ᷢ�ֵڶ���������Ե�һ���������ܻ��һ�㡣����ԭ�����ǲ���˵�����ٵڶ���JS���̲߳���Ҫ��̫��������ˡ�


CSS3�����Ĳ�����transition��animation������transform��ע�����transform��transition�����������£���Ȼ���ʿ����������ơ�
transform��CSS�ṩ�Ķ�Ԫ�صı任������ƽ�Ʊ仯�����ţ���ת�ȡ�
����˵ƽ�Ʊ仯����������Ԫ�ش�ԭ��λ������������ƽ�ƶ������أ�����͵���top,left�ǲ���Ч����֮�����ᵽ������Ϊ���Ͷ������ܵĹ�ϵ�ܴ�

���ǵڶ�������ʹ�õ���transition  + top/left �����������������ǵ��������ӽ�ʹ��transition + transform��������
����transform��ϸ�÷����ǽ���ο��ĵ������ﲻ׸���ˡ�

���������Ӻ͵ڶ����ǳ����ƣ�ֻ�����ǿ�����Ҫ���ɵ����Դ�top/left��Ϊtransform��

��Ӧ���޸ĺ��٣�һ����CSS transition���ɿ��Ƶ����ԡ�
-webkit-transition: -webkit-transform 2s;
transition: -webkit-transform 2s;

һ����animateDom�޸�top/left������޸�transform��
function animateDom() {
    var tx = Math.floor(Math.random() * 800 + 1) + "px";
    var ty = Math.floor(Math.random() * 500 + 1) + "px";

    $(this).css({
        "transform": "translate(" + tx + ", " + ty + ")"
    });
}

����Ϊ�˴���򵥣�����addOneDiv������С������ֵ�λ�ù̶������Ͻ��ˣ��������������Ӧ�Ĵ����޸����¡�
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

������������Ч�����ٶ�ż��ٸ�С�������ԡ��ᷢ�����ܺ���࣬���ٱȵ�һ��Ҫ����ࡣ
����������ӻ��кܹؼ���һ�㡣����������ť����ᷢ�����߳�������ʱ������һС�������ܡ�����������chrome���ԣ�FFò�Ʋ��У���������������������ۻ��ᵽ��


transform֧�ֲ���֧��2D�仯����֧��3D�仯�����ṩ���Ƶ�translate3d������APIд������������X,Y,Z��ά�ռ������任��
���ǿ���ʹ��3D�任��API�����2D�ı任��ֻ��Ҫ�� Z ���ϱ��ֲ��伴�ɡ���ȻЧ����һ�����������СС�ĸĶ�������ȴ���Դ���Ľ����������ܡ�
����������ʹ����3D�任API����ô������������Ե�ѡ��ʹ��GPU������Ⱦ�����ֻ��ʹ��2Dת��API����ô���������ʹ��Ҳ���ܲ���GPU�����ȡ�����������ʵ�֡�

���Ǽ�����ɵ��ĸ����ӣ��͵��������ӵĲ���������ʹ��3D ת����API��
function animateDom() {
    var tx = Math.floor(Math.random() * 800 + 1) + "px";
    var ty = Math.floor(Math.random() * 500 + 1) + "px";

    $(this).css({
        "transform": "translate3d(" + tx + ", " + ty + ", 0)"
    });
}
������Ҫ��translate��100�� 100������������ֵ����д��translate3d��100��100�� 0�������ļ��ɡ�

����������������ԣ�������õ���chrome�����͵�������Ч����ͬ������Ҳ�о���ࡣ


�����������CSS�����Ķ���animation
animation�͹��ɣ�transition����ȣ����Ŀ������ȸ��Ӿ�׼�����԰ɶ���(animation)�����Ƕ�����ɣ�transition������ϡ�

����animation��API��Թ��ɣ�transition��Ҫ����һ�㡣
��һҪ�ȶ���һ�������Ĺ��̣�ͬʱҲ�������������һ�����֡�

@keyframes anim01 {
    0%   {left:0px; top:0px;}
    50%  {left:50px; top:50px;}
    100% {left:100px; top:100px;}
}

����������������ô˶������ɣ��涨���������ʱ�䳤���ٶ����ߣ��Ƿ�ѭ���ȡ�
div {
    animation: anim01 2s;
}


�������������������ӡ�ʹ��animation + transform3d����ɡ�
�����������transition�����ӿ��Կ�����Ҫ������ǵĲ������ӣ�animationҲ�������left + top, transform2d, transform3d�����ִ��䡣
���Ǵ�д����˵����ûʲô��𣬴����ܽǶ���˵��transition��ص��������ӿ��Բο���û��Ҫ�ټ���ϸ���ˡ�


�������ǵ��ȼ���һ��������Ķ���CSS��һ��@keyframes
@-webkit-keyframes anim01 {
    0%   {-webkit-transform:translate(10px, 10px);}
    100% {-webkit-transform:translate(500px, 500px);}
}

Ȼ����С�������ʽʹ�ô˶�����
div.animate {
    position: absolute;
    width: 10px;
    height: 10px;

    -webkit-animation-name: anim01;
    -webkit-animation-duration: 2s;
    -webkit-animation-iteration-count: infinite;
    -webkit-animation-timing-function: linear;
}

��ΪCSS����(animation)��ȫ��CSS���ƣ��������ǿ��Խ����������п��ƶ����Ĵ��붼ɾ��������CSS��һ��class���Ƽ��ɡ�
animateDom��������ɾ�������������Ĵ��붼����ɾ������

�����и���΢�鷳������⡣������Ҫ��Ч���Ǹ���С����������ҵ��߶��������Ǵ�ʱֻ�����˹̶���һ��@keyframes��������������ÿ��С���鶼�ǹ̶���·�������ҵ�add10, add100��ʱ����Կ���С������ȫ�ص��ˡ�
������Ҫ��̬�ı�@keyframes�ؼ�֡��������JS����@keyframesҲ�ǿ��еģ���������������Ӿ�����������켣�ˡ�

��ô��ζ�̬�޸�@keyframes�أ��õ�����WebkitCSSKeyFramesRule����������������ӿڣ�deleteRule, insertRule, findRule�������¡�
WebkitCSSKeyFramesRule.deleteRule("50%");
WebkitCSSKeyFramesRule.findRule("50%");
WebkitCSSKeyFramesRule.insertRule("50%", "{left: 0px}");
keyframesRule.insertRule("50% {left: 0px;}");
��KEY/VALUEһ�������Ϳ����ˡ��������ȥ������ϸ�ĵ���

�����������½�һ�������������޸�����Ψһ��@keyframes
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
ÿ�������һ��С����͸���һ��@keyframes�����º�����С�����·��������������ǵ�����Ͳ����OK�ˡ�
function addOneDiv($container) {

    var $div = $("<div></div>").addClass("animate").css({
        "position": "absolute",
        "top": 0 + "px",
        "left": 0 + "px",
        "background-color": '#' + Math.floor(Math.random()*16777215).toString(16)
    }).appendTo($container);

    updateAnimateRule();
}

��������������������ֻ���û�������е�Ч����С���黹���ص��ġ�
Ҫ��ȫ��������Ҫһ��С���ɣ�����ÿ�����С�����ʱ������һ��ʱ�����Ϳ����ˡ�
addNDiv�����ĳ����£���һ��setInterval���Ŷ����С���飬�ؼ��Ǳ�֤ÿ���޸ĺ��@keyframes������Ч��

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

��������������OK�ˡ����ұ����Ӻ͵�������֮ͬ�����ڣ����������ť���Կ������ۺ�ʱ����������������������С�
������Ϊ���Ƕ���ִ�й�������ȫû���õ�JS���ƣ�һ��С������������JS�����������ˣ����Ե�������Ͳ������С����Ķ�����
���ǵ�������ʱ��������Ҫ��JS������С�����ѭ�����У����Ե��������������ǿ��ܷ���һ����С��������еġ�


������̽��һ�����ܵ����⡣
��ʵ�����������ӵĹ����й������ܵ�����Ҫ����������ᵽ����������ͳһ�ܽ�һ�¡�
����һ������Ҫ�Ĺ۵��ǣ�������CSS+JS�����ƶ��������ܣ�ʵ�������������ĺ����ޡ���Ϊ������ܴܺ�̶���ȡ�����������ʵ�֡�
����ʹ�ò�ͬ��API����ͬ���÷��ǻ�ʹ����������ͬ���������Ͼ����١��������ֻ���Ҫȡ�����������ν��Ͳ�ʵ����д�Ĵ��롣

�ܽ���������ӣ���������������ʹ�ò�ͬ��������JS, animation, transition�����ǿ���������ԣ�transform, transform3d��
��������������Ӷ���ʵ����֤������������ʲô�����ʵ�֣��ؼ�����һ�¼��㡣
��JS�� VS ��animation �� transition���� JSռ��UI���̣߳������߲��á�
transform VS (����)  : transform����Ⱦ����UI���߳��У�Ҳ�����������ܵ�JS�����Ӱ�죨����ǱȽ��µİ汾���еģ������������������Ҫ��֧�֣���
����һ�㣬�ı����Կ��ܻ�����������������ػ档����ʹ��transform������scale(2)�͸ı�����(height, width)��ȣ����߻�������������ػ档
transform VS transform3d : ���߿����������������ѡ��GPU����Ⱦ��Ҳ����Ӳ�����١���߶�����ȾЧ�ʡ�

���������ϵ�Ҫ�㣬��ô��������������ѡ�����ַ�ʽ�����������ˡ���ʵ������ͨ����Ҫ�󲻸ߵ������ҳ��ʹ�����ַ�ʽ����OK�ġ�
��Ϊ���ۺ��ַ�ʽ��PC���������ܶ��������⣬�������϶��������������������N��С����һ�����֡�
������������ƶ��ն��ܵĻ���Ҫע���ˡ����ǵĵ�һԭ���Ǿ�������������ػ棬�ػ��Ǹ��ȽϺ�ʱ���衣�ڶ�������õ�transform3d�����������á�
�����ò���JS���������ƣ����Ƶ�һ�����������ϵ�������ر���Ҫ����Ϊ��������������JSռ�ı����Ǻ��ٵģ������������JS�޸�һ��Ԫ�ص�left, top�Ǻܿ�Ϳ�����ɵġ�
���յ����ܹؼ����������������Ⱦ���̡���Ȼ���ܲ��������������Ǻ�һ�㡣������JS������ȴ���Ժܷ���Ŀ��ơ�


