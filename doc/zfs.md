ZFS
===
ZFSの操作方法

ZFSを使用する前準備
---
- `/boot/loader.conf` と `/etc/rc.conf` へ設定を追加します
- 設定追加後、リブートします

```
#> vim /boot/loader.conf
--------------------------------------------------------------
zfs_load="YES"
kern.geom.label.gptid.enable="0"
--------------------------------------------------------------

#> vim /etc/rc.conf
--------------------------------------------------------------
zfs_enable="YES"
--------------------------------------------------------------

#> shutdown -r now
```

ZFS用プールの作成
---
- HDD/SSDの全エリアをZFSとして使用する場合
- HDD/SSD が `ada1` とします

```
#> zpool create -m none zdpool ada1
```

ZFSの作成
---
```
#> zfs create -o mountpoint=/usr/home zdpool/home
```

作成済みディレクトリをZFSへ移行する手順
---
- ここでは **/var** を例に説明します

```
#> zfs create -o mountpoint=/var.bak zdpool/var.bak
#> shutdown now
# rsync -av /var/ /var.bak/
# chflags noschg /var/empty
# rm -rfv /var
# zfs rename zdpool/var.bak zdpool/var
# zfs set mountpoint=/var zdpool/var
# rm -rfv /var.bak
# shutdown -r now
```