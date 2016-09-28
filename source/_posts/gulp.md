---
title: gulp
date: 2016-09-27 13:27:20
categories: 前端开发工具
tags: 
  -- gulp
  -- 前端自动化工具
---

本文将带着你构建一个完整的gulp自动化开发环境


## 安装nodejs

gulp基于nodejs的前端自动化工具,在使用前需要先安装nodejs,这里对nodejs的安装不做详细介绍具体参考nodejs官网。

## 全局安装gulp

```
$ npm install --global gulp
```

## 初始化你的项目目录

```
$ npm init
```

此时在你的项目根目录下将会出现一个package.json的文件,用来管理通过npm安装的模块


## 安装gulp到你的项目

```
$ npm install gulp --save-dev
```

查看package.json查看是否存在以下纪录,有则代表安装成功,之后在接下来所安装成功的模块都会依次出现在"devDependencies"下面

```
  "devDependencies": {
    "gulp": "^3.9.1"
  }
```


## 在跟目录下创建gulpfile.js文件

打开gulpfile.js文件,写入以下代码,加载刚刚安装的gulp模块

```
const gulp = require("gulp");
```


## 安装gulp插件

gulp有很多第三方开发的插件,这些插件都可以通过

` $ npm install packageName --save-dev ` 来进行安装

根据项目,来安装自己所需要的插件包,这篇文章里介绍几个常用的插件来做示例


### gulp-concat

gulp-concat是一个用来合并文件的插件,一般用来合并js文件较多

```
$ npm install gulp-concat --save-dev
```

#### gulpfile 示例

```
const gulp = require("gulp");
const concat = require('gulp-concat');

gulp.task('scripts', function() {
  return gulp.src('./js/*.js') //指定要被合并的文件
  .pipe(concat('app.js')) //将以上目录下的.js文件都合并到app.js文件中 
  .pipe(gulp.dest('./dist/')); //配置app.js的输出目录
});

```

### gulp-sass

gulp-sass 自动将sass文件编译成css文件

```
$ npm install gulp-sass --save-dev
```

#### gulpfile 示例

```
const gulp = require("gulp");
const sass = require('gulp-sass');

gulp.task('sass', function () {
  return gulp.src('./sass/**/*.scss')
    .pipe(sass().on('error', sass.logError))
    .pipe(gulp.dest('./dest/css/'));
});
```

### gulp-watch

gulp－watch 用来自动监控任务

```
$ npm install gulp-watch --save-dev
```

#### gulpfile 示例

```
const gulp = require("gulp");
const watch = require('gulp-watch');

gulp.task('watch',function(){
	watch(['./js/**/*.js'],function(event){
		gulp.start('scripts');
	});
	watch('./sass/**/*.scss',function(event){
		gulp.start('scripts');
	});
});

```


## gulpfile 最终代码示例

```
'use strict';

const gulp = require("gulp");
const concat = require('gulp-concat');
const sass = require('gulp-sass');
const watch = require('gulp-watch');

/*合并js*/
gulp.task('scripts', function() {
	return gulp.src('./js/**/*.js')
	.pipe(concat('app.js'))
	.pipe(gulp.dest('./dist/js/'));
});

/*编译sass*/
gulp.task('sass', function () {
	return gulp.src('./sass/**/*.scss')
	.pipe(sass().on('error', sass.logError))
	.pipe(gulp.dest('./dest/css/'));
});

/*监控*/
gulp.task('watch',function(){
	watch(['./js/**/*.js'],function(event){
		gulp.start('scripts');
	});
	watch('./sass/**/*.scss',function(event){
		gulp.start('scripts');
	});
});

gulp.task('default', ['watch']);

```

此时只要在终端输入`$ gulp` 将自动执行以上配置好的任务
只要监控的文件发生改变,将会自动执行合并js任务或编译sass任务

更完整的gulp配置可以看：[https://github.com/taoyage/gulp](https://github.com/taoyage/gulp)



