MariaDB
===
MariaDBのセットアップ

インストール
---
- MariaDB 10.2 をインストールします

```
#> pkg install mariadb102-server
```

文字コードの設定
---
- 設定ファイルを作成します
- `UTF-8`を使用する様に設定します
```
#> mkdir /usr/local/etc/mysql
#> cp -p /usr/local/share/mysql/my-large.cnf /usr/local/etc/mysql/my.cnf
#> vim /usr/local/etc/mysql/my.cnf
-------------------------------------------------------------------------------------
[clinet]
 :
default-character-set = utf8

[mysqld]
 :
 :
character-set-server = utf8
-------------------------------------------------------------------------------------
```

自動起動
---
```
#> vim /etc/rc.conf
-------------------------------------------------------------------------------------
mysql_enable="YES"
-------------------------------------------------------------------------------------

#> service mysql-server start
```

root アカウントのパスワード設定
---
- `MariaDB` は rootアカウントの初期パスワードが未設定のため必ず設定します
```
#> mysql -u root
MariaDB [(none)]> update mysql.user set password=password('xxxxx') where user='root';
MariaDB [(none)]> flush privileges;
MariaDB [(none)]> exit;
#>
```

リカバリ
---
- `MariaDB` が起動しなくなった場合のリカバリ方法
- `MariaDB` が起動しない場合、`/var/db/mysql/<hostname>.err` を参照して原因を確認します
```
#> mysqld_safe --wsrep-recover --tc-heuristic-recover rollback
```
