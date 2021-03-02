# Linux コマンドの備忘録

記憶できないのでメモを残す。

```sh
# HEADのアーカイブを指定ファイル名(-o)で出力する
git archive HEAD -o ../archive.zip

# archive.zip を ./exportフォルダ(-d)に 上書き(-o)で解凍する
unzip -o -d ./export archive.zip
```