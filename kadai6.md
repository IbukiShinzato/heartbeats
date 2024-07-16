### MySQLのバージョンは8系をインストールしてください

mysqlサーバーのインストール
```
root@ubuntu:~# apt-get install mysql-server
```

サーバーの起動確認
```
root@ubuntu:~# systemctl status mysql.service
● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-07-15 20:15:08 JST; 1min 31s ago
    Process: 19710 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
   Main PID: 19718 (mysqld)
     Status: "Server is operational"
      Tasks: 37 (limit: 9433)
     Memory: 365.7M
        CPU: 1.698s
     CGroup: /system.slice/mysql.service
             └─19718 /usr/sbin/mysqld

Jul 15 20:15:07 ubuntu systemd[1]: Starting MySQL Community Server...
Jul 15 20:15:08 ubuntu systemd[1]: Started MySQL Community Server.
```

activeより起動している

### MySQLは自動起動するようにしてください

自動起動の確認
```
root@ubuntu:~# systemctl list-unit-files --type=service | grep mysql
mysql.service                           enabled         enabled
```

enabledより自動起動される

### MySQLに新しく`hbtask`というデータベースを作成してください

mysqlの接続方法

```
root@ubuntu:/# man mysql

mysql --user=user_name --password db_name
```

rootでログイン
```
root@ubuntu:/# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.37-0ubuntu0.22.04.3 (Ubuntu)

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

hbtaskデータベースを作成
```
mysql> create database hbtask;
Query OK, 1 row affected (0.02 sec)
```

確認
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| hbtask             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

hbtaskデータベースの選択
```
mysql> use hbtask;
Database changed
```

テーブルの確認
```
mysql> show tables;
Empty set (0.00 sec)
```

現在はテーブルが何もない状態である。

meiboテーブルの作成
```
mysql> CREATE TABLE meibo (number INT, username VARCHAR(255));
Query OK, 0 rows affected (0.05 sec)
```

テーブルの確認
```
mysql> show tables;
+------------------+
| Tables_in_hbtask |
+------------------+
| meibo            |
+------------------+
1 row in set (0.01 sec)

mysql> DESCRIBE meibo;
+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| number   | int          | YES  |     | NULL    |       |
| username | varchar(255) | YES  |     | NULL    |       |
+----------+--------------+------+-----+---------+-------+
2 rows in set (0.01 sec)
```

### `meibo`テーブルに下記データを登録してください
    - `number`: 1
    - `username`: <自分のフルネーム>

現在のデータ
```
mysql> SELECT * FROM meibo;
Empty set (0.02 sec)
```

データの挿入
```
mysql> INSERT INTO meibo VALUES (1, "Shinzato Ibuki");
Query OK, 1 row affected (0.00 sec)
```

データの確認
```
mysql> SELECT * FROM meibo;
+--------+----------------+
| number | username       |
+--------+----------------+
|      1 | Shinzato Ibuki |
+--------+----------------+
1 row in set (0.00 sec)
```

### Webブラウザで`http://<自分の名前>-hbtask.local/mysql.php`にアクセスすると、`meibo`テーブルに登録した自分のフルネームが表示される
    - `meibo`テーブルから、`number`カラムが1の`username`を抽出させる
    - 表示例: 「私の名前は Kuramochi Wataru です。」

mysql.phpファイルの作成
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# touch mysql.php
```

```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# curl -I "http://shinzato-hbtask.local/mysql.php"
HTTP/1.0 500 Internal Server Error
Date: Tue, 16 Jul 2024 04:29:10 GMT
Server: Apache/2.4.52 (Ubuntu)
Connection: close
Content-Type: text/html; charset=UTF-8
```

serverエラーが出たのでエラーログを確認

```
[Tue Jul 16 13:26:14.966701 2024] [php:error] [pid 751] [client 127.0.0.1:42074] PHP Fatal error:  Uncaught Error: Class "mysqli" not found in /var/www/shinzato-hbtask.local/public_html/mysql.php:9\nStack trace:\n#0 {main}\n  thrown in /var/www/shinzato-hbtask.local/public_html/mysql.php on line 9
```

mysqliをインストールする必要がある。

phpのバージョンに合わせたmysqliをインストール
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# php -v
PHP 7.4.33 (cli) (built: Jun  6 2024 16:48:51) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.33, Copyright (c), by Zend Technologies

root@ubuntu:/var/www/shinzato-hbtask.local/public_html# apt-get install php7.4-mysql
```

apacheの再起動
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# systemctl restart apache2
```

mysqliの確認
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# php -m | grep mysqli
mysqli
```

コメントアウトを外す
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# cat -n /etc/php/7.4/apache2/php.ini | grep extension=mysqli
   895	;   extension=mysqli
   926	;extension=mysqli
```

phpディレクトリ
```
root@ubuntu:/# find . -type d -name php
./usr/share/doc/php
./usr/lib/php
./etc/php
./var/lib/php
```

apacheのPHPのバージョンが異なっている可能性がある。
```
root@ubuntu:/# ls /etc/apache2/mods-enabled | grep php
php8.1.conf
php8.1.load
```

現在のapacheではphp8.1が使われている、しかし実際に使用するphpのバージョンは7.4である。

apacheのphpのバージョンを7.4に変更
```
root@ubuntu:/# a2dismod php8.1
Module php8.1 disabled.
To activate the new configuration, you need to run:
  systemctl restart apache2

root@ubuntu:/# ls /etc/apache2/mods-enabled | grep php

root@ubuntu:/# a2enmod php7.4
Considering dependency mpm_prefork for php7.4:
Considering conflict mpm_event for mpm_prefork:
Considering conflict mpm_worker for mpm_prefork:
Module mpm_prefork already enabled
Considering conflict php5 for php7.4:
Enabling module php7.4.
To activate the new configuration, you need to run:
  systemctl restart apache2

root@ubuntu:/# ls /etc/apache2/mods-enabled | grep php
php7.4.conf
php7.4.load
```

apacheの再起動
```
root@ubuntu:/# systemctl restart apache2
```

動作確認
```
root@ubuntu:/# curl -I "http://shinzato-hbtask.local/mysql.php"
HTTP/1.1 200 OK
Date: Tue, 16 Jul 2024 08:38:49 GMT
Server: Apache/2.4.52 (Ubuntu)
Content-Type: text/html; charset=UTF-8
```