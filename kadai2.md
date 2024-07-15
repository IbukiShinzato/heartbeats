### OSのインストール

VirtualBoxなどでは実行に時間がかかったのと使いづらかったので、学科サーバーからVMを作成した。

```
e235718@amane:~$ ie-virsh list
uid: 19042 gid: 1001 name: e235718
 Id    Name                            State
-------------------------------------------------
 519   e235718-Ubuntu-22               running
```

VMの詳細
```
e235718@amane:~$ ie-virsh dominfo Ubuntu-22
uid: 19042 gid: 1001 name: e235718
Id:             519
Name:           e235718-Ubuntu-22
UUID:           38df6f5d-8ccc-48af-8ce3-d6fe45b8bd8f
OS Type:        hvm
State:          running
CPU(s):         2
CPU time:       620.2s
Max memory:     8388608 KiB
Used memory:    8388608 KiB
Persistent:     yes
Autostart:      disable
Managed save:   no
Security model: apparmor
Security DOI:   0
Security label: libvirt-38df6f5d-8ccc-48af-8ce3-d6fe45b8bd8f (enforcing)
```

ログイン完了

```
e235718@amane:~$ ssh e235718@10.0.6.31
e235718@10.0.6.31's password: 
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-87-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

Expanded Security Maintenance for Applications is not enabled.

27 updates can be applied immediately.
8 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

Last login: Mon Jul 15 20:00:24 2024 from 10.100.0.10
e235718@ubuntu:~$ 
```