### Webブラウザで`http://<自分の名字>-hbtask.local/blog/`にアクセスすると、WordPressのブログサイトが表示される

latest-ja.tar.gzファイルをダウンロード
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# wget https://ja.wordpress.org/latest-ja.tar.gz
```

latest-ja.tar.gzを解凍
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# tar xvf latest-ja.tar.gz
```

.tar.gzとあるのでtarコマンドで解凍


解凍されたディレクトリはwordpressというディレクトリで保存されている
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# ls
index.html  latest-ja.tar.gz  mysql.php  phpinfo.php  wordpress
```

wordpressからblogに名前を変更
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# mv wordpress/ blog
```

所有権の変更
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# chown -R www-data:www-data blog
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# ll
total 25268
drwxr-xr-x 3 e235718  e235718      4096 Jul 16 18:23 ./
drwxr-xr-x 3 e235718  e235718      4096 Jul 15 16:47 ../
drwxr-xr-x 5 www-data www-data     4096 Jun 25 05:15 blog/
-rw-r--r-- 1 e235718  e235718        68 Jul 14 20:31 index.html
-rw-r--r-- 1 root     root     25846701 Jun 25 05:16 latest-ja.tar.gz
-rw-r--r-- 1 root     root          516 Jul 16 17:44 mysql.php
-rw-r--r-- 1 root     root           17 Jul 15 19:45 phpinfo.php
```

動作確認
```
root@ubuntu:/var/www/shinzato-hbtask.local/public_html# curl -I "http://shinzato-hbtask.local/blog"
HTTP/1.1 301 Moved Permanently
Date: Tue, 16 Jul 2024 09:31:11 GMT
Server: Apache/2.4.52 (Ubuntu)
Location: http://shinzato-hbtask.local/blog/
Content-Type: text/html; charset=iso-8859-1
```