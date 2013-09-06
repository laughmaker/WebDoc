# Windows平台配置PHP开发环境
```
注：这里使用的平台为windows server 2003,其它版本可能需要根据相应环境下载相应的版本。安装过程可以自己选择安装目录，这次安装过程全部使用默认的目录。
```

## 一、安装apache
```
版本解释：
	openssl是表示带有openssl模块，利用openssl就可以给Apache配置SSL安全链接的，也就是使用https://方式进行访问。
　　 nossl则表示不带OpenSSL模块，无　法用于SSL安全链接，在这里我们下载带OpenSSL的。
下载地址：http://www.apache.org/dist/httpd/binaries/win32/
使用版本：apache httpd-2.2.25-win32-x86-openssl-0.9.8y.msi
```
### 安装过程
* 运行apache安装程序，一直next,默认是安装到C:\Program Files目录下，默认选择80端口。
* 添加PHP运行相关参数设置：打开C:\Program Files\Apache Software Foundation\Apache2.2\conf目录下的httpd.conf文档，在#LoadModule vhost_alias_module modules/mod_vhost_alias.so下添加：
	* LoadModule php5_module "C:/PHP/php5apache2_2.dll"
	* PHPIniDir "C:/PHP"
	* AddType application/x-httpd-php .php .html .htm

	需要注意的是，这三行代码后面不能有空格，否则会找不到文件，另外目录中的地址为PHP的安装地址。
* 测试一下，在C:\Program Files\Apache Software Foundation\Apache2.2\htdocs目录中新建一个index.html页面，输出hello apahce，如果能正常显示，则表示apache已正常运行。
* 停止启动apache可通过右下角的图标点击来完成。

---
 
## 二、安装MYSQL
```
版本解释：
	The Essentials Package：不包含 embedded server and benchmark suite，有自动安装程序和配置向导，没有MySQL Documentation。
	The Complete Package：包含 embedded server and benchmark suite，有自动安装程序和配置向导，有MySQL Documentation。
	The Noinstall Archive：包含 embedded server and benchmark suite，没有自动安装程序和配置向导，有MySQL Documentation。
下载地址：http://www.mysql.com/downloads/mysql/
使用版本：mysql-5.5.21-win32.zip
```

### 安装过程
* 运行.exe，一路下一步，速度安装到C:\Program Files目录下，安装完成后，选择Detailed Configuration，选择Server Machine，数据库用途选择Mutltifunctional Database，并发数、编码我使用的都是默认配置，勾上设置为windows服务，设置好root密码，下一步完成安装。
* 默认端口为3306，最好别修改，修改这里其它地方也都需要修改。

```
数据库相关说明：
	* 服务器选择：
		Developer Machine，将只用尽量少的内存;
		Server Machine，将使用中等数量内存;
		Dedicated MySQL Server Machine，这台服务器上面只跑mysql数据库，将占用全部的内存。
	* 数据库用途选择：
		Mutltifunctional Database多功能用途，将把数据库优化成很好的innodb存储类型和高效率的myisam存储类型;
		Transactional Database Only只用于事务处理类型，最好的优化innodb，但同时也支持myisam;
		Non-Transactional Databse Only非事务处理类型，适合于简单的应用，只有不支持事务的myisam类型是被支持的。
	* 并发数选择：允许的最大连接数，第一种是最大20个连接并发数，第二种是最大500个并发连接数，最后一种是自定义，自己可以根据需求选择。
```

---

## 三、安装PHP
```
版本解释：
	VC9 专门为IIS定值的脚本，使用Visual Studio 2008编译器编译，支持最新的微软组建，从而提高效率。
	VC6 是为了其他WEB服务软件提供的脚本 如 Apache。
	Thread Safe 现成安全，之星时会进行线程 安全检查，以防止有心要求就启动新线程(Thread)的CGI执行方式而耗尽系统资源。
	Non Thread Safe是非线程安全，在执行时不进行线程(Thread)安全检查
下载地址：http://windows.php.net/download/
选择版本：php-5.2.17-Win32-VC6-x86.zip
```
### 安装过程
* 将压缩包解压到C:盘根目录下改名为PHP。
* 将PHP目录下的php.ini-recommended改名为php.ini-recommended，打开php.ini.
	* 去掉以下文件前面的注释符号";"，
	
			```
			extension=php_curl.dll
			extension=php_gd2.dll
			extension=php_mbstring.dll
			extension=php_mysql.dll
			extension=php_mysqli.dll
			extension=php_pdo_mysql.dll
			extension=php_xmlrpc.dll
			```
	* 去掉;date.timezone =前的注释符号";"，并赋值为date.timezone = Asia/Shanghai。
* 设置环境变量：
	* 开始->控制面板->系统->环境变量，在系统变量一栏找到Path这一项，在里面添加PHP的安装目录，两个目录之间以";"号分隔，我的设置后如下：C:\Program Files\Parallels\Parallels Tools\Applications;%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;C:\Program Files\MySQL\MySQL Server 5.5\bin;C:\PHP;C:\PHP\ext，其中后两个是自己添加的。
	* 再新建一个系统变量，变量名为：PHPRC，值为：PHP的安装目录，我的为C:/PHP。
* 在C:\Program Files\Apache Software Foundation\Apache2.2\htdocs目录下，新建phpInfo.php文件，在里面输入

	```
	<html>
	  <body>
		<h1>It works php !</h1>
		<?php 
			phpinfo(); 
		?>
	  </body>
	</html>
	```
打开浏览器，输入http://localhost/phpInfo.php，若能显示php的相关信息，则表示PHP配置成功。


	*说明：
	* 配置完PHP后为了让环境变量生效，需要重启电脑。
	* 若php无法运行，搜索extension_dir= ,修改值为ext的安装目录，这里的目录为C:\PHP\ext，若PHP运行正常，则无需修改。*
	
## 测试PHP是否能连接到MYSQL
* C:\Program Files\Apache Software Foundation\Apache2.2\htdocs目录下新建phpMysqlTest.php,里面输入如下内容：

	```
	<html>
	  <body>
		<h1>It works php !</h1>
	
		<?php 
			$link = mysql_connect('localhost', 'root', 'wsk123,./');
			if (!$link) 
			{
			    die('Could not connect: ' . mysql_error());
			}
			else
			{
				echo 'Connected successfully';  
			}
			
			mysql_close($link);
	
			phpinfo(); 
		?>
	  </body>
	</html>
	```
	在浏览器里输入http://localhost/phpMysqlTest.php，如果显示Connected successfully，则表示PHP连接MYSql成功。


## 安装PhpMyAdmin(非必须)
```
说明：PhpMyAdmin仅作为界形界面查看数据库用，非必须使用。
下载地址：http://www.phpmyadmin.net/home_page/downloads.php。
使用版本：phpMyAdmin-4.0.5-all-languages.zip
```
### 安装过程
* 将文件解压到：C:\Program Files\Apache Software Foundation\Apache2.2\htdocs，重命名为PhpMyAdmin。
* 修改配置：打开phpMyAdmin\libraries\config.default.php文件，将以下值修改为如下：
	* $cfg['PmaAbsoluteUri'] = ‘http://localhost/PHPMyadmin/’
	* $cfg['Servers'][$i]['host'] = ‘localhost’
	* $cfg['Servers'][$i]['user'] = ‘root’
	* $cfg['Servers'][$i]['password'] = ‘Mysql PWD'
	* $cfg['Servers'][$i]['auth_type'] = ‘cookie’
	* $cfg['blowfish_secret'] = ‘cookie’
* 在浏览器输入：http://localhost/phpmyadmin/，登录访问。


## 配置smtp服务器
* 从http://glob.com.au/sendmail/下载sendmail.zip
* 解压到C:下，例如C:\PHP\sendmail，最好短路径，长路径名有可能产生问题。
* 修改sendmail.ini如下，以QQ邮箱为例:
	```
	* smtp_server=smtp.qq.com
	* smtp_port=25
	* auth_username=myQQ
	* auth_password=myPassword
	```
* 修改PHP的php.ini文件为如下，以QQ邮箱为例：
	```
	* SMTP = localhost
	* smtp_port = 25
	* sendmail_from = myQQ@qq.com
	* sendmail_path = "C:\PHP\sendmail\sendmail.exe -t"
	```
* 在C:\Program Files\Apache Software Foundation\Apache2.2\htdocs目录下新建mailTest.php,里面内容如下：

	```
	<html>
		  <body>
			<h1>php sendmail test</h1>
	
			<?php 	
				$to = "2764xxxxx@qq.com";
				$now = date("Y-m-d h:i:s");
				$from_name = "石头";
				$from_email = "myQQ@qq.com";
				$headers = "From:$from_name <$from_email>";
				$body = "我是body";
				$subject = "[$now] mail测试";
				if (mail($to, $subject, $body, $headers)) 
				{
					echo "send mail finished";
				}
				else
				{
					echo "send mail failed";
				}
			?>
		  </body>
	</html>
	```
* 登录你的QQ邮箱，在设置->帐户里，启用smpt服务。
* 在浏览器输入：http://localhost/mailTest.php，然后看提示是否成功，然后到邮箱查看。



# 总结：
* 至此，整个配置已完成，以下是几个比较有帮助的链接：
	* http://blog.sina.com.cn/s/blog_4a4a8c7d01015bwb.html
	* http://blog.sina.com.cn/s/blog_4abb9bba01019mv7.html
	* http://hi.baidu.com/zhangtianshun/item/258f8327cb2df6150875089c
	* http://hi.baidu.com/gwbjx123/item/d411b20391ba52d01ef04630
	* http://www.cnblogs.com/pharen/archive/2012/02/06/2340628.html
	* http://os.51cto.com/art/201006/204100.htm
	* http://code-tech.diandian.com/post/2013-03-23/40048264369
	* http://www.jcwcn.com/article-31817-1.html
* 若发生错误，到C:\Program Files\Apache Software Foundation\Apache2.2\logs目录下看error.log里的日志，基本上可以判断是哪里出的问题。
* 在修改配置文件后，最好重启一下apache。
