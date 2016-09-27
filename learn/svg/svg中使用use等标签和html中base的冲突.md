## SVG中使用use等标签和html中base的冲突问题



> + svg attributes: `` ['clip-path', 'color-profile', 'src', 'cursor', 'fill', 'filter', 'marker', 'marker-start', 'marker-mid', 'marker-end', 'mask', 'stroke’] `` 

> + check attribute value for url(...) using regex



在SVG中使用上面的属性的时候，要检查是否使用的 `` url  `` 来引入的，如果使用了`` url  `` 来引入的话，就需要注意html的head中是否使用了base标签

如果使用了base标签的话，url的引入机制就会发生变化，导致引入不进来。请看下面的例子：



``

<svg style="width: 100%;height:100%;" class="svgGraph" xmlns="http://www.w3.org/2000/svg" "> 

    <g id="rootGroup" transform="translate(175, 175)">

        <path class="link" fill="#eb4f38" d="M0,124C654,124 654,124 1308,124"></path>

        <g class="node" id="e5b306f27b614ee58e5df8282091a52c" transform="translate(0,124)">

            <circle class="node-dot" r="35" stroke="#FB5F6D" stroke-width="4"></circle>

            <text x="20" dy=".35em" text-anchor="end">FLUME</text>

        </g>

        <g class="node" id="d22c856d5efe40d2b4ddedfdd72b1894" transform="translate(1308,124)">

            <circle class="node-dot" r="35" stroke="#46cdc7" stroke-width="4"></circle>

            <text x="20" dy=".35em" text-anchor="end">KAFKA</text>

        </g>

    </g>

    <defs>

        <filter id="f1" filterUnits="userSpaceOnUse">

            <feColorMatrix result="matrixOut" in="SourceGraphic" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.5 0"></feColorMatrix>

            <feGaussianBlur result="blurOut" in="matrixOut" stdDeviation="10"></feGaussianBlur>

            <feBlend in="SourceGraphic" in2="blurOut" mode="normal"></feBlend>

        </filter>

    </defs>

    <g class="menu">

        <circle id="out-circle" class="ring" cx="175" cy="299" r="115" fill="rgba(0, 0, 0, .3)"></circle>

        <circle id="in-circle" class="ring" cx="175" cy="299" r="90" fill="white"></circle>

        <circle class="ring-button" r="35" cx="175" cy="299" stroke="white" stroke-width="2" filter="url(#f1)" fill="white" transform="translate(0,99)"></circle>

        ...

    </g>

    <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#e5b306f27b614ee58e5df8282091a52c" x="175" y="175"></use>

</svg>

``

上面是没有使用base标签的时候，`` filter: url(#f1) `` 是可以显示出来的，当使用了base之后：



``

<html>

<head>

<base href="/">

</head>

<body>

<svg style="width: 100%;height:100%;" class="svgGraph" xmlns="http://www.w3.org/2000/svg" "> 

    <g id="rootGroup" transform="translate(175, 175)">

        <path class="link" fill="#eb4f38" d="M0,124C654,124 654,124 1308,124"></path>

        <g class="node" id="e5b306f27b614ee58e5df8282091a52c" transform="translate(0,124)">

            <circle class="node-dot" r="35" stroke="#FB5F6D" stroke-width="4"></circle>

            <text x="20" dy=".35em" text-anchor="end">FLUME</text>

        </g>

        <g class="node" id="d22c856d5efe40d2b4ddedfdd72b1894" transform="translate(1308,124)">

            <circle class="node-dot" r="35" stroke="#46cdc7" stroke-width="4"></circle>

            <text x="20" dy=".35em" text-anchor="end">KAFKA</text>

        </g>

    </g>

    <defs>

        <filter id="f1" filterUnits="userSpaceOnUse">

            <feColorMatrix result="matrixOut" in="SourceGraphic" type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.5 0"></feColorMatrix>

            <feGaussianBlur result="blurOut" in="matrixOut" stdDeviation="10"></feGaussianBlur>

            <feBlend in="SourceGraphic" in2="blurOut" mode="normal"></feBlend>

        </filter>

    </defs>

    <g class="menu">

        <circle id="out-circle" class="ring" cx="175" cy="299" r="115" fill="rgba(0, 0, 0, .3)"></circle>

        <circle id="in-circle" class="ring" cx="175" cy="299" r="90" fill="white"></circle>

        <circle class="ring-button" r="35" cx="175" cy="299" stroke="white" stroke-width="2" filter="url(#f1)" fill="white" transform="translate(0,99)"></circle>

        ...

    </g>

    <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#e5b306f27b614ee58e5df8282091a52c" x="175" y="175"></use>

</svg>

...

``

这是如果 `` filter: url(#f1) `` 就显示不出来了。



### 解决方案



如果前端主流框架Angular 1.3之后的版本都支持html5Mode模式，这时候就需要设置base属性。包括如果使用nginx反向代理之后也需要设置base属性

只需要将location.href添加到url中即可

``

<base href="http://someone.com">

<use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="http://someone.com#e5b306f27b614ee58e5df8282091a52c" x="175" y="175"></use>

``



如果使用框架，并且use等标签是js动态生成的，那么：

``

var useEle = document.getElementById('use');

// 或者使用jq, 这是使用jq

var useEle = $('#use');

useEle.attr('xlink:href', `${window.location.href}#${d.id}`)

        // .attr('fill-opacity', 0)

        .attr('x', 175)

        .attr('y', 175);

``






