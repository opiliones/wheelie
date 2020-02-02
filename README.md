# dashutil

dashを少しだけ便利にするutility

## 一時ファイルを作りたくない人向けのコマンド

### tmpfifo COMMAND \[ARG\]...

bashのプロセス置換(read)に対応するコマンド。

```
$ diff $(tmpfifo echo a) $(tmpfifo echo b)
> a
< b
```
### qee COMMAND \[ARG\]...

bashのプロセス置換(write)に対応するコマンド。
moreutilのpeeの1コマンド版。
 \[ARG\]
```
$ echo a | qee grep a | tr a b
a
b
```

### pipe \[-r|-l|-a VARIABLE\] QUOTED...

パイプの復帰値のポリシーを変更する。

```
$ pipe -r 'exit 1' 'exit 2' true; echo $?
2
$ pipe -l 'exit 1' 'exit 2' ture; echo $?
1
$ pipe -a fuga 'exit 1' 'exit 2' ture; echo $?; echo $fuga
0
1 2 0
$ pipe 'exit 1' 'exit 2' true; echo $?
2
```
### either系

#### 
戻り値が0なら標準入力を、戻り値が0以外ならエラー出力を標準出力に出す。


### maybe系



## リソース管理用のコマンド

### defer COMMAND \[ARG\]...

シグナルトラップを追加する。

```
$ (touch file; defer rm file)
$ ls file
ls: cannot access 'file': No such file or directory
```

### tmpf COMMAND \[ARG\]...

一時ファイルを作って引数に渡してコマンドを実行する。

```
$ tmpf fval 'echo a > $1; cat $1'
a
```

## 疑似データ構造

### data

- data VARIABLE \[KEY VALUE\]...
- data DATA set KEY VALUE \[KEY VALUE\]...
- data DATA get KEY\[,KEY\]... COMMAND \[ARG\]...
- data DATA get-default KEY VALUE COMMAND \[ARG\]...
- data DATA exists KEY...
- data DATA values COMMAND \[ARG\]...
- data DATA keys COMMAND \[ARG\]...
- data DATA pairs COMMAND \[ARG\]...
- data DATA rm [KEY]...

```
$ data hoge \
>   apple  1
>   orange 2
$ data $hoge get orange echo
2
$ data $hoge get cake echo

$ data $hoge get-default cake 0 echo
0
$ data $hoge values echo
1
3
$ data $hoge pairs echo
apple 1
orange 2
```

## 上記のコマンドと組み合わせるためのコマンド

### fval QUOTED \[ARG\]...

QUOTEDの中では位置パラメタでARGを参照できる。

```
$ fval 'echo $(($1-$2))' 2 1
1
```

### quote \[-v VARIABLE\] STRING...

printfの%qに対応するコマンド。

```
$ quote 'a b' "' '"
'a b' \'' '\'
$ quote -v foge 'echo "|"'
$ eval $foge
|
```

### rot COMMAND \[ARG\]...

最後の引数を最初の引数に持ってくる。

```
$ rot echo 1 2 3
3 1 2
```

### rotn NUMBER COMMAND \[ARG\]...

NUMBER分末尾の引数を最初の引数に持ってくる。

```
$ rotn 2 echo 1 2 3
3 2 1
```

### cutin NUMBER COMMAND \[ARG\]...

最後の引数をNUMBERの引数に持ってくる。

```
$ cutin 2 echo 1 2 3
1 3 2
```

## その他

### ifany COMMAND \[ARG\]...

moreutilのifneと同じ。

### null COMMAND \[ARG\]...

出力をすべて捨てる。

```
$ null echo a
$ null eco a
$
```

### exists VARIABLE...

変数が存在かどうかを復帰値で返す。

```
$ exists a && echo $a
$ a=yes
$ exists a && echo $a
yes
```

