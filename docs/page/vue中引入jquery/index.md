## vue中引入jquery

#### 1. 安装jquery

`npm install jquery `

#### 2. 在`webpack.base.conf.js`中配置jquery
##### 引入`webpack`

`var webpack = require("webpack")`

##### 在`module.exports`的最后添加jquery插件
``` js
pliguns:[
    new webpack.ProvidePlugin({
        jQuery : "jquery",
        $ : "jquery"
    })
]
```
![](image/Image.png)

#### 3. 在`main.js`中引入

`import $ from "jquery"`

