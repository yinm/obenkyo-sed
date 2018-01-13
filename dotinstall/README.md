[sed入門 (全10回) - プログラミングならドットインストール](https://dotinstall.com/lessons/basic_sed)をやってます。

## ch02

### `-e` 
* sedのコマンド実行
* expressionの略

```sh
$ cat names.txt
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda
$ sed -e '3d' names.txt
1 taguchi
2 fkoji
4 takahashi
5 yasuda
```

* コマンドが1個だけなら省略可能 (e.g. `sed '3d' names.txt`)

### `-i` 
* ファイルの上書きをする
* backupもとりたい場合は、`-i` + `拡張子`にする

```sh
$ sed -i.bak '3d' names.txt
$ cat names.txt
1 taguchi
2 fkoji
4 takahashi
5 yasuda
$ ls
names.txt     names.txt.bak
$ cat names.txt.bak
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda
```

### `-f`
* ファイルに書いたsedのコマンド実行

```sh
$ cat ex1.sed
3d
$ sed -f ex1.sed names.txt
1 taguchi
2 fkoji
4 takahashi
5 yasuda
```


