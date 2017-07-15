# pkg site

パッケージサイトの作成

## 公開するパッケージの作成

コンピューターにインストール済みのパッケージから公開用のパッケージを作成します。

* 例とし `/tmp/pkg` 内へ作成しています。

```
#> pkg create -a -o /tmp/pkg  # 公開用パッケージを作成
#> pkg repo /tmp/pkg          # 公開用パッケージのリポジトリを作成
```

## パッケージの公開

Webサービス等でパッケージを公開します。

* Webサーバーの構築手順については割愛します。


## 公開サーバーへの参照設定

* パッケージの公開サーバーを、**参照する側** で設定します。

### FreeBSD標準設定を無効化

`/etc/pkg/FreeBSD.conf` の設定を次の様に変更します。

```
FreeBSD: {
    url: "pkg+http://pkg.FreeBSD.org/${ABI}/quarterly",
    mirror_type: "srv",
    signature_type: "fingerprints",
    fingerprints: "/usr/share/keys/pkg",
    enabled: no                           # "yes" から "no" へ変更する
}
```

### 公開サーバーの設定を追加

`/usr/local/etc/pkg/repos/` へ設定ファイルを作成します。

* ファイル名は任意です。
* 例では、`http://pkg.hoge.jp/pkg/FreeBSD:10:amd64/` でパッケージが公開されます。

```
test: {
    url: "http://pkg.hoge.jp/pkg/${ABI}",
    mirror_type: "http",
    priority: 1,
    enabled: yes
}
```
