Redmine
===
Redmineのセットアップ手順

インストール
---
- **pkg** コマンドを使用します
- データベースは **MySQL 5.6** を使用します
- **Apache 2.4 + Passenger** でホストします

```
#> pkg install apache24 redmine mysql56-server rubygem-passenger-apache2
```

セットアップ
---

### データベースの作成
- **MySQL** の初期設定は[こちら](./mysql.md)を参照してください

```
#> mysql -u root -p
mysql> create database redmine character set utf8;
mysql> create user 'redmine'@'localhost' identified by 'redmine';
mysql> grant all privileges on redmine.* to 'redmine'@'localhost';
mysql> exit;
#>
```

### データベース接続設定

```
#> vim /usr/local/www/redmine/config/database.yml
--------------------------------------------------------------
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: redmine
--------------------------------------------------------------
```

### テーブル作成とデフォルトデータの登録

```
#> cd /usr/local/www/redmine
#> sh
# rake generate_secret_token
# rake db:migrate RAILS_ENV="production"
# rake redmine:load_default_data RAILS_ENV="production"
# exit
#> 
```

### パーミッション設定

```
#> cd /usr/local/www/redmine
#> mkdir -p ./tmp/pdf ./public/plugin_assets
#> chown -R www:www ./files ./log ./tmp ./public/plugin_assets
#> chmod -R 755 ./files ./log ./tmp ./public/plugin_assets
```

### 動作確認
- Redmineを単独起動して動作確認をします

```
#> cd /usr/local/www/redmine
#> ruby bin/server webrick -e production -b 0.0.0.0
```

Apache2.4 + Passengerの設定
---

### DocumentRootの設定
- **DocumentRoot** へシンボリックリンクを作成します

```
#> ln -s /usr/local/www/redmine/public /usr/local/www/apache24/data/redmine
```

### Apacheの設定変更
- **Apache 2.4**の基本設定は`こちら`を参照してください

```
#> vim /usr/local/etc/apache24/Includes/redmine.conf
--------------------------------------------------------------
LoadModule passenger_module /usr/local/lib/ruby/gems/2.4/gems/passenger-5.2.1/buildout/apache2/mod_passenger.so

PassengerRoot /usr/local/lib/ruby/gems/2.4/gems/passenger-5.2.1
PassengerRuby /usr/local/bin/ruby

RailsBaseURI /redmine
--------------------------------------------------------------
```

