使用构建工具去对 JS、CSS、HTML、LESS、IMG 等进行合并压缩构建到最后实现自动化构建项目。是前端工程师必备的技能之一。
本套视频主要讲解当前开发中最流行的三种自动化构建工具: Grunt, Gulp, webpack。从理解什么是构建工具，为什么要用构建工具，到如何去使用构建工具。
学习本套视频之前 建议先学习 JS 模块化。

* Gulp介绍
  * 中文主页: http://www.gulpjs.com.cn/
  * gulp是与grunt功能类似的**前端项目构建**工具, 也是基于Nodejs的自动**任务运行器**
  * 能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的
    合并、压缩、检查、监听文件变化、浏览器自动刷新、测试等任务
  * gulp更高效(异步多任务), 更易于使用, 插件高质量
Gulp：
    1.任务化：统一接口注册一个任务(task())
    2.基于流：流（数据流 I/O）
    3.gulp： 有自己的内存=》将你所有的文件读到内存中然后所有的操作都是在gulp的内存中
           经行合并压缩任务===》》操作完任务在输出到你本地
           输入流：本地读取到gulp内存中  （API：src() ===》https://www.gulpjs.com.cn/docs/api/src/）
           输出流：在gulp内存中操作完之后在输出 （API:dest()==>https://www.gulpjs.com.cn/docs/api/dest/）
    4.插件：https://gulpjs.com/plugins

插件
* 使用gulp插件
  * 相关插件:
    * gulp-concat : 合并文件(js/css)
    * gulp-uglify : 压缩js文件
    * gulp-rename : 文件重命名
    * gulp-less : 编译less
    * gulp-clean-css : 压缩css
    * gulp-livereload : 实时自动编译刷新
  * 重要API
    * gulp.src(filePath/pathArr) : 
      * 指向指定路径的所有文件, 返回文件流对象
      * 用于读取文件
    * gulp.dest(dirPath/pathArr)
      * 指向指定的所有文件夹
      * 用于向文件夹中输出文件
    * gulp.task(name, [deps], fn) 
      * 定义一个任务
    * gulp.watch() 
      * 监视文件的变化

压缩指令 gulp js


### 压缩 js
    npm install gulp-concat gulp-uglify gulp-rename --save-dev
    注意事项：
    (function () {
    	// 用不了 es6
    	function add(num1, num2) {
    		var num3 = 0;
    		num1 = num2 + num3;
    		return num1 + num2;
    	}
    	//console.log(add(10, 30));
    })();
    
    如果你定义的变量函数没有使用那么压缩的时候会把他去掉 也就是说 上面的代码压缩文件中没有

### css
    npm install gulp-less gulp-clean-css --save-dev (压缩)
    //注册编译less的任务

    gulp.task('less', function () {
    	return gulp.src('src/less/*.less')
    		.pipe($.less())//将less文件转换为css文件
    		.pipe(gulp.dest('src/css/'))//输出到css下面，为了和之前的合并
    		//.pipe(livereload())//实时加载
    		.pipe($.connect.reload())
    });
    
    //注册合并压缩css的任务
    gulp.task('css',['less'] ,function () {
    	return gulp.src('src/css/*.css')
    		.pipe($.concat('build.css')) //合并后的名字
    		.pipe(gulp.dest('dist/css/')) //输出
    		.pipe($.rename({suffix:'.min'})) //重命名
    		.pipe($.cleanCss({compatibility: 'ie8'})) //兼容到ie8压缩代码
    		.pipe(gulp.dest('dist/css/'))
    		//.pipe(livereload())//实时加载
    		.pipe($.connect.reload())
    
    });
#### 处理html
      * 下载插件:
        ```
        npm install gulp-htmlmin --save-dev
        ```
      * 配置编码
        ```
        var htmlmin = require('gulp-htmlmin');
        //压缩html任务
        gulp.task('htmlMinify', function() {
            return gulp.src('index.html')
                .pipe(htmlmin({collapseWhitespace: true}))
                .pipe(gulp.dest('dist'));
        });
        gulp.task('default', ['minifyjs', 'cssTask', 'htmlMinify']);
        ```
      * 修改页面引入
        ```
        <link rel="stylesheet" href="css/built.min.css">
        <script type="text/javascript" src="js/built.min.js"></script>
        ```
      * 打包测试: gulp    
#### 自动编译：
     改代码自己编译
     * 自动编译
    * 下载插件
      ```
      npm install gulp-livereload --save-dev
      ```
    * 配置编码:
      ```
      var livereload = require('gulp-livereload');
                
      //所有的pipe
      .pipe(livereload());
      
      gulp.task('watch', ['default'], function () {    
        //开启监视
        livereload.listen();
        //监视指定的文件, 并指定对应的处理任务
        gulp.watch('src/js/*.js', ['minifyjs'])
        gulp.watch(['src/css/*.css','src/less/*.less'], ['cssTask','lessTask']);
      });
      ```
      
    * 热加载(实时加载)
        * 下载插件：gulp-connect
        ```
        1、 npm install gulp-connect --save-dev
        2、 注册 热加载的任务 server，注意依赖build任务 
        3、注册热加载的任务
            //配置加载的选项
            connect.server({
                  root : 'dist/',//监视的源目标文件路径
                  livereload : true,//是否实时刷新
                  port : 5000//开启端口号
             });
             // 自动开启链接
             open('http://localhost:5000');//npm install open --save-dev
             // 监视目标文件
            gulp.watch('src/js/*.js', ['js']);
            gulp.watch(['src/css/*.css', 'src/css/*.less'], ['cssMin', 'less']);
        ```

### 自己打开浏览器 
    var open = require('open');


###  扩展 不用引入其他包
        * 打包加载gulp插件
        * 前提：将插件下载好。
        * 下载打包插件： gulp-load-plugins
        * npm install gulp-load-plugins --save-dev
        * 引入： var $ = require('gulp-load-plugins')();！！！引入的插件是个方法，必须记住调用。
        * 神来之笔：其他的插件不用再引入了
        * 使用方法：
            报未定义可能是你自己驼峰命名影响
            * 所有的插件用 $ 引出，其他插件的方法名统一为插件的功能名字(即插件名字的最后一部分)：如：concat,connect,cssmin...
            gulp.task('less', function () {
  				return gulp.src('src/less/*.less')
			    .pipe($.less())//将less转换为less
			    .pipe(gulp.dest('src/css/'))//将转换为less的文件输出到src下
			    .pipe($.livereload())//实时刷新
			    .pipe($.connect.reload())
			});

