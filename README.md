网上学来的，自己稍稍做了一些调整，方便流程阅读
原文网址：http://www.cnblogs.com/yexiaochai/p/3603389.html
# -grunt-
grunt打包使用方法，一步步教程

grunt是一套前端自动化工具，一个基于nodeJs的命令行工具，一般用于：
① 压缩文件
② 合并文件
③ 简单语法检查

对于其他用法，我还不太清楚，我们这里简单介绍下grunt的压缩、合并文件，初学，有误请包涵

# 准备阶段

1、nodeJs环境

因为grunt是基于nodeJs的，所以首先各位需要安装nodeJS环境，这块我们便不管了
http://www.cnblogs.com/yexiaochai/p/3527418.html

2、安装grunt

有了nodeJs环境后，我们便可以开始搞grunt了，因为我们可能在任何目录下运行打包程序，所以我们需要安装CLI
官方推荐在全局安装CLI（grunt的命令行接口）

`npm install -g grunt-cli`
这条命令将会把grunt命令植入系统路径，这样就能在任意目录运行他，原因是

每次运行grunt时，它都会使用node的require查找本地是否安装grunt，如果找到CLI便加载这个本地grunt库
然后应用我们项目中的GruntFile配置，并执行任务
PS：这段先不要管，安装完了往下看

#实例学习：打包zepto

一些东西说多了都是泪，直接先上实例吧，实例结束后再说其它的
首先在E盘新建一个项目（文件夹就好）
在里面新增两个文件（不要问为什么，搞进去先）

① package.json
内容如下：
```  
{
  "name": "demo",
  "file": "base",  //此处为要压缩的文件名
  "version": "0.1.0",
  "description": "demo",
  "license": "MIT",
  "devDependencies": {
    "grunt": "~0.4.1",
    "grunt-contrib-jshint": "~0.6.3",
    "grunt-contrib-uglify": "~0.2.1",
    "grunt-contrib-requirejs": "~0.4.1",
    "grunt-contrib-copy": "~0.4.1",
    "grunt-contrib-clean": "~0.5.0",
    "grunt-strip": "~0.2.1"
  },
  "dependencies": {
    "express": "3.x"
  }
}
```
② Gruntfile.js 
先不写内容

完了我们需要在grunt目录下执行 npm install将相关的文件下载下来：
```
$ e:
$ cd grunt
```
![](https://github.com/yarlinLee/-grunt-/raw/master/images/cd.jpg)   </br>
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian1.jpg)  </br>
然后我们的目录就会多一点东西： </br>
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian2.jpg)  </br>
多了很多东西，先别管事干什么的，我们后面都会用到，这个时候在目录下新建js文件夹，并且搞一个base.js进去 </br>
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian3.jpg) </br>
然后在Gruntfile.js中新增以下代码（先别管，增加再说） </br>
```
module.exports = function (grunt) {
  // 项目配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.file %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'js/<%=pkg.file %>.js',
        dest: 'min-js/<%= pkg.file %>.min.js'
      }
    }
  });
  // 加载提供"uglify"任务的插件
  grunt.loadNpmTasks('grunt-contrib-uglify');
  // 默认任务
  grunt.registerTask('default', ['uglify']);
}
```
然后运行 grunt命令后
![](https://github.com/yarlinLee/-grunt-/raw/master/images/cd2.jpg)
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian4.jpg)
嗯嗯，多了一个文件，并且是压缩的，不差！！！第一步结束


