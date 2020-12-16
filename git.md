# Git のメモ

## 用語

インデックス と ステージ は同意。

## コミットメッセージの指針

少人数であれば、あまりこだわり過ぎない方が良いように思える

    1行目: コミットでの変更内容を予約  
    2行目: 空業  
    3行目以降: 変更した理由

プレフィックスを使う場合

    fix: バグ修正
    add: 新規(ファイル)機能追加
    update: 機能修正(バグ修正以外)
    remove: 削除(ファイル)


参考文献
https://qiita.com/itosho/items/9565c6ad2ffc24c09364

## 設定の表示

```sh
# 全体
git config --system --list 

# ユーザ
# ⇒実ファイル: ~/.gitconfig
git config --global --list 

# リポジトリ個別 
# ⇒リポジトリでない場所で時刻するとエラーになる 
# ⇒実ファイル: repos/.git/config 
git config --local -- list 
```

## 設定の追加

```sh
# リポジトリ個別に設定を追加
git config --local http.proxy http://192.168.0.254:8080
git config --local https.proxy http://192.168.0.254:8080
```
