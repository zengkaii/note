# 利用vw+rem实现移动web适配布局

## 基本概念

### 1、单位
Px(CSS pixels)

- 像素 (px) 是一种绝对单位（absolute units）， 因为无论其他相关的设置怎么变化，像素指定的值是不会变化的 
- 其实是相对于某个设备而言的，不同设备指定的值大小还是有区别的

em

- 1em与当前元素的字体大小相同（更具体地说，一个大写字母M的宽度）。CSS样式被应用之前，浏览器给网页设置的默认基础字体大小是16像素，这意味着对一个元素来说1em的计算值默认为16像素。但是要小心—em单位是会继承父元素的字体大小，所以如果在父元素上设置了不同的字体大小，em的像素值就会变得复杂。现在不要过于担心这个问题，我们将在后面的文章和模块中更详细地介绍继承和字体大小设置。em是Web开发中最常用的相对单位。

ex, ch
- 分别是小写x的高度和数字0的宽度。这些并不像em那样被普遍使用或很好地被支持。

rem
- REM（root em）和em以同样的方式工作，但它总是等于默认基础字体大小的尺寸；继承的字体大小将不起作用，所以这听起来像一个比em更好的选择，虽然在旧版本的IE上不被支持 查看关于跨浏览器支持 [Debugging CSS](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Introduction_to_CSS/Debugging_CSS).)

vw,vh
- 分别是视口宽度的1/100和视口高度的1/100，其次，它不像rem那样被广泛支持。 还有vmin,vmax...

百分比


rem 和 vw 兼容对比

rem
![rem](https://pic2.zhimg.com/80/b4f035b1007996aba311cab004425179_hd.png)

vw
![vm](https://pic4.zhimg.com/80/a6df85f2f4c38db8ff30da6f5276362f_hd.png)

### 2、关于window.devicePixelRatio

[mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio)
- 此属性返回当前显示设备的物理像素分辨率与CSS像素分辨率的比值。该值也可以被解释为像素大小的比例：即一个CSS像素的大小相对于一个物理像素的大小的比值。

那么什么是设备的物理像素？
- 物理像素（physical pixel）：设备能控制显示的最小单位。设备独立像素（DIP，device-independent pixel，density-independent pixel）：独立于设备的用于逻辑上衡量像素的单位


- 对于前端来说，可以理解成在设备上能设置的最小值。如在dpr = 1 最小值1px; dpr = 2 最小值为0.5px  

PPI（pixel per inch）
- 每英寸有多少个像素点（设备像素点），PPI越高，画质越好。

公式 
```
// w: 横向pixel
// h: 纵向pixel
// inch: 屏幕尺寸
PPI = Math.sqrt(w * w + h * h) / inch

console.log(Math.ceil(Math.sqrt(1920 * 1920 + 1080 * 1080) / 5.5))
// 然而我并不明白怎么要这样算 不是应该是 w * h / inch ？？？？？
```

[retina 显示屏](https://zh.wikipedia.org/wiki/Retina%E6%98%BE%E7%A4%BA%E5%B1%8F)
> 具备足够高像素密度而使得人体肉眼无法分辨其中单独像素点的液晶屏

### 3、移动适配注意的点

1. 屏幕尺寸不一样的适配问题
    - 这个可以借助vw完美解决
2. 图片模糊问题
    - 图片用三倍图 可以保证清晰，推荐用两倍图+webp格式 开发和显示效果折中的结果

3. 1px 显示问题
    - 方法很多
4. api不支持的兼容问题
    - vw不支持的兼容 
5. 高清显示
    - 是否影响渲染性能？？待考证
    - 缩放是否可以是网页更高清？

![](https://user-gold-cdn.xitu.io/2018/5/3/16323f26ebbd4463?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

6. 第三方组件库兼容问题
7. 富文本显示问题
8. 前端代码迁移单位不一致问题

## 适配方案

### [1、经典的flexble.js方案](https://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html)
1、iframe 问题
2、富文本问题
```
// vue指令
// 1. 先获取dpr 2获取每个文档对象的字体大小 乘以dpr
Vue.directive('richtext', (el, binding) => {
    Vue.nextTick(() => {
        let dpr = document.querySelector('html').getAttribute('data-dpr')
        let f = (dom) => {
            let fontPx = dom.style && dom.style.fontSize
            if (fontPx) {
                fontPx = fontPx.substr(0, fontPx.length - 2) * parseInt(dpr)
                dom.style.fontSize = fontPx + 'px'
            }
            if (dom.childNodes.length > 0) {
                for (let i = 0; i < dom.childNodes.length; i++) {
                    f(dom.childNodes[i])
                }
            }
        }
        f(el)
    })
})
// 使用方法 
.richtext(v-html="content" v-richtext='')
```
3、高清方案存在的误区
dpr越高越清晰 是源自设备本身和缩放关系不大或者说当dpr为1时清晰度已经足够了，不需要再通过缩放来是网页显示更清晰（实践总结，未找到资料支持）
4、兼容问题
https://juejin.im/post/5b9cb9d26fb9a05d1b2e44a3

### 2、vw方案
[参考](https://www.w3cplus.com/css/vw-for-layout.html)

### 3、rem + vw （推荐方案）
设置html根font-size的值
// 设计稿 750 * 1334
```
html {
  height: 100%;
  font-size: 50px; // 向下兼容 不止vw时候 写死font-size
  font-size: 13.33333333vw; // 7.5rem === 100vw
  margin: 0 auto;
}


@media (min-width: 560px) { // pc兼容
  html,body {
    font-size: 54px;
    max-width: 540px;
  }
}

@media (max-width: 1024px) { // ipad兼容：ipad最大1024px
  html,body {
    max-width: 1024px;
  }
}
```

1px显示问题 物理像素 dpr>=2 取2倍
```
.border-bottom {
  position: relative;
  &::after {
    content: '';
    position: absolute;
    z-index: 1;
    pointer-events: none;
    background-color: red;
    height: 1px;
    left: 0;
    right: 0;
    bottom: 0;
    @media only screen and (min-resolution: 2dppx) { // 非标准的
      -webkit-transform: scaleY(0.5);
      -webkit-transform-origin: 50% 0%;
    }
  }
}
// only操作符仅在媒体查询匹配成功的情况下被用于应用一个样式，这对于防止让选中的样式在老式浏览器中被应用到
// -webkit-device-pixel-ratio 是一个非标准的布尔类型CSS媒体类型，是标准 
// resolution 媒体类型的一个替代方案.
```
单位要怎么写

方法一：postcss-px2rem(vue-cli3) 
> 这里不推荐 因为px2rem: 写px时需要加上 /\*no\*/ 感觉比较麻烦

```
const px2rem = require('postcss-px2rem')
const postcssPx2rem = new px2rem({
  remUnit: 50 // 基准大小
})
css: {
    loaderOptions: {
      postcss: {
        plugins: [postcssPx2rem]
      },
    }
  },
```
方法二：目前方案是直接写 7.5rem=100vw 设计稿除以100即可

方法三：px2rem的编辑器插件如vscode的cssrem


> 除不尽的问题 （calc）
> 涉及到兼容问题
## 总结 vw + rem的好处
- 实现简单
  - 不依赖插件及第三方库
  - 几行css代码 就可以实现
- 开发方便
  - 100vw === 设计稿 / 100 rem 方便换算
  - 不影响px使用，完美兼容第三方组件库
  - 不存在富文本和iframe等兼容问题
