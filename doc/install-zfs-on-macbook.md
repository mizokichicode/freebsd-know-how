インストール on MacBook
===

- MacBookへFreeBSDをインストールする
- Root on ZFSでインストールする
- MacBookにインストール済みのmacOSとは共存させる
- コンポーネントのインストールはFreeBSD Installerに任せる


環境
---

- macOS Catalina (macOS 11.1)
- FreeBSD 12.2 RELEASE


準備
---

### インストールメディア

- [USBメモリ用イメージ](https://download.freebsd.org/ftp/releases/ISO-IMAGES/12.2/FreeBSD-12.2-RELEASE-amd64-memstick.img)をダウンロードしてUSBメモリへ書き込む
- mscOSでUSBメモリが`/dev/disk2`として認識されていることとします

```
% sudo diskutil unmoutDisk /dev/disk2
% sudo dd if=/Users/hoge/Download/FreeBSD-12.2-RELEASE-amd64-memstick.img of=/dev/disk2
```

### FreeBSD用パーティション

- ディスクユーティリティでmacOSがインストールされているパーティションを分割します
- MacBookを再起動して復旧メニューから起動したディスクユーティリティで作業します (`command + R`を押してMacBookを起動します)
- APFS環境ではAPFSのままパーティションを分割してからHFS+へ変換します



インストール
---

- USBメモリをMacBookへ接続後、`option`を押しながらMacBookを起動します
- インストーラー(bsdinstaller)が起動したら`Shell`を選択します
- `Shell`での作業が完了したら`exit`でインストーラーへ戻ります

### 1. キーマップ設定

- Shellで作業用にキーマップを設定します (JISキーボードの場合)

```
# kbdmap
```

### 2. ZFS用カーネルモジュールの読み込みと4Kセクタ用設定

- ZFSを使用するためカーネルモジュールを手動で読み込みます
- ZFSのプールを`4kセクタ`で作成するための設定をおこないます

```
# kldload zfs
# sysctl vfs.zfs.min_auto_ashift=12
```

### 3. パーティション作成

- 下記の構成となるようにパーティションを作成します
- MacBookはUEFIブート環境のため`freebsd-boot`は使用しない 

|デバイス名|パーティションタイプ|サイズ|用途|
|:-------:|:---------------:|----:|:---|
|ada0p1|efi|200M|macOS用ブート領域 (UEFI)|
|ada0p2|apple-apfs|56G|macOS用APFS領域|
|ada0p3|efi|512M|FreeBSD用ブート領域 (UEFI)|
|ada0p4|freebsd-zfs|40G|FreeBSD用ZFS領域|
|ada0p5|freebsd-swap|16G|FreeBSD用スワップ領域|

```
# gpart add -a 4k -t efi -s 512m ada0
# gpart add -a 4k -t freebsd-boot -s 512k ada0
# gpart add -a 4k -t freebsd-zfs -s 40g ada0
# gpart add -a 4k -t freebsd-swap ada0
```

### 4. ZFSプール作成

- ルートプールを作成
- インストール環境の`/mnt`がインストール先のルート(`/`)となるようにファイルシステムを作成してマントする
- mountpoint、compression、atime、normalization の各オプションを指定する場合は、`-O` (大文字の`O`)を指定する

```
# zpool create -o altroot=/mnt -o cachefile=/tmp/zpool.cache -O mountpoint=none -O compression=lz4 -O atime=off -O normalization=formC zroot ada0p4
```

### 5. ZFSの作成

#### 5-1. デフォルトシステムのファイルシステムの作成とマウント

```
# zfs create -o mountpoint=/ -o canmount=noauto -p zroot/ROOT/default
# zfs mount zroot/ROOT/default
```

#### 5-2. 追加のファイルシステムを作成

##### ファイルシステムの作成

```
# zfs create -o mountpoint=/tmp -o exec=on -o setuid=off zroot/tmp
# zfs create -o mountpoint=/usr -o canmount=off zroot/usr
# zfs create -o mountpoint=/usr/home zroot/usr/home
# zfs create -o mountpoint=/usr/ports -o compression=gzip-9 -o setuid=off zroot/usr/ports
# zfs create -o mountpoint=/usr/src -o compression=gzip-9 zroot/usr/src
# zfs create -o mountpoint=/var zroot/var
# zfs create -o mountpoint=/var/audit -o exec=off -o setuid=off zroot/var/audit
# zfs create -o mountpoint=/var/crash -o exec=off -o setuid=off zroot/var/crash
# zfs create -o mountpoint=/var/log -o exec=off -o setuid=off zroot/var/log
# zfs create -o mountpoint=/var/mail -o atime=on zroot/var/mail
# zfs create -o mountpoint=/var/tmp -o setuid=off zroot/var/tmp
```

##### パーミッションの設定

```
# chmod 1777 /mnt/tmp
# chmod 1777 /mnt/var/tmp
```

### 6. ブートローダーのインストール

- UEFI用のブートローダーをインストールします

#### 6-1. ZFS用ブートファイルシステムの設定

- ZFS用プールへブートダイルシステムを設定します

```
# zpool set bootfs=zroot/ROOT/default zroot
```

#### 6-2. ブートローダーのインストール

- UEFI用のブートローダーをインストールします
- UEFI用ブート領域を`FAT32`でフォーマットしてブートローダーをコピーします
- ブートローダーのインストール後に必ずアンマウントする

```
# mkdir /tmp/esp
# newfs_msdos -F 32 -S 512 -c 4 ada0p3
# mount -t msdosfs /dev/ada0p3 /tmp/esp
# mkdir -p /tmp/esp/EFI/BOOT
# cp /boot/boot1.efi /tmp/esp/EFI/BOOT/BOOTX64.efi
# cd
# umount /tmp/esp
```

### 7. コンポーネントのインストール

- インストーラーへ戻ったら通常のインストール手順で`Partitioning`まで進めます
- `Partitioning`画面で`Shell`を選択します
- `Shell`に入ったら`exit`で抜けます
- `Manual Configuration`まで手順を進め、`Yes`を選択後最終設定をします

```
# vi /etc/fstab
-----------------------------------------------------------------------------
/dev/ada0p5    none    swap    sw    0    0

-----------------------------------------------------------------------------

# echo 'zfs_enable="YES"' >> /etc/rc.conf
# exit
```

### 8. 再起動

- `option`を押しながらMacBookを起動します



その他
---

- サードパーティー製のブートローダーをインストールしないため `option`を押しながらMacBookを起動する必要があります (`option`を押さない場合はmacOSが起動します)
