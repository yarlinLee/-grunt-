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
然后运行 grunt命令后  </br>
![](https://github.com/yarlinLee/-grunt-/raw/master/images/cd2.jpg)  </br>
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian4.jpg)  </br>
嗯嗯，多了一个文件，并且是压缩的，不差！！！第一步结束

#认识Gruntdile与package.json
不出意外，每一个gurnt都会需要这两个文件，并且很可能就只有这两个文件（复杂的情况有所不同）</br>
###package.json

这个文件用来存储npm模块的依赖项（比如我们的打包若是依赖requireJS的插件，这里就需要配置）
然后，我们会在里面配置一些不一样的信息，比如我们上面的file，这些数据都会放到package中
对于package的灵活配置，我们会在后面提到

###Gruntfile

这个文件尤其关键，他一般干两件事情：
① 读取package信息
② 插件加载、注册任务，运行任务（grunt对外的接口全部写在这里面）

Gruntfile一般由四个部分组成
① 包装函数
这个包装函数没什么东西，意思就是我们所有的代码必须放到这个函数里面
```
module.exports = function (grunt) {
//你的代码
}
```
这个不用知道为什么，直接将代码放入即可

② 项目/任务配置
我们在Gruntfile一般第一个用到的就是initConfig方法配置依赖信息

`pkg: grunt.file.readJSON('package.json')` </br>
这里的 grunt.file.readJSON就会将我们的配置文件读出，并且转换为json对象

然后我们在后面的地方就可以采用pkg.XXX的方式访问其中的数据了
值得注意的是这里使用的是underscore模板引擎，所以你在这里可以写很多东西

uglify是一个插件的，我们在package依赖项进行了配置，这个时候我们为系统配置了一个任务
uglify（压缩），他会干这几个事情：

① 在src中找到zepto进行压缩（具体名字在package中找到）
② 找到dest目录，没有就新建，然后将压缩文件搞进去
③ 在上面加几个描述语言

这个任务配置其实就是一个方法接口调用，按照规范来就好，暂时不予关注，内幕后期来
这里只是定义了相关参数，但是并未加载实际函数，所以后面马上就有一句：

`grunt.loadNpmTasks('grunt-contrib-uglify');`
用于加载相关插件

最后注册一个自定义任务（其实也是默认任务），所以我们下面的命令行是等效的：

`grunt == grunt uglify`
至此，我们就简单解析了一番grunt的整个操作，下面来合并文件的例子

#合并文件

合并文件依赖于grunt-contrib-concat插件，所以我们的package依赖项要新增一项
```
"devDependencies": {
  "grunt": "~0.4.1",
  "grunt-contrib-jshint": "~0.6.3",
  "grunt-contrib-concat": "~0.3.0",
  "grunt-contrib-uglify": "~0.2.1",
  "grunt-contrib-requirejs": "~0.4.1",
  "grunt-contrib-copy": "~0.4.1",
  "grunt-contrib-clean": "~0.5.0",
  "grunt-strip": "~0.2.1"
},
```
然后再将代码写成这个样子
```
module.exports = function (grunt) {
  // 项目配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    concat: {
      options: {
        separator: ';'
      },
      dist: {
        src: ['src/zepto.js', 'src/underscore.js', 'src/backbone.js'],
        dest: 'dest/libs.js'
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-concat');
  // 默认任务
  grunt.registerTask('default', ['concat']);
}
```
运行后，神奇的一幕发生了：


