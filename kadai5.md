### PHPのバージョンは7系をインストールしてください

phpが存在するかの確認
```
root@ubuntu:/home/e235718# which php
```

phpのインストール
```
root@ubuntu:/home/e235718# apt-get install php
```

再確認
```
root@ubuntu:/home/e235718# which php
/usr/bin/php
```

バージョン確認
```
root@ubuntu:/home/e235718# php -v
PHP 8.1.2-1ubuntu2.18 (cli) (built: Jun 14 2024 15:52:55) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.1.2, Copyright (c) Zend Technologies
    with Zend OPcache v8.1.2-1ubuntu2.18, Copyright (c), by Zend Technologies
```

現在のphpのバージョンは8.1なので、7系に変更


software-properties-commonパッケージをインストール
```
root@ubuntu:/home/e235718# apt install software-properties-common
```

リポジトリを追加
```
root@ubuntu:/home/e235718# add-apt-repository ppa:ondrej/php
```

phpバージョン7.4をインストール
```
root@ubuntu:/home/e235718# apt-get install php7.4
```

7.4がインストールされていることを確認
```
root@ubuntu:/usr/lib/php# ls
20190902  20210902  7.4  8.1  packaging  php-fpm-socket-helper  php-helper  php-maintscript-helper  sessionclean
```

しかし、現在はphp8.1にリンクされている。
```
root@ubuntu:/usr/lib/php# ls -l /usr/bin/php*
lrwxrwxrwx 1 root root      21 Jul 15 18:27 /usr/bin/php -> /etc/alternatives/php
-rwxr-xr-x 1 root root 4813912 Jun  7 01:48 /usr/bin/php7.4
-rwxr-xr-x 1 root root 5531064 Jun 15 00:52 /usr/bin/php8.1

root@ubuntu:/usr/lib/php# ls -l /etc/alternatives/php
lrwxrwxrwx 1 root root 15 Jul 15 18:27 /etc/alternatives/php -> /usr/bin/php8.1
```

バージョンの切り替え
```
root@ubuntu:/usr/lib/php# update-alternatives --config php
There are 2 choices for the alternative php (providing /usr/bin/php).

  Selection    Path             Priority   Status
------------------------------------------------------------
* 0            /usr/bin/php8.1   81        auto mode
  1            /usr/bin/php7.4   74        manual mode
  2            /usr/bin/php8.1   81        manual mode

Press <enter> to keep the current choice[*], or type selection number: 1
update-alternatives: using /usr/bin/php7.4 to provide /usr/bin/php (php) in manual mode
```

バージョンの再確認
```
root@ubuntu:/usr/lib/php# php -v
PHP 7.4.33 (cli) (built: Jun  6 2024 16:48:51) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.33, Copyright (c), by Zend Technologies
```

php7.4バージョンに変更完了

### Webブラウザで`http://<自分の名字>-hbtask.local/phpinfo.php`にアクセスすると、phpinfoの情報が表示される

/var/www/shinzato-hbtask.local/public_htmlにphpinfo.phpファイル作成

phpinfo.phpのコード
```
<?php
phpinfo();
```

動作確認
```
root@ubuntu:~# curl -I "http://shinzato-hbtask.local/phpinfo.php"
HTTP/1.1 200 OK
Date: Mon, 15 Jul 2024 10:48:49 GMT
Server: Apache/2.4.52 (Ubuntu)
Content-Type: text/html; charset=UTF-8
```

phpinfo()のhtmlファイルは記述量が多いため省略する