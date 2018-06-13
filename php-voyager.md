---
title:  PHP voyager项目安装
date: 2018-06-13 18:59:00
tags: [PHP, voyager, quickstart]
categories:  PHP
---

### PHP安装voyager基础项目

> 测试安装的环境为PHP 7.2.6+MySQL 5.7.22

##### PHP环境安装

```
# 下载PHP的安装包(zip版),PHP版本要7以上
# 在环境变量中加入PHP的环境变量
# 下载安装composer
# 在php.ini打开pdo和fileinfo和GD的扩展
extension=fileinfo
extension=pdo_mysql
extension=gd2
```
##### 初始化一个Laravel项目

```
# Laravel版本要5.6以上
shell> composer create-project laravel/laravel php-voyager-quickstart --prefer-dist "5.6.*"
```
##### 安装voyager

```
shell> cd php-voyager-quickstart
shell> composer require tcg/voyager
```
##### 配置数据库 (数据库版本5.7)

```
mysql> CREATE USER 'voyager'@'localhost' IDENTIFIED BY  'voyager@123';
mysql> CREATE SCHEMA `voyager` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
mysql> GRANT ALL PRIVILEGES ON voyager.* TO 'voyager'@'localhost' WITH GRANT OPTION;
```
##### 配置voyager数据库连接(更改.env文件)

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=voyager
DB_USERNAME=voyager
DB_PASSWORD=voyager@123
```
#####  初始化voyager项目

```
shell> php artisan voyager:install --with-dummy
```
##### 启动和访问项目

```
# 默认的用户密码admin@admin.com/password
shell> php artisan serve #  [http://localhost:8000/admin](http://localhost:8000/admin)
```
##### 汉化

```
# 登录系统后在用户信息编辑页面,更改用户语言问zh_CN
# 更改Laravel的配置 config/app.php locale => 'zh_CN'
```

##### ERROR (安装错误解决)

```
# 可能会出现"Specified key was too long error."的报错.这是一个laravel5.4就有的一个问题,解决方法如下:修改App/Providers/AppServiceProvider.php文件，代码如下；
use Illuminate\Support\Facades\Schema; 
public function boot() { 
	Schema::defaultStringLength(191); 
}
```