##   导出PDF

### 需要用到的插件
#### 1、html2canvas（版本1.0.0-rc.1）
`npm install html2canvas@1.0.0-rc.1`
    
#### 2、jspdf（版本1.4.1）
`npm install jspdf@1.4.1`

* 目前这两个插件的版本是没有问题的，最新的插件会出现白屏问题

### 代码
``` JavaScript
var title = '打印票'
// 解决PDF打印模糊的问题
// 将canvas的属性width和height属性放大2倍
var c = document.createElement("canvas")
let pdfDoms = document.querySelectorAll('#pdfDom');
for( let i = 0 ; i < pdfDoms.length ;i++ ){
    var opts = {
    allowTaint:true,//允许加载跨域的图片
    tainttest:true, //检测每张图片都已经加载完成
    background: '#FFFFFF', // 默认为黑色，将 background 为其他颜色
    useCORS: true,
    scale: 2,
    canvas: c,
    logging: false,//日志
    width: pdfDoms[i].width,
    height: pdfDoms[i].height,
};
c.width = pdfDoms[i].width * 2
c.height = pdfDoms[i].height * 2
c.getContext("2d").scale(2, 2);
html2Canvas(pdfDoms[i],opts).then(
    function (canvas) {
        var pdf = new JsPDF('p', 'mm', 'a4');    //A4纸，纵向
        var ctx = canvas.getContext('2d'),
            a4w = 190, a4h = 277,    //A4大小，210mm x 297mm，四边各保留10mm的边距，显示区域190x277
        // imgHeight = Math.floor(a4h * canvas.width / a4w),    //按A4显示比例换算一页图像的像素高度
        imgHeight = 260*6*2,
        renderedHeight = 0;
        while(renderedHeight < canvas.height) {
            var page = document.createElement("canvas");
            page.width = canvas.width ;
            page.height = Math.min(imgHeight, canvas.height - renderedHeight);//可能内容不足一页
            //用getImageData剪裁指定区域，
            var imageData = ctx.getImageData(0, renderedHeight, canvas.width, Math.min(imgHeight,canvas.height - renderedHeight))
            // 并画到前面创建的canvas对象中
            page.getContext('2d').putImageData(imageData, 0, 0);
            // 返回图片dataURL
            var pageData = page.toDataURL('image/jpeg', 1.0)
            //添加图像到页面，保留10mm边距
            pdf.addImage(pageData, 'JPEG', 10, 10, a4w, Math.min(a4h, a4w * page.height / page.width));
            renderedHeight += imgHeight;
            if(renderedHeight < canvas.height)
                pdf.addPage();//如果后面还有内容，添加一个空页
                // delete page;
            }
            pdf.save(title +'-'+ (i+1) + '.pdf')
            // setTimeout(() => {
            // window.close()
            // }, 1000)
        }
    )
}

```

### 遇见的问题
#### 1、PDF分页时将内容一同分开
##### 解决方法：计算每一页适合展示的内容高度，赋值给imgHeight

#### 2、PDF内容模糊
##### 解决方法：将canvas的宽高放大两倍
```
c.width = pdfDoms[i].width * 2
c.height = pdfDoms[i].height * 2
c.getContext("2d").scale(2, 2);
```

#### 3、导出PDF为黑屏
##### 原因：数据量加载过大，canvas无法绘制；
##### 解决方法：分批次导出PDF