### 转到Typecho的原因

昨天晚上突然发现自己的网站加载速度非常慢，也不知道为什么,连网站的首页都很难加载出来(移动端和PC端)，不知道是不是昨天新增了一个界面，服务器压力变大。于是我打开了宝塔面板查看内存的使用程度，一看，使用的内存超过了60%，感觉确实用了挺多，但不至于让页面加载这么慢吧，疑惑。几小时后依然没有改变，于是下定决心，今晚一定要把网站所有数据迁移到Typecho上！


----------

### 选择Typecho

在各种Typecho的介绍视频里面，几乎都提到了它的轻量，相比于WordPress完善的功能，Typecho更简约，从管理界面就能看出。Typecho管理界面顶部只有四个选项，分别是***控制台 撰写 管理 设置***，基本能实现作为个人网站搭建的要求。
![屏幕截图 2024-07-06 170818.png][1]

[1]: http://slugyao.top/usr/uploads/2024/07/4285143366.png


----------
### 如何将WordPress上的数据导入Typecho中?
我使用插件进行数据迁移,插件名字叫做WordPressToTypecho，可以搜索到并下载插件的压缩包。

使用宝塔面板对网站进行数据迁移的步骤为

	1. 安装Typecho，安装网址为[Typecho Official Site](https://typecho.org/)，点击Download下载，选择正式版即可。
	1. 打开宝塔面板，点击侧边栏中“文件”选项，按照/www/wwwroot的顺序打开和域名同名的文件夹，上传Typecho压缩包，并解压。最后解压出来的所有文件都要放在以域名命名的文件夹下面。
	1. 在浏览器中输入xxx.com(你的域名)/admin进入网站的后台，注册Typecho账号并登录。
	1. 下载WordPressToTypecho插件，下载地址为[WordpressToTypecho - 将wp数据转换为typecho的插件 - 念念不忘必有回响小站](https://typecho.work/archives/WordpressToTypecho.html)。
	1. 打开宝塔面板，点击侧边栏中“文件”选项，按照/www/wwwroot/（域名）/usr/plugins的顺序打开文件夹，将WordPressToTypecho插件的压缩包上传到plugins文件夹内，并选择解压，解压后可以看见一个名为WordPressToTypecho的文件夹。
	1. 返回网站后台，点击控制台，启用WordPressToTypecho插件，填写数据库相关信息（wordpress的数据库信息）点击导入Typecho即可实现数据的迁移，如果出现database query error报错，则需要到宝塔面板中关闭数据库的严格模式，需要在控制台输入以下代码,注意，输入第一行代码后可能会要求输入数据库密码，数据库密码可在宝塔面板中数据库选项中找到。

```mysql
mysql -u root -p
SET GLOBAL sql_mode='';
exit;
```

参考:[wordpress迁移到typecho(database query error) (aoyouer.com)](https://aoyouer.com/posts/wordpress-to-typecho/)

---

### Typecho使用感受

1. 从WordPress转到Typecho后网页加载速度明显加快
2. 页面简洁许多
3. 主题和插件相比WordPress少，不能满足特定要求
4. 插件安装有点麻烦，并且存在插件无法使用的情况
5. 网站菜单栏的修改对新手来说不太友好
6. Markdown语法不能内嵌LaTex，自己配置很麻烦

**总结**：在服务器内存不大的情况下，个人博客的搭建还是很推荐Typecho的。
