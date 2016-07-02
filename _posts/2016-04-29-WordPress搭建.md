#WordPress搭建
* 1.下载解压wordpress

* 2.为Wordpress创建数据库
    * 2.1 创建database
            create database wpdb;
    * 2.2 创建用户
            create user wpuser@localhost;
    * 2.3 设置密码
            set password for 
            wpuser@localhost = password('passwd');
    * 2.4 修改权限
            grant all privileges on wpdb.* to 
            wpuser@localhost identified by 'passwd'; 
    * 2.5 刷新&退出
            flush privileges;
            exit
* 3.修改WordPress配置文件  
    * 3.1 打开配置文件
            cp wp-config-sample.php wp-config.php
            vi wp-config.php
    * 3.2 修改
        ```
        /** The name of the database for WordPress */
        define('DB_NAME', 'wpdb');

        /** MySQL database username */
        define('DB_USER', 'wpuser');

        /** MySQL database password */
        define('DB_PASSWORD', 'passwd');
        ```
* 4.其他环境
     ```
     apt-get install php5-mysql
     apt-get install php5-gd
     apt-get install php5
     apt-get install mysql-server
     apt-get install apache2
     ```       
    