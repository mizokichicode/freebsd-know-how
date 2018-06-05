update
===
**freebsd-update** を使用した更新方法です。

セキュリティアップデート
---
セキュリティアップデートは、次のように実行します。

```
#> freebsd-update fetch       # 更新プログラムの確認とダウンロード
#> freebsd-update install     # 更新プログラムのインストール
#> shutdown -r now            # 再起動
```

もし、セキュリティアップデートが失敗するような場合は、以下のように実行することでロールバックが可能です。

```
#> freebsd-update rollback
```

バージョンアップ
---
- **freebsd-update** でバージョンアップする場合は以下の手順で実行します。
- ここでは、FreeBSD 10.2 RELEASE から FreeBSD 10.3 RELEASEへのバージョンアップしています。

### セキュリティ更新
- FreeBSD 10.2 RELEASE を最新の状態に更新します。

```
#> freebsd-update fetch       # 更新プログラムの確認とダウンロード
#> freebsd-update install     # 更新プログラムのインストール
#> shutdown -r now            # 再起動
```

### アップグレード
- カーネルをアップグレードします。

```
#> freebsd-update -r 10.3-RELEASE upgrade # 10.3 の最新プログラムをダウンロード
#> freebsd-update install     # 更新プログラムのインストール
#> shutdown -r now            # 再起動
```

- カーネルのアップグレード後に、ユーザーランド (World) のアップグレードを実行します。

```
#> freebsd-update install     # 更新プログラムのインストール
#> shutdown -r now            # 再起動
```

設定ファイル
---
- **freebsd-update** の設定は、`/etc/freebsd-update.conf` に記述されています。
- 詳細は、[FreeBSDハンドブック](https://www.freebsd.org/doc/ja_JP.eucJP/books/handbook/updating-updating-freebsdupdate.html) を参照してください。

ダウンロード軽減策
---
- **freebsd-update** でダウンロードした更新プログラムは、`/var/db/freebsd-update` に保存されます。
- 複数のコンピューターで、**freebsd-update** を実行する場合、実行済みのコンピューターの`/var/db/freebsd-update`をコピーすることでダウンロードする更新プログラムの数や時間を削減できます。

