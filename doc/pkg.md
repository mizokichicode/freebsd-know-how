pkg
===
**pkg** コマンドによるパッケージの管理方法です。

パッケージ情報の更新
---
```
#> pkg update
```

パッケージの検索
---
```
#> pkg search vim
```

パッケージのインストール
---
```
#> pkg install vim
```

パッケージのアンインストール
---
```
#> pkg delete vim
```

インストール済みパッケージ情報の表示
---
```
#> pkg info
```

アップデート可能なパッケージの表示
---
```
#> pkg version -l '<'
```

パッケージのアップグレード
---
```
#> pkg upgrade
```

キャッシュされたパッケージの削除
---
```
#> pkg clean
```

未使用パッケージの削除
---
```
#> pkg autoremove
```