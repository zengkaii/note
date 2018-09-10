# Sticky footers
链接地址：[css秘密花园](https://www.w3cplus.com/css3/css-secrets/sticky-footers.html)
## 解决的问题
    当图片与底部文字（一个模块）不足够填充满整个设备时，我们可以取图片底部的颜色作为背景色填充，
    当然要填充在底部文字（一个模块）与上层图片中间
    那么怎么计算这个高度呢？
    一般使用css能解决的就尽量不使用js
    所以这里就用到sticky footers
## 固定高度的解决方案
    <header> 
        <h1>Site name</h1> 
    </header>
    <main>
        <p> Bacon Ipsum dolor sit amet... <!-- Filler text from baconipsum.com -->
         </p> 
    </main> 
    <footer>
        <p>© 2015 No rights reserved.</p> <p>Made with ♥ by an anonymous pastafarian.
        </p> 
     </footer>
     css 
     main {
         min-height:calc(100vh - 2.5em -7em);
         box-sizing:border-box;
     }
## Flexbox解决方案
解决这类问题，Flexbox是最完美的方案。我们只需要几行CSS代码就可以完美的实现，而且不需要一些奇怪的计算或添加额外的HTML元素。首先，我们需要在<body>元素上设置display:flex。如果父元素(<body>)的三个块元素，使用Flexbox切换布局，还需要设置flex-flow:column，否则这三个块会排成一行

    body{
        display:flex;
        flex-flow:column;
        min-height:100vh;
    }
    main{
        flex:1;
    }