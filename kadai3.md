### サーバのタイムゾーンをJSTにする

現在のタイムゾーンは以下のようになっています

```
root@ubuntu:/home/ubuntu# timedatectl
               Local time: Thu 2024-07-11 12:27:42 UTC
           Universal time: Thu 2024-07-11 12:27:42 UTC
                 RTC time: Thu 2024-07-11 12:27:43
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

ここではTime zoneはUTC（協定世界時）が利用されています

これをJSTに変更します

```
root@ubuntu:/home/ubuntu# timedatectl set-timezone Asia/Tokyo

root@ubuntu:/home/ubuntu# timedatectl
               Local time: Thu 2024-07-11 21:28:20 JST
           Universal time: Thu 2024-07-11 12:28:20 UTC
                 RTC time: Thu 2024-07-11 12:28:21
                Time zone: Asia/Tokyo (JST, +0900)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

このようにTime zoneがJSTに変更されています

### 下記NTPサーバと時刻同期を行う
    - ntp1.jst.mfeed.ad.jp
    - ntp2.jst.mfeed.ad.jp
    - ntp3.jst.mfeed.ad.jp

```
root@ubuntu:/home/e235718$ man chronyd
No manual entry for chronyd
root@ubuntu:/home/e235718$ man chronyc
No manual entry for chronyc
```

chronyd,chronycのmanualを見ることができなかったので、chronyパッケージをインストールしました。

```
root@ubuntu:~$ apt-get install chrony
```

インストール後、chronyd,chronycのmanualを見ることができました。
```
root@ubuntu:~$ man chronyd

CHRONYD(8)                                                                                 System Administration                                                                                CHRONYD(8)

NAME
       chronyd - chrony daemon

SYNOPSIS
       chronyd [OPTION]... [DIRECTIVE]...

DESCRIPTION
       chronyd is a daemon for synchronisation of the system clock. It can synchronise the clock with NTP servers, reference clocks (e.g. a GPS receiver), and manual input using wristwatch and keyboard
       via chronyc. It can also operate as an NTPv4 (RFC 5905) server and peer to provide a time service to other computers in the network.

       If no configuration directives are specified on the command line, chronyd will read them from a configuration file. The compiled-in default location of the file is /etc/chrony/chrony.conf.

       Information messages and warnings will be logged to syslog.
```

NTP(Network Time Protocol)はネットワーク上のコンピュータシステム間で時刻同期させるための通信プロトコルである。
そしてChronyはNTPクライアントとNTPサーバーの実装の一つである。

変更前

```
root@ubuntu:/etc/chrony$ chronyc sources

210 Number of sources = 8
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^- prod-ntp-5.ntp1.ps5.cano>     2   8   377   173    +13ms[  +13ms] +/-  132ms
^- prod-ntp-3.ntp4.ps5.cano>     2   8   377   232    +12ms[  +12ms] +/-  130ms
^- prod-ntp-4.ntp1.ps5.cano>     2   7   377   108    +13ms[  +13ms] +/-  129ms
^- prod-ntp-4.ntp4.ps5.cano>     2   8   377    39    +10ms[  +10ms] +/-  137ms
^+ 108.160.132.224.vultruse>     2   8   377   238   +275us[ +333us] +/-   40ms
^* sv1.localdomain1.com          2   8   377   177  -1112us[-1054us] +/-   46ms
^+ mail.moe.cat                  2   8   377   175   -967us[ -967us] +/-   41ms
^+ 122x215x240x52.ap122.ftt>     2   8   377   177  +2563us[+2563us] +/-   40ms
```

/etc/chrony/chrony.confに以下を記述しました。

```
server ntp1.jst.mfeed.ad.jp iburst
server ntp2.jst.mfeed.ad.jp iburst
server ntp3.jst.mfeed.ad.jp iburst
```

再起動
```
root@ubuntu:/etc/chrony$ systemctl restart chronyd
```

変更後
```
root@ubuntu:/etc/chrony$ chronyc sources

210 Number of sources = 3
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ ntp1.jst.mfeed.ad.jp          2   6     7     0  +4397ns[-1412us] +/-   75ms
^* ntp2.jst.mfeed.ad.jp          2   6     7     1    -40us[-1456us] +/-   61ms
^+ ntp3.jst.mfeed.ad.jp          2   6     7     0    +60us[-1357us] +/-   89ms

```

^*が主要サーバーであるので、ntp2.jst.mfeed.ad.jpが主要サーバーで、それ以外が候補のサーバーである。

### サーバのファイアウォールを有効(起動)にしてください
ファイアウォールとはあらかじめ設定したルールに従って、通してはいけないデータを止める機能のことである。

```
root@ubuntu:~$ ufw status
Status: inactive
```

以上よりファイアウォールがinactive(非アクティブ)の状態であることがわかる

以下のコマンドでファイアウォールをアクティブにする
```
root@ubuntu:~$ ufw enable
```

再確認

```
root@ubuntu:~$ ufw status
Status: active
```

### SELinuxを無効にしてください
SELinux(Security-Enhanced Linux)とはシステムにアクセスできるユーザーを管理者がより詳細に制御できるようにするLinuxシステム用のセキュリティ・アーキテクチャである。
有効にするとroot権限が乗っ取られた時の被害を最小限に抑えることが可能である。

selinux-utilsのインストール
```
root@ubuntu:/etc/selinux$ apt install selinux-utils
```

SELinux の有効化状態
```
root@ubuntu:/etc/selinux$ getenforce
Disabled
```

現在はDisabled(無効)であるのでそのままで良い。

