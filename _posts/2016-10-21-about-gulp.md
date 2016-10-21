---
layout: wp
title: about gulp
---

在执行gulp的时候报错：

```
NodeJS: throw er; //Unhandled 'error' event (events.js:72) when using child_process spawn method
```

我在spawn的时候加error监听：
```
shell.on('error', function(err){
                console.log(process.env.PATH);
                console.log(err);
            });
```

发现：

```
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin://sbin:/bin:/usr/sbin:/usr/bin:/usr/X11R6/bin:/home/work/app/jdk/bin:/root/bin
error { [Error: spawn ENOENT] code: 'ENOENT', errno: 'ENOENT', syscall: 'spawn' } 
```

这里执行的是php命令，因此，怀疑是php环境变量没有设置，添加环境变量后OK。 


我现在的项目使用的是grunt,速度执行有点慢，现在切换到gulp。以下是一个简单的gulp介绍。  


#### install #### 

```
npm install -g gulp
```

#### 项目设置 ####

```
npm install gulp
```

编辑gulpfile.js

```
vi gulpfile.js

var gulp = require('gulp');

gulp.task('default', function() {
    
});

```

#### gulp api #### 

gulp提供4个API函数，

##### gulp.src(globs[,options]) #####

用来获取文件流。

globs为文件匹配模式，例子:

```
js/app.js：指定确切的文件名。
js/*.js：某个目录所有后缀名为js的文件。
js/**/*.js：某个目录及其所有子目录中的所有后缀名为js的文件。
!js/app.js：除了js/app.js以外的所有文件。
*.+(js  css)：匹配项目根目录下，所有后缀名为js或css的文件。
```

##### gulp.dest(path[,options])  #####

用来写文件，输出文件流。dest方法将管道的输出写入文件，同时将这些输出继续输出，所以可以依次调用多次dest方法，将输出写入多个目录。如果有目录不存在，将会被新建。生成的文件路径是我们传入的path参数后面再加上gulp.src()中有通配符开始出现的那部分路径。

gulp的path参数会替换掉src中的通配符出现前的base路径，如果src options中配置了base参数，那dest的path将替换掉src的base。  

eg:

```
gulp.src('source/js/**/*.js', { base: './'})
    .pipe(gulp.dest('output'));
```
假如有一个source/js/app.js,结果为./output/source/js/app.js



##### gulp.task(name[,deps], fn) #####

定义任务：  
name: 任务名  
deps: 任务依赖的任务列表  
fn: 任务函数  



##### gulp.watch(glob[, opts], tasks/fn) #####

监听文件的变化:  
glob 文件的监听匹配模式，和gulp.src相同  
tasks: 文件变化后要执行的task   
fn: 文件变化后要执行的函数


##### 完成的gulpfile.js ####  

以下是我的项目用到的gulpfile.js 

```
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    cssmin = require('gulp-cssmin'),
    rename = require('gulp-rename'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    pump = require('pump'),
    concat = require('gulp-concat');



gulp.task('sass', function(){
    return gulp.src('source/scss/**/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('source/dist'));
});

gulp.task('cssmin', function(){
    return gulp.src(['bower_components/nprogress/nprogress.css',
        'bower_components/bootstrap-tagsinput/dist/bootstrap-tagsinput.css',
        'source/dist/application.css'
        ])
    .pipe(cssmin())
    .pipe(rename('app.css'))
    .pipe(gulp.dest('css'));
});

gulp.task('jshint', function(){
    return gulp.src(['source/js/**/*.js', '!source/js/libs/**/*.js'])
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});

gulp.task('uglify', function (cb) {
  pump([
        gulp.src('source/js/**/*.js'),
        uglify(),
        gulp.dest('source/dist')
    ],
    cb
  );
});

gulp.task('uglify2', function (cb) {
  pump([
        gulp.src('bower_components/nprogress/nprogress.js'),
        uglify(),
        gulp.dest('bower_components/nprogress/dist')
    ],
    cb
  );
});


gulp.task('concat', function(){
    return gulp.src([
                    'bower_components/nprogress/dist/nprogress.js',
                    'bower_components/doT/doT.min.js',
                    'bower_components/bootstrap-tagsinput/dist/bootstrap-tagsinput.min.js',
                    'source/dist/*.js'
                ])
    .pipe(concat('app.js'))
    .pipe(gulp.dest('js'));
});

gulp.task('watch', function(){
    gulp.watch(['source/scss/*.scss'], ['sass', 'cssmin']);
    gulp.watch(['source/js/*.js'], ['jshint', 'uglify', 'uglify2','concat']);
});

gulp.task('default', ['watch']);
gulp.task('refresh', ['sass' ,'cssmin', 'jshint', 'uglify', 'uglify2', 'concat']);
```



#### 参考资料 ####

[gulp docs](https://github.com/gulpjs/gulp/blob/master/docs/README.md)  
[gulp基础](http://www.reeoo.me/archives/gulpjs.html)  
[gulp教程](http://javascript.ruanyifeng.com/tool/gulp.html)  


