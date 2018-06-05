MySQL
===
MySQLサーバーのセットアップ手順

インストール
---
- **pkg**コマンドを使用します
```
#> pkg install mysql57-server
```

設定
---
### 文字コード
- MySQL内で `UTF-8` を使用する様に設定します
- `/usr/local/etc/mysql/my.cnf` へ以下の記述を追加します

```
[client]
  :
default-character-set = utf8

[mysqld]
  :
  :
character-set-server = utf8
```

自動起動
---
- `/etc/rc.conf` へ以下の記述を追加します
- 設定追加後にサービスを起動します

```
#> vim /etc/rc.conf
-------------------------------------------------------------------------
mysql_enable="YES"

#> service mysql-server start
```

rootアカウントのパスワード設定
---
### MySQL 5.6 の場合
- MySQL 5.6 は、`root`の初期パスワードが未設定のため必ず設定します

```
#> mysql -u root
mysql> update mysql.user set password=password('xxxx') where user='root';
mysql> flush privileges;
mysql> exit;
```

### MySQL 5.7 の場合
- MySQL 5.7 は、`root`の初期パスワードが `/root/.mysql_secret` に保存されています

```
#> mysql -u root -p
Enter password: default_root_password
mysql> alter user 'root'@'localhost' identified by 'new_root_password';
mysql> exit;
```