GitBucket 外部DBへの変更
===
GitBucket で外部データベースを使用する手順を説明します。

- 外部データベースとして`MySQL`を使用します。
- `Tomcat 8`がインストールされている環境を想定しています。

MySQL のセットアップ
---
- MySQLの初期セットアップは[こちら](./mysql.md)を参照してください

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
