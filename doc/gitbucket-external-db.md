GitBucket 外部DBへの変更
===
GitBucket で外部データベースを使用する手順を説明します。

- 外部データベースとして`MySQL`を使用します。
- `Tomcat 8`がインストールされている環境を想定しています。

MySQL のセットアップ
---
### インストール

- **pkg** コマンドでインストールします。
- GitBucket では、MySQL 5.7 以上が必要です。

```
#> pkg install mysql57-server
 :
 :
#> pkg install mysql-connector-java
 :
 :
#> cp /usr/local/share/java/classes/mysql-connector-java.jar /usr/local/apache-tomcat-8.0/lib/
```

### 文字コード設定
- MySQL 内で、`UTF-8`を使用するように設定します。

`/usr/local/etc/mysql/my.cnf`へ、以下の記述を追加します。

```
[client]
 :
default-character-set = utf8

[mysqld]
 :
 :
character-set-server = utf8
```

### 起動設定

`/etc/rc.conf` へ、以下の記述を追加します。

```
mysql_enable="YES"
```

設定を追記後に、サービスを起動します。

`#> service mysql-server start`

### ログイン
- `root` アカウントの初期パスワードは、`/root/.mysql_secret` に記載されています。
- `root` アカウントのパスワード変更は割愛します。

```
#> mysql -u root -p
Enter password:
```

### データベース作成

```
root@localhost [(none)]> create database gitbucket;
```

### ユーザー作成

```
root@localhost [(none)]> create user 'gitbucket'@'localhost' identified by 'gitbucket';
root@localhost [(none)]> grant all privileges on gitbucket.* to 'gitbucket'@'localhost';
root@localhost [(none)]> flush privileges;
```
