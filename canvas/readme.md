# canvas添加水印
### 创建一块画布
    var canvas = document.getElementById('canvas')
    var context = canvas.getContext('2d')
## drawImage
w3c地址：[drawImage](http://www.w3school.com.cn/html5/canvas_drawimage.asp)
## font
font属性可以设置字体大小，但是必须同时设置字体才能生效  
     `context.font = '40px Arial'`
## 跨域
- 可选择将资源保存到本地
- 添加属性  img.crossOrigin="anonymous" 但添加完还是有遇到跨域问题（未完全解决）
- 转base64
## canvas保存图片
生成的新图片实际上是一张画布，不能直接将图片保存，所以可以将生成的新图用img标签输出，将canvas样式属性设置为隐藏     
    `#canvas{
        width: 1px;
        height: 1px;
        opacity: 0;
      }`
## 代码示例
    imgTogether() {
      let self = this
      var img = new Image()
      img.src = '/newoffline/static/activity/activity-apple-agent/bg03.png'
      var img2 = new Image()
      img2.src = this.src
      img.onload = function() {
        // 绘制
        var canvas = document.getElementById('canvas')
        var context = canvas.getContext('2d')
        canvas.width = img.width
        canvas.height = img.height
        context.drawImage(img, 0, 0)
        context.drawImage(img2, 365, 1095, 400, 400)
        context.fillStyle = '#fff'
        context.font = '40px Arial'
        context.fillText(`活动时间：${self.activityStartDate}-${self.activityEndDate}`, 290, 423)
        context.stroke()
        self.imgSrc = canvas.toDataURL('image/png')
      }