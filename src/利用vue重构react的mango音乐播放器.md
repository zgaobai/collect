# 利用vue重构react的mango音乐播放器
> 不知道做不做的到，先xjb记录一下过程，后期再整理

## 项目准备
安装node.exe在目录E:\software\nodejs\

node -v  
npm -v  

npm config set prefix "E:\software\nodejs\node_global"

npm config set cache "E:\software\nodejs\node_cache"

环境变量

Path：
// 后期全局安装的各种包访问
E:\software\nodejs\node_global

// 访问node
E:\software\nodejs

安装cnpm  
npm install -g cnpm -registry=https://registry.npm.taobao.org

cnpm -v

安装vue-cli
cnpm install -g vue-cli
vue -V

新建项目
vue init webpack vue-mango-music

原作者用react实现需要自定义配置，我发现vue真是智能，已经全部抽取出来在build目录下了，直接在里面改就好了啊

### 配置Stylus预处理语言
安装  
cnpm i stylus -D
cnpm i stylus-loader -D

预编译所以只在开发时候

然后在App.vue文件里面更改

<style lang="stylus" rel="stylesheet/stylus">
 // 更改成stylus代码
</style>

### 加入autoprefixer
vue-cli脚手架默认依赖autoprefixer，所以npm已经给我们装了

为了stylus和autoprefixer一起使用这里使用一款poststylus插件

npm install poststylus --D

在webpack.base.conf.js里面添加
```
plugins: [
    new webpack.LoaderOptionsPlugin({
      options: {
        stylus: {
          use: [
            poststylus([ 
              require('postcss-flexbugs-fixes'),
              autoprefixer({
                browsers: [
                  '>1%',
                  'last 4 versions',
                  'Firefox ESR',
                  'not ie < 9', // React doesn't support IE8 anyway
                ],
                flexbox: 'no-2009',
              })
            ])
          ]
        }
      }
    })
  ],
```
然后特么发现报错 **Cannot find module 'postcss-flexbugs-fixes'**

跟原作者教程不一样啊！

然后我对症下药 cnpm install postcss-flexbugs-fixes -D

就可以了，大概跟手脚架有关，问题先放在这里

然后启动   1 1 1 1 1 1 95% emitting  Compiled successfully in 486ms 

还不清楚95%什么意思，但是已经成功运行了

但是这个好像vue-cli默认添加前缀，这个可能是多此一举









