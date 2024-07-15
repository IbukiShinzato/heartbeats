### Apacheのバージョンは2.4系をインストールしてください

Apacheとは無償で利用できるオープンソースのWebサーバーソフトウェアのことである。
WebサーバーのソフトにはIISやnginxなどもある。

Apacheのインストール

```
root@ubuntu:~# apt-get install apache2
```

Apacheのバージョン確認

```
root@ubuntu:/etc/apache2# apache2 -v
Server version: Apache/2.4.52 (Ubuntu)
Server built:   2024-07-11T12:20:46
```

### Apacheは自動起動するように設定してください

Apacheが自動起動しているかの確認

```
root@ubuntu:~# systemctl list-unit-files --type=service | grep apache2.service
apache2.service                         disabled        enabled
```

disabledになっているので、これをenabledに変更する。

```
systemctl enable apache2.service
```

再確認
```
root@ubuntu:~# systemctl list-unit-files --type=service | grep apache2.service
apache2.service                         enabled         enabled
```

enabledになっているので自動起動の設定完了。

### VirtualHostの機能を利用し、下記の要件を満たしてください
    - Webブラウザで`http://<自分の名字>-hbtask.local/` にアクセスすると「こんにちは。ありがとう。」と表示される
        - 例: `http://kuramochi-hbtask.local`
    - Webブラウザで`http://<自分の名前>-hbtask.local/` にアクセスすると「ありがとう。こんにちは。」と表示される
        - 例: `http://wataru-hbtask.local/`

/var/www/ディレクトリに移動
```
root@ubuntu:~# cd /var/www
```

ここにドメイン名が含まれるディレクトリ名を作成し、その中にpublic_htmlディレクトリをそれぞれ作成。
```
root@ubuntu:/var/www# mkdir shinzato-hbtask.local && mkdir shinzato-hbtask.local/public_html 
root@ubuntu:/var/www# mkdir ibuki-hbtask.local && mkdir ibuki-hbtask.local/public_html 
```

以上のpublic_htmlの中にそれぞれのhtmlファイルを作成
```
root@ubuntu:/var/www# touch shinzato-hbtask.local/public_html/index.html && touch ibuki-hbtask.local/public_html/index.html 
```

index.htmlの中身は以下である。

shinzato-hbtask.local/public_html/index.html 
```
<html>
<body>
こんにちは。ありがとう。
</body>
</html>
```

ibuki-hbtask.local/public_html/index.html
```
<html>
<body>
ありがとう。こんにちは。
</body>
</html>
```

以上のファイルがWeb上に表示される。

所有権の変更
```
root@ubuntu:/var/www# chown -R e235718: /var/www/shinzato-hbtask.local/

root@ubuntu:/var/www# chown -R e235718: /var/www/ibuki-hbtask.local/
```

所有権の確認
```
root@ubuntu:/var/www# ls -l
total 12
drwxr-xr-x 2 root    root    4096 Jul 14 19:13 html
drwxr-xr-x 3 e235718 e235718 4096 Jul 15 16:49 ibuki-hbtask.local
drwxr-xr-x 3 e235718 e235718 4096 Jul 15 16:47 shinzato-hbtask.local
```

/etc/apache2/sites-available/ディレクトリに移動
```
root@ubuntu:/var/www# cd /etc/apache2/sites-available/
```

それぞれの設定ファイルを作成
```
touch shizato-hbtask.local.conf && touch ibuki-hbtask.local.conf
```

confファイルの中身は以下である。

shinzato-hbtask.local.conf 
```
<VirtualHost *:80>
    Servername shinzato-hbtask.local
    DocumentRoot /var/www/shinzato-hbtask.local/public_html

    ErrorLog ${APACHE_LOG_DIR}/shinzato-hbtask.local.error.log
    CustomLog ${APACHE_LOG_DIR}/shinzato-hbtask.local.access.log combined
</VirtualHost>
```

ibuki-hbtask.local.conf
```
<VirtualHost *:80>
    Servername ibuki-hbtask.local
    DocumentRoot /var/www/ibuki-hbtask.local/public_html

    ErrorLog ${APACHE_LOG_DIR}/ibuki-hbtask.local.error.log
    CustomLog ${APACHE_LOG_DIR}/ibuki-hbtask.local.access.log combined
</VirtualHost>
```


VirtualHost *:80 はポート80に対する全てのリクエストに適用される

Servername はメインドメイン名を設定

DocumentRoot はこのバーチャルホストのルートディレクトリを指定

ErrorLog はエラーログのパス

CustomLog はアクセスログのパス　combinedはログフォーマット


バーシャルホスト設定ファイルを登録
```
root@ubuntu:/etc/apache2/sites-available# a2ensite shinzato-hbtask.local
Enabling site shinzato-hbtask.local.
To activate the new configuration, you need to run:
  systemctl reload apache2

root@ubuntu:/etc/apache2/sites-available# a2ensite ibuki-hbtask.local
Enabling site ibuki-hbtask.local.
To activate the new configuration, you need to run:
  systemctl reload apache2
```

設定ファイルの構文をチェック
```
root@ubuntu:/etc/apache2/sites-available# sudo apachectl configtest
Syntax OK
```

再起動
```
root@ubuntu:/etc/apache2/sites-availabl$ systemctl restart apache2
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to restart 'apache2.service'.
Authenticating as: e235718
Password: 
==== AUTHENTICATION COMPLETE ===
```

hostsファイルの検索
```
root@ubuntu:~# find / -type f -name hosts
/etc/hosts
```

hostsファイルの中身
```
127.0.0.1       localhost
127.0.1.1       ubuntu.st.ie.u-ryukyu.ac.jp     ubuntu

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

hostsファイルの変更
```
127.0.0.1       localhost
127.0.1.1       ubuntu.st.ie.u-ryukyu.ac.jp     ubuntu

127.0.0.1       shinzato-hbtask.local
127.0.0.1       ibuki-hbtask.local

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

動作確認
```
e235718@ubuntu:/$ curl -I "http://shinzato-hbtask.local"
HTTP/1.1 200 OK
Date: Mon, 15 Jul 2024 08:58:46 GMT
Server: Apache/2.4.52 (Ubuntu)
Last-Modified: Sun, 14 Jul 2024 11:31:39 GMT
ETag: "44-61d3374b8e791"
Accept-Ranges: bytes
Content-Length: 68
Content-Type: text/html

e235718@ubuntu:/$ curl -I "http://ibuki-hbtask.local"
HTTP/1.1 200 OK
Date: Mon, 15 Jul 2024 08:58:55 GMT
Server: Apache/2.4.52 (Ubuntu)
Last-Modified: Mon, 15 Jul 2024 07:50:20 GMT
ETag: "44-61d447b1cdbd8"
Accept-Ranges: bytes
Content-Length: 68
Content-Type: text/html

e235718@ubuntu:/$ curl "http://shinzato-hbtask.local"
<html>
<body>
こんにちは。ありがとう。
</body>
</html>

e235718@ubuntu:/$ curl "http://ibuki-hbtask.local"
<html>
<body>
ありがとう。こんにちは。
</body>
</html>
```


