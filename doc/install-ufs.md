インストール
===
- FreeBSD 11.x RELEASE の手動インストール手順
- HDDが `ada0` として認識されているものとします
- インストールメディアを `Live CD` として起動させます

パーティションの作成
---

### 1. GPTボリュームの作成
- HDDにパーティションが作成済みの場合は、`#> gpart destroy -F ada0` でパーティションを全削除します

```
#> gpart create -s GPT ada0
```

### 2. ブートパーティションの作成
- ブートコードの書き込みまで行います

```
#> gpart add -t freebsd-boot -b 40 -s 512 ada0
#> gpart bootcode -b /boot/pmbr -p /boot/gptboot -i 1 ada0
```

### 3. SWAPパーティションの作成
- SWAPパーティションのサイズは、搭載メモリサイズの2倍程度を指定します

```
#> gpart add -t freebsd-swap -s <swap-size> ada0
```

### 4. UFSパーティションの作成
- パーティションのフォーマットまで行います

```
#> gpart add -t freebsd-ufs ada0
#> newfs -U ada0p3
```

システムのインストール
---
- 作成したパーティションをマウント後、インストールメディアにあるシステムアーカイブをHDDへ展開します

```
#> mount /dev/ada0p3 /mnt
#> cd /usr/freebsd-dist
#> tar zxvf base.txz -C /mnt
#> tar zxvf kernel.txz -C /mnt
#> tar zxvf lib32.txz -C /mnt
```

必要に応じて以下のパッケージも展開します

```
#> tar zxvf doc.txz -C /mnt
#> tar zxvf games.txz -C /mnt
#> tar zxvf ports.txz -C /mnt
#> tar zxvf src.txz -C /mnt
```

自動マウント設定
---
- `/etc/fstab` を以下の様に作成します

```
#> echo '/dev/ada0p3    /   ufs rw  1   1' >> /mnt/etc/fstab
#> echo '/dev/ada0p2    none    swap    sw  0   0' >> /mnt/etc/fstab
```

サービスの自動起動設定
---
- 必要最低限の設定を追加します
- NICの設定は、実際の環境に合わせて指定します

```
#> echo 'dumpdev="NO"' >> /mnt/etc/rc.conf
#> echo 'keymap="jp"' >> /mnt/etc/rc.conf               ← FreeBSD 11.x では "jp" のみ指定する
#> echo 'hostname="freebsd"' >> /mnt/etc/rc.conf
#> echo 'ifconfig_xxx="inet xxx.xxx.xxx.xxx netmask xxx.xxx.xxx.xxx"' >> /mnt/etc/rc.conf
#> echo 'sendmail_enable="NONE"' >> /mnt/etc/rc.conf
```

ドメイン・DNS設定
---
- 実際のネットワーク環境に合わせて、ドメイン名とDNSサーバーアドレスを指定します

```
#> echo 'search xxxxxxx' >> /mnt/etc/resolv.conf
#> echo 'nameserver xxx.xxx.xxx.xxx' >> /mnt/etc/resolv.conf
```

