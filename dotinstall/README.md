* [sed入門 (全10回) - プログラミングならドットインストール](https://dotinstall.com/lessons/basic_sed)をやってます。
* 方言がつらかったので、[Macでsedするならgnu-sedを入れておくべき | メモ帳代わりのブログ](http://www.absolute-keitarou.net/blog/?p=586)を参考に、gnu-sedを入れた

## #02 はじめてのsed

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

## #03 パターンスペースについて理解しよう

### sedの `-e expression` は、2つのもので構成されている
* address: commandを実行する行
* command: addressでマッチした行に対する処理
* e.g. `sed -e '3d' names.txt` の場合
  * address: `3`
  * command: `d`(delete)

### パターンスペース
* sedの処理を実行する場所(バッファのようなもの)

### sedの処理順序 (以下の内容を1行ずつ繰り返す)
1. ファイルから1行読み込んで、パターンスペースに格納
2. addressにマッチ？ -> command実行！
3. パターンスペースを出力 (標準出力などに出力する)


## #04 アドレスを使いこなそう

### `!`
* 指定したaddress以外を処理する

```sh
bash-3.2$ sed '3!d' names.txt
3 dotinstall
```

### `;`
* 複数のaddress + commandを指定

```sh
bash-3.2$ sed '1d;3d' names.txt
2 fkoji
4 takahashi
5 yasuda

```

### `,`
* 範囲を指定する

```sh
bash-3.2$ sed '1,3d' names.txt
4 takahashi
5 yasuda

```

### `~`
* 最初の指定した行目から、次に指定した行飛ばしで処理する 
* Macだと使えないっぽい

```sh
bash-3.2$ sed '1~2d' names.txt
sed: 1: "1~2d": invalid command code ~
```


### `$`
* 最終行

```sh
bash-3.2$ sed '$d' names.txt
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda
```

* `,`と合わせて、指定行から最後まで処理

```sh
bash-3.2$ sed '3,$d' names.txt
1 taguchi
2 fkoji
```

### `/regexp/`
* 正規表現

```sh
bash-3.2$ sed '/i$/d' names.txt
3 dotinstall
5 yasuda

```

### `アドレスなし`
* 全ての行を処理する

```sh
bash-3.2$ sed 'd' names.txt
```

## #05 p/a/iコマンドを使ってみよう

### `p`
* addressにマッチした行を出力する
* printの略

```sh
bash-3.2$ sed '3p' names.txt
1 taguchi
2 fkoji
3 dotinstall
3 dotinstall
4 takahashi
5 yasuda

```

* `-n`と組み合わせて、パターンスペースの動作を抑えて使うことが多い (マッチした以外の行を表示するのを防ぐため)

```sh
bash-3.2$ sed -n '3p' names.txt
3 dotinstall
```

### `q`
* addressにマッチした時点で処理を終了する
* quitの略

```sh
bash-3.2$ sed '3q' names.txt
1 taguchi
2 fkoji
3 dotinstall
```

### `i`
* addressにマッチした行の前に、行を追加する
* 追加する行の文字列は、`\(バックスラッシュ)`の後に続ける
* insertの略 

```sh
bash-3.2$ sed '1i\--- start ---' names.txt
--- start ---
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda

```

### `a`
* addressにマッチした行の後に、行を追加する
* 追加する行の文字列は、`\(バックスラッシュ)`の後に続ける (`i`と同じ)
* appendの略 

```sh
bash-3.2$ sed -e '1i\--- start ---' -e '$a\--- end ---' names.txt
--- start ---
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda

--- end ---
```

* 最終行の空行も取り除いたバージョン (`/^$/d`の正規表現で、空行を除く)

```sh
bash-3.2$ sed -e '1i\--- start ---' -e '$a\--- end ---' -e '/^$/d' names.txt
--- start ---
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda
--- end ---
```

### `/regexp/=`
* addressにマッチした行数を表示する

```sh
bash-3.2$ cat names_some_dotinstall.txt
1 taguchi
2 fkoji
3 dotinstall
4 takahashi
5 yasuda
6 dotinstall
7 dotinstall

bash-3.2$ sed -n '/dotinstall/=' names_some_dotinstall.txt
3
6
7
```



