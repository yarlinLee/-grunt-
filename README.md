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

#实例学习：打包base.js

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

① 在js中找到base.js进行压缩（具体名字在package中找到）
② 找到min-js目录，没有就新建，然后将压缩文件搞进去
③ 在上面加几个描述语言

这个任务配置其实就是一个方法接口调用，按照规范来就好，暂时不予关注，内幕后期来
这里只是定义了相关参数，但是并未加载实际函数，所以后面马上就有一句：

`grunt.loadNpmTasks('grunt-contrib-uglify');`
用于加载相关插件

最后注册一个自定义任务（其实也是默认任务），所以我们下面的命令行是等效的：

`grunt == grunt uglify`
至此，我们就简单解析了一番grunt的整个操作，下面来合并文件的例子

#合并文件

合并文件依赖于grunt-contrib-concat插件，
如果grunt->node_modules里没有grunt-contrib-concat改插件，则需要用npm安装
`npm install grunt-contrib-concat`

我们的package依赖项要新增一项

```
"devDependencies": {
  "grunt": "~0.4.1",
  "grunt-contrib-jshint": "~0.6.3",
  "grunt-contrib-concat": "~1.0.1",  //新增concat插件
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
    concat: {  //用concat
      options: {
        separator: ';' //此处做修改
      },
      dist: {
        src: ['js/enterorder.js', 'js/index.js', 'js/goodsdetail.js'],
        dest: 'min-js/market.js'
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-concat');  //用concat
  // 默认任务
  grunt.registerTask('default', ['concat']);  //用concat
}
```
运行后，神奇的一幕发生了：

![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian5.jpg)  </br>
三个文件被压缩成了一个，但是没有压缩，所以，我们这里再加一步操作，将之压缩后再合并

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
        src: ['js/enterorder.js', 'js/index.js', 'js/goodsdetail.js'],
        dest: 'min-js/market.js'
      }
    },
    uglify: {
      build: {
        src: 'min-js/market.js',
        dest: 'min-js/market.min.js'
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.loadNpmTasks('grunt-contrib-concat');
  // 默认任务
  grunt.registerTask('default', ['concat', 'uglify']);
}
```
我这里的做法是先合并形成一个market，然后再将libs压缩成market.min.js

![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian6.jpg)  </br>

我们这里可以换个做法，先压缩再合并，其实unglify已经干了这些事情了
```
module.exports = function (grunt) {
  // 项目配置
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      "my_target": {
        "files": {
          'min-js/total.min.js': ['js/base.js', 'js/index.js', 'js/enterorder.js', 'js/goodsdetail.js'] //合并文件
        }
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-uglify');
  // 默认任务
  grunt.registerTask('default', ['uglify']);
}
```
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian7.jpg)  </br>
所以，我们就暂时不去关注concat了,unglify可以全包

#grunt插件
学习grunt主要就是学习grunt的插件使用，所以我们今天先来学习常用的几个插件

##grunt-contrib-unglify

我们仍然以简单例子学习

复制代码
```
module.exports = function (grunt) {
  grunt.initConfig({
    uglify: {
      my_target: {
        files: {
          'min-js/libs.min.js': ['js/index.js', 'js/goodsdetail.js']
        }
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.registerTask('default', ['uglify']);
}
```
这样会将js里面的index.js等文件打包合并到min-js的lib.min.js中

###压缩一个文件夹的所有文件

然后这段代码非常有意思，他会将一个文件目录里面的所有js文件打包到另一个文件夹

```
module.exports = function (grunt) {
  grunt.initConfig({
    uglify: {
      my_target: {
        files: [{
          expand: true,
          cwd: 'js',
          src: '**/*.js',
          dest: 'min-js'
        }]
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-uglify');
  grunt.registerTask('default', ['uglify']);
}
```
![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian8.jpg)  </br>

若是你希望给你文件的头部加一段注释性语言配置banner信息即可

```
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! 注释信息 */'
    },
    my_target: {
      files: {
        'min-js/index.min.js': ['js/index.js']
      }
    }
  }
});
```
##grunt-contrib-concat
该插件主要用于代码合并，将多个文件合并为一个，我们前面的uglify也提供了一定合并的功能
在可选属性中我们可以设置以下属性：</br>
① separator 用于分割各个文件的文字， </br>
② banner 前面说到的文件头注释信息，只会出现一次 </br>
③ footer 文件尾信息，只会出现一次 </br>
④ stripBanners去掉源代码注释信息（只会清楚/**/这种注释）

一个简单的例子：

```
module.exports = function (grunt) {
  grunt.initConfig({
  concat: {
    options: {
      separator: '/*分割*/',  //拼接不同页面代码之间加/分割/
      banner: '/*测试*/',
      footer: '/*footer*/'
     
    },
    dist: {
      src: ['js/index.js', 'js/enterorder.js', 'js/goodsdetail.js'],
      dest: 'min-js/built.js',
    }
  }
});
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.registerTask('default', ['concat']);
}
```
合并三个文件为一个，这种在我们源码调试时候很有意义

###构建两个文件夹

有时候我们可能需要将合并的代码放到不同的文件，这个时候可以这样干

```
module.exports = function (grunt) {
  grunt.initConfig({
    concat: {
      basic: {
        src: ['js/index.js'],
        dest: 'min-js/in.js'
      },
      extras: {
        src: ['js/enterorder.js', 'js/goodsdetail.js'],
        dest: 'min-js/market.js'
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.registerTask('default', ['concat']);
}
```
这种功能还有这样的写法：

```
module.exports = function (grunt) {
  grunt.initConfig({
    concat: {
      basic_and_extras: {
        files: {
          'min-js/in.js': ['js/index.js'],
          'min-js/market.js': ['js/enterorder.js', 'js/goodsdetail.js']
        }
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.registerTask('default', ['concat']);
}
```

![](https://github.com/yarlinLee/-grunt-/raw/master/images/wenjian9.jpg)  </br>
第二种写法便于使用配置文件，具体各位选取吧，至于读取配置文件的东西我们这里就先不关注了

##grunt-contrib-jshint

该插件用于检测文件中的js语法问题，比如我test.js是这样写的：

`alert('我是李小丫')` 
```
module.exports = function (grunt) {
  grunt.initConfig({
    jshint: { 
      all: ['js/test.js']
    }
  });
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.registerTask('default', ['jshint']);
}
```
运行结果是：
```
$ grunt jshint
Running "jshint:all" (jshint) task
Linting src/test.js ...ERROR
[L1:C15] W033: Missing semicolon.
alert('我是李小丫')
```
说我缺少一个分号，好像确实缺少.....如果在里面写明显的BUG的话会报错
多数时候，我们认为没有分号无伤大雅，所以，我们文件会忽略这个错误：
```
jshint: {
  options: {   //忽略缺少分号错误
    '-W033': true 
  },
  all: ['js/test.js']
}
```
这里有一个稍微复杂的应用，就是我们合并之前做一次检查，合并之后再做一次检查，我们可以这样写

```
module.exports = function (grunt) {
  grunt.initConfig({
    concat: {
      dist: {
        src: ['js/test01.js', 'js/test02.js'],
        dest: 'min-js/output.js'
      }
    },
    jshint: {
      options: {
        '-W033': true 
      },
      pre: ['js/test01.js', 'js/test02.js'],
      after: ['min-js/output.js']
    }
  });
  grunt.loadNpmTasks('grunt-contrib-concat');
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.registerTask('default', ['concat', 'jshint']);
}
```
![](https://github.com/yarlinLee/-grunt-/raw/master/images/cd3.jpg)  </br>
这里连续运行了三个任务，先做检查再合并，然后做检测，我这里写了两个简单的文件，如果将jquery搞进去的话，好像还出了不少BUG......
所以真的要用它还要自定一些规范，我们这里暂时到这里，先进入下一个插件学习

下一个插件是grunt-contrib-requirejs，因为还没用require，所以先不分享了，有兴趣看http://www.cnblogs.com/yexiaochai/p/3603389.html
