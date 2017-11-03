# vim-go チュートリアル

これは vim-go のチュートリアルです。 vim-go のインストール方法と使い方についての簡単なチュートリアルとなっています。

(訳注: このチュートリアルは [fatih/vim-go-tutorial: Tutorial for vim-go](https://github.com/fatih/vim-go-tutorial) を翻訳したものです。修正・改善がある場合はぜひプルリクエストを送ってください！)

# 目次

1. [クイックセットアップ](#クイックセットアップ)
2. [Hello World](#hello-world)
3. [実行する](#実行する)
4. [ビルドする](#ビルドする)
5. [修正する](#修正する)
6. [テストする](#テストする)
7. [カバレッジを見る](#カバレッジを見る)
8. [編集する](#編集する)
  * [import文](#import文)
  * [テキストオブジェクト](#テキストオブジェクト)
  * [構造体リテラルの行分割と結合](#構造体リテラルの行分割と結合)
  * [スニペット](#スニペット)
9. [美しく表示する](#美しく表示する)
10. [チェックする](#チェックする)
11. [ナビゲートする](#ナビゲートする)
  * [代替ファイル](#代替ファイル)
  * [定義へジャンプ](#定義へジャンプ)
  * [関数の間を移動する](#関数の間を移動する)
12. [理解する](#理解する)
  * [ドキュメント検索](#ドキュメント検索)
  * [識別子の解決](#識別子の解決)
  * [識別子のハイライト](#識別子のハイライト)
  * [依存パッケージとファイル](#依存パッケージとファイル)
  * [Guru](#guru)
13. [リファクタリングする](#リファクタリングする)
  * [識別子をリネームする](#識別子をリネームする)
  * [関数を抽出する](#関数を抽出する)
14. [コード生成する](#コード生成する)
  * [インターフェースを実装するメソッドスタブ](#インターフェースを実装するメソッドスタブ)
15. [シェアする](#シェアする)

# クイックセットアップ

vim-go をインストールするのに私たちは `vim-plug` を使います。
他のプラグインマネージャを使っても構いません。
私たちは最小限の `~/.vimrc` を作って必要に応じて設定を追加していきます。

まず `vim-go` と共に `vim-plug` を取得してインストールしてください。

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
git clone https://github.com/fatih/vim-go.git ~/.vim/plugged/vim-go
```

以下の内容で `~/.vimrc` を作成してください。

```vim
call plug#begin()
Plug 'fatih/vim-go', { 'do': ':GoInstallBinaries' }
call plug#end()
```

あるいは Vim を起動して `:GoInstallBinaries` を実行してください。これは
`vim-go` に必要なツールを全てインストールする `vim-go` のコマンドです。 
コンパイル済みのバイナリをダウンロードするのではなく、裏で `go get` を呼ぶので
バイナリは全てあなたのホストマシンでコンパイルされます (これは安全であり且つ
複数のプラットフォームのバイナリを提供する必要がないのでインストール手順を
単純化します)。 (`guru` や `goimports` などの) 必要なツールの一部を既に
インストール済みの場合は `:GoUpdateBinaries` を実行してバイナリをアップデート
してください。

このチュートリアルのすべてのサンプルは
`GOPATH/src/github.com/fatih/vim-go-tutorial/`
フォルダで実行されます。
カレントフォルダがこのフォルダになっていることを確認してください。
そうすることでチュートリアルを進めることが簡単になります。
既に `GOPATH` を設定済みであれば、単に以下のように実行してください。

```
go get github.com/fatih/vim-go-tutorial
```

あるいは必要ならフォルダを作成してください。


# Hello World!

ターミナルで `main.go` を開いてください。

```
vim main.go
```

これは標準出力に `vim-go` と出力する非常に基本的なファイルになっています。

# 実行する

`:GoRun` でファイルを簡単に実行できます。裏ではカレントファイルに対して
`go run` が実行されます。実行すると `vim-go` と出力されるはずです。

# ビルドする

`vim-go` を `Hello Gophercon` に置き換えてください。実行する代わりにコンパイル
してみましょう。このために `:GoBuild` があります。実行すると以下のメッセージが
表示されるはずです。

```
vim-go: [build] SUCCESS
```

裏では `go build` が実行されますが、それよりは少し賢いです。以下の点が異なります。

* バイナリは作られません。 `:GoBuild` を複数回実行しても作業領域を汚染
  することはありません。
* ソースのパッケージディレクトリに自動的に `cd` します。
* ビルドエラーがある場合はパースしてクイックフィクスリストに表示します。
* 自動的に GOPATH を検知して必要に応じて修正します (`gb` や `Godeps` などの
  プロジェクトを検知します)。
* NeoVimまたはVim8.0以降のバージョンで実行された場合は非同期に実行します。

# 修正する

2つのコンパイルエラーを追加して2つのビルドエラーが起きるようにしてみましょう。

```go
var b = foo()

func main() {
	fmt.Println("Hello GopherCon")
	a
}
```

ファイルを保存して再度 `:GoBuild` を実行してください。

今回はクイックフィクスビューが開きます。エラー間をジャンプするのに
`:cnext` と `:cprevious` が使えます。最初のエラーを修正しファイルを保存して
再度 `:GoBuild` を実行してみましょう。クイックフィクスリストはエラー1つに
更新されるでしょう。2つめのエラーも消しファイルを保存して再度 `:GoBulid` を
実行してください。今度はもうエラーがないので vim-go はクイックフィクスウィンドウを
自動的に閉じてくれます。

もう少し改善してみましょう。 Vim には `autowrite` という設定があり
`:make` を実行したらファイルの内容を自動的に保存できます。 vim-go も
この設定を活用できます。 `.vimrc` を開いて以下の内容を追加してください。

```
set autowrite
```

(訳注: 原文では省略されていますが `~/.vimrc` に設定を追加した場合は、
vimを再起動するか `:source ~/.vimrc` として設定を反映する必要があります。
以下で `~/.vimrc` に設定を追加する場合も同様です)

これでもう `:GoBuild` を実行するときにファイルを保存する必要はありません。
再度2つのエラーを追加して `:GoBuild` を実行するには `:GoBuild` だけを
実行すれば良いのでより迅速に繰り返すことができます。

`:GoBuild` を実行すると最初のエラーにジャンプします。ジャンプしたくない場合は
最後に `!` (感嘆符)を付けて `:GoBuild!` と実行してください。

`:GoRun` 、 `:GoInstall` 、 `:GoTest` など全ての `go` コマンドで、エラーが
あるときはクィックフィクスウィンドウが必ず開きます。

### vimrc の改善

* 以下のショートカットを追加すればクィックフィクスリスト内のエラー間でジャンプ
するのがより簡単になります。

```vi
map <C-n> :cnext<CR>
map <C-m> :cprevious<CR>
nnoremap <leader>a :cclose<CR>
```

* 私はさらに以下のショートカットを使ってGoのプログラムを `<leader>b` と
`<leader>r` でビルド、実行しています。

```vim
autocmd FileType go nmap <leader>b  <Plug>(go-build)
autocmd FileType go nmap <leader>r  <Plug>(go-run)
```

* Vimには2種類のエラーリストがあります。1つは `ロケーションリスト` でもう1つは
`クイックフィクス` です。残念ながらそれぞれのリストのコマンドは異なっています。
ですので `:cnext` は `クイックフィクス` にしか効かず、 `ロケーションリスト` には
`:lnext` を使わなければなりません。 `vim-go` の一部のコマンドはロケーションリスト
を開きますが、ロケーションリストはウィンドウに紐づけられているので、それぞれの
ウィンドウが別のリストを持つことができます。これは複数のウィンドウと複数の
ロケーションリストを持ち、1つは `Build` 用、1つは `Check` 用、1つは `Test` 用
などと使い分けることができます。

しかし `クイックフィクス` だけを使いたいユーザもいます。以下の設定を `vimrc` に
追加すれば、すべてのリストは `クイックフィクス` になります。

```vim
let g:go_list_type = "quickfix"
```

# テストする

簡単な関数とその関数のテストを書いてみましょう。以下を追加してください。


```go
func Bar() string {
	return "bar"
}
```

`main_test.go` という新規ファイルを開いてください (どのように開いても
構いません。起動済みのVimからでも良いですし、別のVimセッションからでも良いです。
ご自由にどうぞ)。 ここでは `:edit main_test.go` を実行しカレントバッファを使って
Vimから開きましょう。

新規のファイルを開くと何かに気づくでしょう。ファイルには自動的にパッケージ宣言が
追加されています。

```go
package main
```

これは vim-go によって自動的に追加されています。ファイルが有効なパッケージ内に
あることを検出し、パッケージ名に基づいたファイルを作ったのです (今回のケースでは
パッケージ名は `main` でした)。 もしファイルが存在しなかった場合は、 vim-go は
自動的にシンプルな main パッケージの内容を生成します。

テストファイルを以下のコードに更新してください。

```go
package main

import (
	"testing"
)

func TestBar(t *testing.T) {
	result := Bar()
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}
}
```

`:GoTest` を実行してください。以下のメッセージが表示されます。

```
vim-go: [test] PASS
```

`:GoTest` は裏で `go test` を実行します。 `:GoBuild` のときと同じ改善点があります。
テストでエラーがある場合、クイックフィクスリストが再び開き簡単にジャンプできます。

別のちょっとした改善はテストファイルそのものを開く必要がないということです。
試してみましょう。 `main.go` を開いて `:GoTest` を実行してください。これでも
テストが実行されることがわかるでしょう。

`:GoTest` はデフォルトでは10秒でタイムアウトします。Vimはデフォルトでは非同期では
ないのでこの設定は有用です。 `let g:go_test_timeout = '10s'` のように書くことで
タイムアウトの値を変えることができます。

テストファイルを簡単に扱うためにさらに2つのコマンドがあります。最初の1つは
`:GoTestFunc` です。これはカーソルの下にある関数だけをテストします。
テストファイル (`main_test.go`) の内容を以下のように変えましょう。

```go
package main

import (
	"testing"
)

func TestFoo(t *testing.T) {
	t.Error("intentional error 1")
}

func TestBar(t *testing.T) {
	result := Bar()
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}
}

func TestQuz(t *testing.T) {
	t.Error("intentional error 2")
}
```

この状態で `:GoTest` を実行するとクイックフィクスウィンドウが開いて2つのエラーが
表示されます。しかし `TestBar` 関数の内側に移動して `:GoTestFunc` を実行すると
テストはパスします！ 時間がかかるテストが多数あってそのうちの一部のテストだけを
実行したい場合はこれはとても便利です。

もう1つのテストに関連したコマンドは `:GoTestCompile` です。テストは成功する
必要があるだけではなく、何の問題もなくコンパイルが通らなければなりません。
`:GoTestCompile` はテストファイルをコンパイルし、 `:GoBuild` と全く同じように
エラーがあればクイックフィクスを開きます。しかしこの場合はテストは **実行しません** 。
これは頻繁に編集する多くのテストがある場合に非常に便利です。カレントのテストファイル
の中で `:GoTestCompile` を実行すれば、以下のような出力が見られるはずです。

```
vim-go: [test] SUCCESS 
```

### vimrc の改善

* `:GoBuild` のときと同様にキーのコンビネーションで簡単に `:GoTest` を実行する
ようなマッピングを追加できます。以下の設定を `.vimrc` に追加してください。

```vim
autocmd FileType go nmap <leader>t  <Plug>(go-test)
```

これで `<leader>t` で簡単にファイルをテストできます。

* GoファイルのBuildをさらに簡単にしましょう。最初に、先ほど追加した以下のマッピ
  ングを削除してください。

```vim
autocmd FileType go nmap <leader>b  <Plug>(go-build)
```

改善したマッピングをさらに追加していきます。どんなGoファイルに対しても
シームレスにするために、Goのファイルの種別をチェックして `:GoBuild` または
`:GoTestCompile` を実行するような簡単な Vim の関数を作ることができます。
以下のヘルパー関数を `.vimrc` に追加してください。

```vim
" run :GoBuild or :GoTestCompile based on the go file
function! s:build_go_files()
  let l:file = expand('%')
  if l:file =~# '^\f\+_test\.go$'
    call go#test#Test(0, 1)
  elseif l:file =~# '^\f\+\.go$'
    call go#cmd#Build(0)
  endif
endfunction

autocmd FileType go nmap <leader>b :<C-u>call <SID>build_go_files()<CR>
```

これで `<leader>b` を押せばいつでもシームレスの Go ファイルをビルドするか
テストファイルをコンパイルするようになります。

* デフォルトではリーダーショートカットは `\` として定義されています。
私は `,` のほうがより便利だと気づいたので以下の設定 (これを .vimrc の
先頭に入れてください) でリーダーを `,` にマップしています。

```vim
let mapleader = ","
```

この設定をすれば、テストもテストでないファイルも `,b` で簡単にビルドできます。

# カバレッジを見る

テストの世界により深く飛び込んでみましょう。テストは非常に大切です。
Go はソースコードのカバレッジを表示するとても素晴らしい方法を提供しています。
vim-go はVimを抜けることなくとてもエレガントにコードカバレッジを見るのを
簡単にします。

まず `main_test.go` ファイルを以下のように戻しましょう。

```go
package main

import (
	"testing"
)

func TestBar(t *testing.T) {
	result := Bar()
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}
}
```

そして `main.go` を以下のようにします。


```go
package main

func Bar() string {
	return "bar"
}

func Foo() string {
	return "foo"
}

func Qux(v string) string {
	if v == "foo" {
		return Foo()
	}

	if v == "bar" {
		return Bar()
	}

	return "INVALID"
}
```

そうしたら `:GoCoverage` を実行しましょう。裏では `go test -coverprofile
tempfile` が実行されます。プロファイルの出力結果を解釈しカバレッジを反映
するようにソースコードのハイライトを動的に変更します。実行結果を見ると、
`Bar()` 関数に対してのテストしかないため、 `Bar()` 関数だけが緑色になります。

ハイライトをクリアするには `:GoCoverageClear` を実行します。テストケースを
追加してカバレッジがどのように変化するか見てみましょう。以下のコードを
`main_test.go` に追加してください。

```go
func TestQuz(t *testing.T) {
	result := Qux("bar")
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}

	result = Qux("qux")
	if result != "INVALID" {
		t.Errorf("expecting INVALID, got %s", result)
	}
}
```

再び `:GoCoverage` を実行すると今度は `Quz` 関数もテストされてカバレッジが
より広くなるのが見られるでしょう。 `:GoCoverageClear` を再度実行すると
ハイライトがクリアされます。

`:GoCoverage` と `:GoCoverageClear` を実行するのははセットで使われることが
多いので実行して結果をクリアするのを簡単にする別のコマンド `:GoCoverageToggle`
が用意されています。これはトグルとして動作し、一度実行するとカバレッジを表示し、
再度実行するとカバレッジをクリアします。これらのコマンドをどのように使うかは
あなたのワークフロー次第です。

最後に、もし vim-go の内蔵ビューが好きでない場合は、 `:GoCoverageBrowser` を
実行することもできます。これは裏で `go tool cover` を実行してHTMLページを
作成しデフォルトブラウザで開きます。人によってはこちらのほうが好みでしょう。

`:GoCoverageXXX` 系のコマンドを使ってもどんな種類の中間ファイルも作らず
あなたのワークフローを汚染しません。ですので不要なファイルを毎回消すような
必要はありません。

### vimrc の改善

以下の設定を `.vimrc` に追加してください。

```vim
autocmd FileType go nmap <Leader>c <Plug>(go-coverage-toggle)
```

この設定を追加すると `<leader>c` で `:GoCoverageToggle` を簡単に実行できます。


# 編集する

### import文

以下のサンプルの `main.go` から始めましょう。

```go
package main

     import "fmt"

func main() {
 fmt.Println("gopher"     )
}
```

既に知っていることから始めましょう。ファイルを保存すると自動的にフォーマット
されるのを見るでしょう。これはデフォルトで有効になっていますが、もし希望すれば
(なぜ希望するかは謎ですが :)) `let g:go_fmt_autosave = 0` で無効にできます。
私たちは `:GoFmt` コマンドも用意していて、それは裏で `gofmt` を実行します。

`"gopher"` の文字列を全て大文字で出力してみましょう。そのために `strings`
パッケージを使用します。定義を以下のように変更しましょう。

```go
fmt.Println(strings.ToUpper("gopher"))
```

ビルドすると当然エラーが出ます。

```
main.go|8| undefined: strings in strings.ToUpper
```

このエラーが出るのは `strings` パッケージがインポートされていないからです。
vim-go はimport文の宣言を操作するためにいくつかのコマンドを提供しています。

移動してファイルを編集することも簡単にできますが、代わりに `:GoImport` という
Vimコマンドを使います。このコマンドは指定したパッケージをimportパスに追加します。
`:GoImport strings` と実行してください。 `strings` パッケージが追加されるのが
見られるでしょう。このコマンドの素晴らしいところは補完をサポートしていることです。
ですので `:GoImport s` とだけ入力してタブを押すことができます。

importパスを編集するために `:GoImportAs` と `:GoDrop` も用意されています。
`:GoImportAs` は `:GoImport` と同じですが、パッケージ名を変更することが
可能です。例えば `:GoImportAs str strings` は `strings` を `str` という
パッケージ名でインポートします。

最後に `:GoDrop` はimport宣言からimportパスを簡単に削除できます。
`:GoDrop strings` は `strings` をimport宣言から削除します。

もちろんインポートパスを手動で操作するのはとても 2010 年っぽい (訳注: 時代遅れ)
です。この問題を扱うもっとよいツールを提供しています。もしまだ聞いたことがなければ
それは `goimports` と呼ばれています。 `goimports` は `gofmt` を置き換えるものです。
使用方法は2通りあります。最初の(そして推奨される)使い方はファイルを保存したときに
実行するように vim-go に伝えることです。

```
let g:go_fmt_command = "goimports"
```

これでファイルを保存するたびに `goimports` が自動的にコードを整形しimport宣言も
書き換えます。コードベースが非常に巨大な場合は遅くなるかもしれないので `goimports`
を好まない人もいます。このために私たちは `:GoImports` コマンド (最後の `s` に
注意) も用意しています。これで明示的に `goimports` を実行することができます。


### テキストオブジェクト

さらなる編集のチップスとトリックをお見せしましょう。関数を変更するのに使える
2つのテキストオブジェクトがあります。 `if` と `af` です。 `if` は関数の内側を
意味し関数の囲いの中身を選択できるようにします。 `main.go` を以下のように
変更してください。


```go
package main

import "fmt"

func main() {
	fmt.Println(1)
	fmt.Println(2)
	fmt.Println(3)
	fmt.Println(4)
	fmt.Println(5)
}
```

`func` キーワードにカーソルを置いて、ノーマルモードで以下のコマンドを実行して
何が起きるか見てください。

```
dif
```

関数のボディが削除されるのが見られるでしょう。 `d` オペレータを使ったからです。
`u` を押して変更をアンドゥしてください。素晴らしいのはカーソルは `func` キーワード
の開始点から閉じ波括弧 `}` のどこにあっても良いことです。裏では
[motion](https://github.com/fatih/motion) というツールを使っています。
私はまさにこういう機能を vim-go でサポートするためにmotionを書きました。
これはGoのAST (訳注: 抽象構文木)を理解しているので非常に良いです。
どんな感じか気になるって？ `main.go` を以下のように変更してください。

```go
package main

import "fmt"

func Bar() string {
	fmt.Println("calling bar")

	foo := func() string {
		return "foo"
	}

	return foo()
}
```

以前は正規表現ベースのテキストオブジェクトを使っていました。そしてそれは
問題を引き起こしました。例えばこの例で、カーソルを無名関数の `func` キーワード
に置いてノーマルモードで `dif` を実行してください。(訳注: 今のバージョンでは)
無名関数のボディだけが正しく削除されるのが見られるでしょう。

ここまでは `d` オペレータ (削除) だけを使ってきました。しかし、これはあなた
次第で、例えば `vif` を使って選択もできますし、 `yif` でヤンク(コピー)も
できます。

さらに `af` もあり、これは `a function` という意味です。このテキストオブジェクト
は関数の宣言全体を含みます。 `main.go` を以下のように変更してください。


```go
package main

import "fmt"

// bar returns a the string "foo" even though it's named as "bar". It's an
// example to be used with vim-go's tutorial to show the 'if' and 'af' text
// objects.
func bar() string {
	fmt.Println("calling bar")

	foo := func() string {
		return "foo"
	}

	return foo()
}
```

ここからが凄いところです。 `motion` のおかげで私たちはありとあらゆる文法
ノードについての知識を持っています。カーソルを `func` キーワードの先頭か
そこより下か上 (どちらでもよいです) のどこか (訳注: ドキュメントコメントから
関数の綴じ波括弧の間のどこかの行) に置いてください。そして `vaf` を
実行すると関数の宣言が、ドキュメントコメントとともに選択されます！
例えば `daf` で関数全体をコメントと一緒に削除できます。
コメントの先頭行にカーソルを置いて `vif` を実行し次に `vaf` を実行してみて
ください。カーソルが関数の外側にあるにも関わらず、最初は関数のボディが選択され、
次は関数のコメントも選択されることがわかるでしょう。

これはとても強力です。これは全て
`let g:go_textobj_include_function_doc = 1` という設定で `motion` から得られる
情報のおかげです。コメントが関数宣言の一部として扱われてほしくない場合は
以下の設定で簡単に無効にできます。

```vim
let g:go_textobj_include_function_doc = 0
```

`motion` についてもっと知りたければ、より詳細を書いたブログ記事を参照してください。
[Treating Go types as objects in vim](https://medium.com/@farslan/treating-go-types-as-objects-in-vim-ed6b3fad9287#.45q2rtqgf)

(追加の任意の質問: `go/ast` パッケージを見ていませんが、ドキュメントコメント
は関数宣言の一部なのかそうでないのかどちらでしょうか?)

### 構造体リテラルの行分割と結合
Goの構造体リテラルを行分割と結合してくれる素晴らしいプラグインがあります。
実際はGo用のプラグインではないのですが、Goの構造体もサポートしています。
有効にするには `vimrc` の `plug` 定義の中にプラグインのディレクティブを追加
し `:PlugInstall` を実行してください。例を示します。

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'AndrewRadev/splitjoin.vim'
call plug#end()
```

プラグインをインストールしたら `main.go` を以下のように変更してください。

```go
package main

type Foo struct {
	Name    string
	Ports   []int
	Enabled bool
}

func main() {
	foo := Foo{Name: "gopher", Ports: []int{80, 443}, Enabled: true}
}
```

カーソルを構造体のリテラルと同じ行に置いてください。そうしたら `gS` とタイプしてください。
これは構造体のリテラルを複数行に「分割」します。そして逆のこともできます。
カーソルがまだ `foo` 変数にあったらノーマルモードで `gJ` を実行してください。
フィールド定義が全て結合されて1行になるのが見られるでしょう。

これはAST (抽象構文木) を理解するツールを使っていないので、例えばフィールドの
先頭で `gJ` を押すと2つのフィールドだけが行結合されるのを見ることになるでしょう。

### スニペット

vim-go は2つのポピュラーなスニペットプラグインをサポートしています。
[Ultisnips](https://github.com/SirVer/ultisnips) と
[neosnippet](https://github.com/Shougo/neosnippet.vim) です。デフォルトでは
`Ultisnips` をインストールしていればそのまま動きます。まず `ultisnips` を
インストールしてみましょう。 `vimrc` の `plug` ディレクティブの間に追加して
`:PlugInstall` を実行しましょう。例を示します。

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'SirVer/ultisnips'
call plug#end()
```

多くの有用なスニペットがあります。私たちの最新のスニペットの完全な一覧を見るには
https://github.com/fatih/vim-go/blob/master/gosnippets/UltiSnips/go.snippets
を参照してください。

私は最も使っているいくつかのスニペットを紹介しましょう。 `main.go` の内容を
以下のように変更してください。

```go
package main

import "encoding/json"

type foo struct {
	Message    string
	Ports      []int
	ServerName string
}

func newFoo() (*foo, error) {
	return &foo{
		Message:  "foo loves bar",
		Ports: []int{80},
		ServerName: "Foo",
	}, nil
}

func main() {
	res, err := newFoo()

	out, err := json.Marshal(res)
}
```

(訳注: main関数の) `newFoo()` の次の行にカーソルを置きましょう。 err がnilで
なければ panic するようにしてみましょう。インサートモードで `errp` を押して
`tab` を押すだけです。スニペットが展開されてカーソルが `panic()` 関数の内側
に移動するのが見られるでしょう。

```
if err != nil {
    panic( )
          ^
          カーソル位置
}
```

panicの中身に `err` と入力して `json.Marshal` の行にカーソルを移動してください。
同様にコードを追加しましょう。

今度は `out` 変数を出力してみましょう。変数を出力するのはとても頻繁に行われる
ので、いくつかのスニペットが用意されています。

```
fn -> fmt.Println()
ff -> fmt.Printf()
ln -> log.Println()
lf -> log.Printf()
```

ここで `ff` と `lf` は特別です。これらは変数名をフォーマット文字列にも動的に
コピーします。試してみてください。main関数の最後にカーソルを移動して `ff` と
タイプしタブを入力します。そして `string(out)` とタイプするとタイプした文字列が
フォーマット文字列と可変長引数の両方に入力されるのが見られるでしょう。

これはデバッグ用に変数を出力するのを素早く行うのに非常に便利です。
`:GoRun` で実行すると以下のような出力が見られるはずです。

```
string(out) = {"Message":"foo loves bar","Ports":[80],"ServerName":"Foo"}
```

素晴らしい。最後に私はとても有用だと思っているスニペットの1つを紹介しましょう。
先程の出力を見たときに `Message` と `Ports` のフィールドが大文字で開始されていた
ことに気づいたでしょう。これを修正するために構造体のフィールドにjsonタグを追加
することができます。vim-goはフィールドタグを追加するのをとても簡単にしてくれます。
`Message string` の行の最後にカーソルを移動してください (訳注: Message string
の行でノーマルモードで `A` を入力して行末に追加するようにしてインサートモードに
切り替えさらにスペースを1つ押します)。

```
type foo struct {
    Message string .
                   ^ ここにカーソルを置いてください。
}
```

インサートモードで `json` とタイプしタブを押します。自動的に有効なフィールド
タグに展開されるのが見られるはずです。フィールド名は自動的に小文字に変換されて
入力されます。以下のような状態になっているはずです。
(訳注: タブを1回押した時点では `message` が選択された状態になっていてそこから
タイプすると違う文字列に変更することもできます。逆に `message` のまま確定するには
もう一度タブを押せばOKです)。

```
type foo struct {
	Message  string `json:"message"`
}
```

とても素晴らしいです。でももっと良くできます！さらに進んで `ServerName` フィールド
のスニペット展開をしてみましょう。今度は `server_name` と変換されます。
素晴らしいでしょう？

```go
type foo struct {
	Message    string `json:"message"`
	Ports      []int
	ServerName string `json:"server_name"`
}
```

### vimrc の改善

* 忘れずに `gofmt` を `goimports` に変更しましょう。

```vim
let g:go_fmt_command = "goimports"
```

* ファイルを保存すると、 `gofmt` がそのファイルをパーズしたときにエラーがあれば
  それを表示します。パーズエラーがあればクイックフィクスリスト内に表示します。
  これはデフォルトで有効になっています。これが好きでない人もいます。無効にするには
  以下の設定を追加してください。

```vim
let g:go_fmt_fail_silently = 1
```

* 大文字小文字がどのように変換されるかを変更することができます。デフォルトでは
  vim-go は `snake_case` を使います。でも希望すれば `camelCase` を使うことも
  できます。例えばデフォルト値をキャメルケースに変更したい場合は以下の設定が
  使えます。

```vim 
let g:go_addtags_transform = "camelcase"
```

# 美しく表示する

デフォルトでは限定されたシンタクスハイライトのみが有効になっています。
これには2つの大きな理由があります。1つめは人々は色が多いと気が散るので
色が多いのが好きではないことです。2つめの理由はVimのパフォーマンスに
大きく影響を与えることです。有効にするには明示的に設定する必要があります。
まず `.vimrc` に以下の設定を追加してください。

```vim
let g:go_highlight_types = 1
```

これで以下のコードの `bar` と `foo` がハイライトされます。

```
type foo struct{
  quz string
}

type bar interface{}
```

さらに以下の設定を追加します。

```vim
let g:go_highlight_fields = 1
```

すると以下のコードの `quz` がハイライトされます。

```go
type foo struct{
  quz string
}


f := foo{quz:"QUZ"}
f.quz # ここの quz がハイライトされます
```

以下の設定を追加すると

```vim
let g:go_highlight_functions = 1
let g:go_highlight_methods = 1
```

今度は関数名もハイライトされます。 `Foo` と `main` が新たにハイライト
されます。

```go
func (t *T) Foo() {}

func main() {
}
```

`let g:go_highlight_operators = 1` を追加すると以下の演算子がハイライト
されます。

```
- + % < > ! & | ^ * =
-= += %= <= >= != &= |= ^= *= ==
<< >> &^
<<= >>= &^=
:= && || <- ++ --
```

`let g:go_highlight_extra_types = 1` を追加すると以下のような型もハイライト
されます。

```
bytes.(Buffer)
io.(Reader|ReadSeeker|ReadWriter|ReadCloser|ReadWriteCloser|Writer|WriteCloser|Seeker)
reflect.(Kind|Type|Value)
unsafe.Pointer
```

さらにより有用なハイライトに移りましょう。ビルドタグはどうでしょう？
`go/build` のドキュメントを見ずにビルドタグを正しく書くのは簡単ではありません。
まず `let g:go_highlight_build_constraints = 1` を設定して `main.go` を
以下のように変更してみましょう。

```
// build linux
package main
```

`// build linux` の行がグレーになります。有効ではないことを示しています。
`build` の前に `+` を追加して再度保存します。

```
// +build linux
package main
```

(訳注: まだグレーです。)
なぜだかわかりますか？ `go/build` パッケージのドキュメントをよく読むと
以下のように書かれているのに気づくでしょう。

> ... 他の行コメントの前に空行のみを入れる必要があります。

再度変更して保存してみましょう。

```
// +build linux

package main
```

有効なことを示す色で自動的にハイライトされるのを見るでしょう。これはとても
素晴らしいです。 `linux` を他の何かに変更して有効な公式のタグになっているか
チェックしてみましょう (`darwin` 、 `race` 、 `ignore` などがあります)。

別の似たような機能として `//go:generate` のGoディレクティブをハイライトする
機能があります。 .vimrcに `let g:go_highlight_generate_tags = 1` を追加すると
`go generate` コマンドで処理される有効なディレクティブがハイライトされます。

ハイライトの設定はもっとたくさんあって、これらはほんの一部です。より多くの
設定について知るには `:help go-settings` を参照してください。


### vimrc の改善

* 一部の人はタブの表示され方が好きではありません。デフォルトではVimは1つのタブ
  を `8` つのスペースとして表示します。しかしVimでどのように表示されるかは
  私たち次第です。以下のように設定すれば1つのタブが4つのスペースとして表示
  されます。

```
autocmd BufNewFile,BufRead *.go setlocal noexpandtab tabstop=4 shiftwidth=4 
```

この設定はタブをスペースに展開はしません。1つのタブを `4` つのスペースとして
表示するだけです。1段のインデントを表すのに `4` つのスペースを使います。
(訳注: この設定の場合見た目は `4` つのスペースですが入力されるのは1つのタブです)。

* 多くの人が私のカラースキームについて質問します。私は `molokai` を少し
修正したものを使っています。これを有効にするにはプラグイン定義の間に以下の
Plug ディレクティブを追加してください。

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'fatih/molokai'
call plug#end()
```

さらに以下の設定を追加するとオリジナルのカラースキームと256色バージョンの
molokai を有効にできます。

```vim
let g:rehash256 = 1
let g:molokai_original = 1
colorscheme molokai
```

設定を追加したらVimを再起動して `:PlugInstall` を実行してください。これで
プラグインがダウンロードされインストールされます。プラグインがインストール
されたら再びVimを再起動してください。

# チェックする

これまでの例で多くのコマンドが何か問題があったらクイックフィクスウィンドウを
表示するのを見てきました。例えば `:GoBuild` は (もしあれば) コンパイル出力
からエラーを表示します。あるいは例えば `:GoFmt` はカレントファイルを整形する
際にパーズエラーがあればそれを表示します。

私たちには他にも実行してその後エラー、警告、提案を集める多くのコマンドがあります。

例えば `:GoLint` です。裏では `golint` というGoのコードをより慣用的にする
変更を提案してくれるコマンドを実行します。また `:GoVet` もあり、こちらは
裏で `go vet` を実行します。他にもさまざまなことをチェックするツールが多数
あります。より簡単にするために、これらのチェックツールを全部まとめて呼び出す
ツールを作った人がいます。このツールは `gometalinter` と呼ばれています。
そして vim-go は `:GoMetaLinter` コマンドでこれをサポートしています。
これは何をしてくれるのでしょう？

あるGoのソースコードに対して `:GoMetaLinter` を単に実行すると、デフォルトでは
`go vet` 、 `golint` と `errcheck` を同時に実行します。 `gometalinter` は全ての
出力を集めて共通のフォーマットに標準化します。このため、 `:GoMetaLinter` を
実行すると vim-go はこれら全てのチェッカーの結果をクイックフィクスリスト内に
表示します。 lint 、 vet と errcheck の結果の間を簡単にジャンプできます。
このデフォルトの設定は以下のとおりです。

```vim
let g:go_metalinter_enabled = ['vet', 'golint', 'errcheck']
```

他にも多くのツールが有りこれらのリストは簡単にカスタマイズできます。
もし `:GoMetaLinter` を実行すると上記のリストを自動的に使います。

`:GoMetaLinter` は普通は速いので vim-go はファイルを保存したらいつでも
(ちょうど `:GoFmt` のように) それを実行します。これを有効にするには
`.vimrc` に以下の設定を追加します。

```vim
let g:go_metalinter_autosave = 1
```

素晴らしいことに自動保存の際に実行するチェッカーは `:GoMetaLinter` コマンドを
実行したときと違うものにできます。ファイルを保存したときには速いチェッカーだけを
実行し、 `:GoMetaLinter` を実行したときには他のチェッカーも実行するように
カスタマイズ可能となるのでこれは素晴らしいです。以下の設定で自動保存機能に対して
実行されるチェッカーをカスタマイズできます。


```vim
let g:go_metalinter_autosave_enabled = ['vet', 'golint']
```

このようにデフォルトでは `vet` と `golint` が有効になっています。最後に
`:GoMetaLinter` の実行時間が長くなりすぎないように、指定したタイムアウトを
超えたら実行をキャンセルするための設定もあります。デフォルトでは `5秒` ですが
以下の設定で変更可能です。

```vim
let g:go_metalinter_deadline = "5s"
```

# ナビゲートする

これまで私たちは `main.go` と `main_test.go` の2つのファイル間だけでジャンプ
していました。同じディレクトリに2つのファイルしかない場合は切り替えはとても
簡単です。でもプロジェクトが時間とともにどんどん大きくなってきたらどうでしょうか？
あるいはファイル自体がとても巨大になりナビゲートするのが大変になってきたらどうでしょう？


### 代替ファイル

vim-go はナビゲーションを改善するいくつかの方法を提供しています。まず
Goのソースファイルとそのテストファイルの間を素早くジャンプする方法を
紹介します。

`foo.go` とそのテストファイル `foo_test.go` の両方があるとしましょう。
これまでの例の `main.go` があればそれを開いてください。開いたら
以下の Vim コマンドを実行してください。

```vim
:GoAlternate
```

するとすぐに `main_test.go` に切り替わるのが見られるでしょう。再度実行すると
`main.go` に切り替わります。 `:GoAlternate` はトグルとして機能し、多くの
テストファイルがあるパッケージを持っている場合は非常に役立ちます。
概念は [a.vim](https://github.com/vim-scripts/a.vim) プラグインのコマンド名に
非常に近いです。このプラグインは `.c` と `.h` ファイルの間でジャンプします。
私たちの場合は `:GoAlternate` がテストと非テストファイルの間の切り替えに
使われます。

### 定義へジャンプ

最も使われる機能の一つが `定義へジャンプ` です。 vim-go は当初から
`:GoDef` コマンドを持っており、あらゆる識別子の宣言箇所にジャンプする
ことができます。まず `main.go` を作って、実際に動く様子を見てみましょう。
以下の内容で作成してください。

```go
package main

import "fmt"

type T struct {
	Foo string
}

func main() {
	t := T{
		Foo: "foo",
	}

	fmt.Printf("t = %+v\n", t)
}
```

さて定義へジャンプする方法はいくつかあります。例えばmain関数の直後の `T`
構造体リテラルの先頭にカーソルを置いて `:GoDef` コマンドを実行すると
型の定義にジャンプします。

main関数のすぐ後の `t` 変数の宣言の先頭にカーソルを置いて `:GoDef` を
実行すると何も起きません。これはジャンブする先がない (訳注: すでに宣言の
場所にいる) からです。でも数行下に移動して `fmt.Printf()` の中で使われている
`t` 変数にカーソルを移動して `:GoDef` を実行すると、変数の宣言箇所にジャンプ
するのが見られるでしょう。

`:GoDef` はローカルスコープで機能するだけではなくグローバルでも (`GOPATH`
の範囲で) 機能します。例えば、 `Printf()` 関数の先頭にカーソルを置いて
`:GoDef` を実行すると `fmt` パッケージに直接ジャンプします。
これをとても頻繁に使用されるので vim-go はVimにビルトインのショートカット
`gd` と `ctrl-]` の設定を上書きします。ですので `:GoDef` を実行する代わりに
`gd` か `ctrl-]` を簡単に使用できます。

一旦宣言にジャンプしたら、元の場所に戻りたいでしょう。デフォルトでは元の
カーソル位置にジャンプする `ctrl-o` という Vim のショートカットがあります。
これはきちんと機能するときは素晴らしいのですが、Goの宣言の間をナビゲート
するときはあまり良くありません。例えば `:GoDef` でジャンプした後ファイルの
末尾までスクロールし、また先頭に移動したりすると `ctrl-o` はそれらの位置も
覚えてしまいます。ですので `:GoDef` を実行したときの元の位置に戻りたい場合
`ctrl-o` を複数回押す必要があります。これは本当にうっとうしいです。

でもこのショートカットを使う必要はありません。というのもvim-goはもっとよい
実装を提供しているからです。まさにこれを実現する `:GoDefPop` というコマンド
があります。vim-go は `:GoDef` で訪れた全ての場所を管理する内部のスタックリスト
を維持しています。これは `:GoDefPop` で元いた場所に簡単に戻れることを意味し、
ファイル内で上下にスクロールしても機能します。そしてこれも非常に何度も使われる
ため、私たちは `ctrl-t` というショートカットを用意しています。これは裏では
`:GoDefPop` を実行します。ということで要約すると以下のようになります。

* ローカルまたはグローバルに定義にジャンプするには `ctrl-]` か `gd` を使います。
* 元の場所に戻るには `ctrl-t` を使います。

別の疑問に移りましょう。ここまでジャンプしてきて最初の地点に戻りたいとしましょう。
上に書いたように vim-go は `:GoDef` を実行して移動したすべての場所の履歴を
維持しています。これら全てを表示するコマンド `:GoDefStack` があります。
これを実行するとカスタムウィンドウが開き、これまで移動した場所の一覧が
表示されます。あとは希望する場所の行に移動してエンターキーを押すだけです。
そして最後にスタックリストをクリアしたい場合は `:GoDefStackClear` を実行します。


### 関数の間を移動する

この前の例でジャンプしたい場所がわかっている場合は `:GoDef` が便利でした。
でも次の目的地が何かわかっていない場合はどうでしょうか？あるいは関数の名前
の一部しか知らない場合はどうでしょうか？

`編集する` の項で私は `motion` と呼ばれるツールに言及しました。それは
vim-go のためだけに作られたカスタムツールです。 `motion` は別の能力も
持っており、あなたの GO パッケージをパーズして全ての宣言をしっかり理解
しています。私たちはこの機能の利点を活かして宣言の間をジャンプできます。
2つのコマンドがありますが、特定のプラグインをインストールしないと利用可能に
なりません。コマンドは以下の2つです。

```
:GoDecls
:GoDeclsDir
```

まず必要なプラグインをインストールしてこれら2つのコマンドを有効にしましょう。
プラグインは [ctrlp](https://github.com/ctrlpvim/ctrlp.vim) と呼ばれています。
長い間 Vim を使っているユーザなら既にインストールしていることでしょう。
インストールするには `plug` ディレクティブに以下の設定を追加して
`:PlugInstall` コマンドを実行します。

```vim
Plug 'ctrlpvim/ctrlp.vim'
```

インストールされたら、 `main.go` の中身を以下のように変更してください。

```go
package main

import "fmt"

type T struct {
	Foo string
}

func main() {
	t := T{
		Foo: "foo",
	}

	fmt.Printf("t = %+v\n", t)
}

func Bar() string {
	return "bar"
}

func BarFoo() string {
	return "bar_foo"
}
```

そして `main_test.go` を以下の内容にします。

```go
package main

import (
	"testing"
)

type files interface{}

func TestBar(t *testing.T) {
	result := Bar()
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}
}

func TestQuz(t *testing.T) {
	result := Qux("bar")
	if result != "bar" {
		t.Errorf("expecting bar, got %s", result)
	}

	result = Qux("qux")
	if result != "INVALID" {
		t.Errorf("expecting INVALID, got %s", result)
	}
}
```

`main.go` を開いて `:GoDecls` を実行してください。 `:GoDecls` が全ての型と
関数の宣言を表示するのが見られるでしょう。 `ma` とタイプすると `ctrlp` が
一覧をフィルタリングしてくれるのが見られます。エンターを押すとそこにジャンプ
します。 `motion` のAST (抽象構文木) の機能とあいまい検索機能の組み合わせにより
とても簡単に使えるにもかかわらず強力な機能を実現しています。

例えば `:GoDecls` を実行して `foo` と入力します。すると `BarFoo` が絞り込み
されます。 Go のパーザは非常に速く何百という宣言を持つ巨大なファイルでも
非常によく機能します。

ときにはカレントファイル内で検索するだけでは十分ではないこともあります。
Go のパッケージは (テストのように) 複数のファイルを持ちます。
1つの型宣言は1つのファイルにあるかもしれませんが、ある別の1組の機能に
関わるある関数は別のファイルにあるかもしれません。こういうときは
`:GoDeclsDir` が便利です。あるファイルのディレクトリ全体をパーズして
そのディレクトリ (サブディレクトリは除く) のファイルの全ての宣言を
一覧表示します。

`:GoDeclsDir` を実行してみてください。今度は `main_test.go` ファイルに
含まれる定義も一覧に追加されるのが見られるでしょう。 `Bar` とタイプすると
`Bar` と `TestBar` 関数の両方が表示されます。全ての型と関数の宣言の
概要を知りたいだけ、あるいはそれらにジャンプしたいときも、これは非常に
素晴らしいです。

質問を続けましょう。次の関数または前の関数に移動したいときはどうすれば
良いでしょう？今カーソルがある関数のボディが長い場合はおそらく関数名が
見えないでしょう。あるいは今いる関数と別の関数の間に別の宣言があるかも
しれません。

Vim には既に次の単語に移動する `w` や前の単語に移動する `b` という
移動操作を提供しています。でもGoの抽象構文木に対して移動できるとしたら
どうでしょう？例えば関数の宣言とか。

vim-go では関数の間を移動する2つのモーションオブジェクトを (上書きして)
提供しています。以下の2つです。


```
]] -> jump to next function
[[ -> jump to previous function
```

Vim はこれらのショートカットをデフォルトで持っています。でもこれらは C の
ソースコードに適していて波括弧の間をジャンプするものです。私たちは
もっとうまくやれます。私たちのこれまでの例のようにこの操作のために裏で
`motion` が使われています。

`main.go` を開いてファイルの先頭に移動してください。ノーマルモードで `]]` と
タイプして何が起きるか見てみましょう。 `main()` 関数にジャンプするのが
見られるでしょう。もう一度 `]]` を押すと `Bar()` に移動します。
`[[` を押すと `main()` 関数に戻ります。

`]]` と `[[` はカウントも受け付けます。例えば、再びファイルの先頭に移動して
`3]]` と実行するとソースファイル内で3番めの関数にジャンプします。
そしてさらに、これらは有効なモーションなので、操作とも組み合わせられます！

ファイルの先頭に移動して `d]]` と押すと次の関数の前の全てが削除されます。
例えば1つの便利な使い方としては `v]]` と押して次に `]]` を押すと次の関数も
選択されます。同様にして必要な関数を選択に追加していくことができます。


### .vimrc の改善

* 代替ファイルをどのように開くかを改善することができます。以下の設定を `.vimrc` に
  追加してください。


```vim
autocmd Filetype go command! -bang A call go#alternate#Switch(<bang>0, 'edit')
autocmd Filetype go command! -bang AV call go#alternate#Switch(<bang>0, 'vsplit')
autocmd Filetype go command! -bang AS call go#alternate#Switch(<bang>0, 'split')
autocmd Filetype go command! -bang AT call go#alternate#Switch(<bang>0, 'tabe')
```

これは `:A` 、 `:AV` 、 `:AS` と `:AT` という新しいコマンドを追加します。
ここで `:A` は `:GoAlternate` と同じように機能し、カレントバッファを代替ファイル
で置き換えます。 `:AV` は代替ファイルを垂直方向に新しく分割したビューで開きます。
`:AS` は代替ファイルを水平方向に新しく分割したビューで開きます。
そして `:AT` は新しいタブで開きます。これらのコマンドはあなたがどのように使うかに
よって非常に生産性が高くなるので、これらのコマンドを定義しておくのは有用だと
思います。

* 定義にジャンプするコマンドファミリーは非常に協力でいて使うのは簡単です。
裏ではデフォルトでは `guru` (以前は `oracle` と呼ばれていました) というツールを
使っています。 `guru` は非常に予測しやすいことの素晴らしい軌跡を持っています
(訳注: ここは意味がとれませんでした)。ドットインポートに対しても機能しますし、
ベンダライズされたインポートや明白ではない他の多くの識別子についても機能します。
以前は vim-go は `godef` を使っていて、これは問い合わせに応答するのが非常に
速かったです。最新のリリースでは `:GoDef` に使うコマンドを切り替えることが
簡単にできます。 `godef` を使うように戻すには以下の設定を追加してください。

```vim
let g:go_def_mode = 'godef'
```

* 現状はデフォルトでは `:GoDecls` と `:GoDeclsDir` は型と関数の宣言を表示します。
  これは `g:go_decls_includes` の設定でカスタマイズ可能です。
  デフォルトでは以下の設定になっています。

```
let g:go_decls_includes = "func,type"
```

もし関数の宣言だけを表示したいときは、以下のように変更してください。

```
let g:go_decls_includes = "func"
```

# 理解する

コードを書き、編集し、変更するのはまずコードが何をしているか理解してからで
ないと通常はできません。 vim-go はコードがいったいどういうことをしているのは
理解するのを容易にする方法をいくつか提供しています。

### ドキュメント検索

基本から始めましょう。 Go のドキュメントは非常に良く書けていて Go の抽象構文木
にも高度に統合されています。あるコメントを書くだけでパーザーが簡単にそれを
パーズし、抽象構文木のノードに関連付けることができます。抽象構文木からの
ノードがあればドキュメント (もしあれば) を簡単に読むことができます！

私たちは `:GoDoc` というコマンドを用意していてそれはカーソルの下の識別子に
関連付けられたドキュメントを表示してくれます。 `main.go` の内容を以下のように
変更しましょう。

```go
package main

import "fmt"

func main() {
	fmt.Println("vim-go")
	fmt.Println(sayHi())
	fmt.Println(sayYoo())
}

// sayHi() returns the string "hi"
func sayHi() string {
	return "hi"
}

func sayYoo() string {
	return "yoo"
}
```

`main` 関数の直後の `Println` の先頭にカーソルを置き `:GoDoc` を実行
してください。スクラッチウィンドウが自動的に開きドキュメントを表示して
くれるのが見られるでしょう。

```
import "fmt"

func Println(a ...interface{}) (n int, err error)

Println formats using the default formats for its operands and writes to
standard output. Spaces are always added between operands and a newline is
appended. It returns the number of bytes written and any write error
encountered.
```

ドキュメントではインポートパス、関数のシグネチャ、そして最後に識別子に
関連付けられていたドキュメンテーションコメントが表示されます。当初は
vim-go は単に `go doc` を使っていましたが、バイト識別子に応じて解決する
ことができないなどいくつかの欠点がありました。 `go doc` は端末で使用する
には素晴らしいですが、エディターに統合するのは難しいです。
幸運にも `gogetdoc` と呼ばれる非常に便利なコマンドがあり、このコマンドで
ノードに対する抽象構文木のノードを解決、取得し、関連付けられた
ドキュメンテーションコメントを出力することができます。

このため `:GoDoc` はどんな種類の識別子に対しても機能します。カーソルを
`sayHi()` に置いて `:GoDoc` を実行してもドキュメンテーションコメントが
表示されるでしょう。そして `sayYoo()` にカーソルを置いて実行すると
単に `no documentation` と表示されます。この抽象構文木ノードには
ドキュメンテーションコメントが無いからです。

他の機能と同様に私たちはノーマルモードのデフォルトのショートカット `K`
をオーバーライドして `man` (や他の何か) の代わりに `:GoDoc` を実行する
ようにしています。ドキュメントを見つけるのはとても簡単で、ノーマルモード
で `K` を押すだけです！

`:GoDoc` は指定された識別子に対するドキュメントだけを表示します。しかし
それは **ドキュメント エクスプローラ** ではありません。ドキュメントを
探索したい場合はそれを行ってくれるサードパーティのプラグインがあります。
[go-explorer](https://github.com/garyburd/go-explorer) です。これを
vim-go に含めるべきという未対応のバグがあります
(訳注: 2017-07-24時点では vim-go の github レポジトリのイシューや
プルリクエストには見当たらなかったので、いつか対応したいリスト的な
ものだと推測します)。

### 識別子の解決

時々関数が何を受け付けて何を返すのか知りたいことがあるでしょう。あるいは
カーソルの下にある識別子が何かを知りたいこともあるでしょう。このような
質問はよくあるので私たちはそれに応えるコマンドを用意しています。

同じ `main.go` を使い、 `Println` 関数の上に移動して `:GoInfo` を実行
してください。関数のシグネチャがステータスラインに出力されるのが
見られるでしょう。関数が何をするのかを見られるのでこれは非常に素晴らしい
です。このおかげでわざわざジャンプしてシグネチャをチェックしなくても
よくなります。

`:GoInfo` を毎回実行するのは退屈です。より速く実行するために改善する
ことができます。例によってより速くする方法はショートカットを追加する
ことです。

```vim
autocmd FileType go nmap <Leader>i <Plug>(go-info)
```

これで `<leader>i` を押すだけで簡単に `:GoInfo` を実行できるようになりました。
でももう少し改善の余地があります。 vim-go にはカーソルを移動するたびに自動的に
情報を表示するためのサポートがあります。有効にするには `.vimrc` に以下の
設定を追加してください。

```vim
let g:go_auto_type_info = 1
```

これで有効な識別子にカーソルを移動するたびに、ステータスラインが自動的に
更新されるようになります。デフォルトでは `800ms` 毎に更新します。これは
vim の設定で `updatetime` という設定で変更することができます。 `100ms` に
変更するには `.vimrc` に以下の設定を追加してください。

```vim
set updatetime=100
```

### 識別子のハイライト

マッチする全ての識別子を素早く見たいことがあります。変数、関数などです。
以下の Go コードがあるとします。

```go
package main

import "fmt"

func main() {
	fmt.Println("vim-go")
	err := sayHi()
	if err != nil {
		panic(err)
	}
}

// sayHi() returns the string "hi"
func sayHi() error {
	fmt.Println("hi")
	return nil
}
```

`err` にカーソルを置いて `:GoSameIds` を実行すると全ての `err` 変数が
ハイライトされます。 `sayHi()` にカーソルを置いて実行すると `sayHi()` 関数の
識別子が全てハイライトされます。クリアするには `:GoSameIdsClear` を
実行すれば良いだけです。

毎回手動で実行しなくて済むようになればもっと便利です。 vim-go はマッチする
識別子を自動的にハイライトできます。 `.vimrc` に以下の設定を追加してください。

```vim
let g:go_auto_sameids = 1
```

vim を再起動するともう `:GoSameIds` を手動で実行する必要が無いことが
わかるでしょう。マッチする識別子の変数が自動的にハイライトされるように
なっています。

### 依存パッケージとファイル

ご存知のようにパッケージは複数の依存パッケージとファイルから構成されます。
ディレクトリ内に多くのファイルがあったとしても、 package 節が正しく
書かれているファイルだけがパッケージの一部となります。

パッケージを構成するファイル一覧を見るには以下のコマンドを実行してください。

```
:GoFiles
```

すると以下のように出力されます (私の `$GOPATH` は `~/Code/Go` に設定されています)。

```
['/Users/fatih/Code/go/src/github.com/fatih/vim-go-tutorial/main.go']
```

他にもファイルがあれば、それもリストされるでしょう。このコマンドはビルドの
一部になる Go ファイルだけをリストすることに注意してください。テストファイル
はリストされません。

ファイルが依存しているパッケージを見るには `:GoDeps` を実行してください。
実行すると以下のように出力されるでしょう。

```
['errors', 'fmt', 'internal/race', 'io', 'math', 'os', 'reflect', 'runtime',
'runtime/internal/atomic', 'runtime/internal/sys', 'strconv', 'sync',
'sync/atomic ', 'syscall', 'time', 'unicode/utf8', 'unsafe']
```

### Guru

先程の機能は裏で `guru` というツールを使っていました。そこで guru について
もう少し話しましょう。 guru とは何でしょうか？ Guru は Go のコードを
ナビゲートし理解するためのエディター統合ツールです。全ての機能を見られる
ユーザマニュアルがあります。
[https://golang.org/s/using-guru](https://golang.org/s/using-guru)

---

このマニュアルに含まれる例と同じものを使って vim-go に統合している
機能のいくつかを紹介しましょう。

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	h := make(handler)
	go counter(h)
	if err := http.ListenAndServe(":8000", h); err != nil {
		log.Print(err)
	}
}

func counter(ch chan<- int) {
	for n := 0; ; n++ {
		ch <- n
	}
}

type handler chan int

func (h handler) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	w.Header().Set("Content-type", "text/plain")
	fmt.Fprintf(w, "%s: you are visitor #%d", req.URL, <-h)
}
```

`handler` にカーソルを置いて `:GoReferrers` を実行してください。これは
`vim-go` の `referrers` モードを呼び出します。これは作業領域内の必要な
パッケージ全てをスキャンして、選択された識別子への参照を見つけます。
結果はクイックフィクスリストになりますので、簡単に結果にジャンプできる
はずです。

---

`guru` のモードの1つに `describe` モードがあります。これは `:GoInfo` と
ほぼ同じですが少しだけより進歩しています (より多くの情報を表示してくれます)。
例えば、もし型のメソッドの組があればそれを表示してくれます。選択すると
パッケージの宣言を表示します。

同じ `main.go` ファイルで続けましょう。 (`ServeHTTP` 関数内の)  `URL`
フィールドか `req.URL` にカーソルを置いて `:GoDescribe` を実行してください。
クイックフィクスリストが以下の内容で表示されます。

```
main.go|27 col 48| reference to field URL *net/url.URL
/usr/local/go/src/net/http/request.go|91 col 2| defined here
main.go|27 col 48| Methods:
/usr/local/go/src/net/url/url.go|587 col 15| method (*URL) EscapedPath() string
/usr/local/go/src/net/url/url.go|844 col 15| method (*URL) IsAbs() bool
/usr/local/go/src/net/url/url.go|851 col 15| method (*URL) Parse(ref string) (*URL, error)
/usr/local/go/src/net/url/url.go|897 col 15| method (*URL) Query() Values
/usr/local/go/src/net/url/url.go|904 col 15| method (*URL) RequestURI() string
/usr/local/go/src/net/url/url.go|865 col 15| method (*URL) ResolveReference(ref *URL) *URL
/usr/local/go/src/net/url/url.go|662 col 15| method (*URL) String() string
main.go|27 col 48| Fields:
/usr/local/go/src/net/url/url.go|310 col 2| Scheme   string
/usr/local/go/src/net/url/url.go|311 col 2| Opaque   string
/usr/local/go/src/net/url/url.go|312 col 2| User     *Userinfo
/usr/local/go/src/net/url/url.go|313 col 2| Host     string
/usr/local/go/src/net/url/url.go|314 col 2| Path     string
/usr/local/go/src/net/url/url.go|315 col 2| RawPath  string
/usr/local/go/src/net/url/url.go|316 col 2| RawQuery string
/usr/local/go/src/net/url/url.go|317 col 2| Fragment string
```

フィールドの定義、メソッドの組と `URL` 構造体のフィールドが表示される
でしょう。これは非常に有用なコマンドで、フィールドの定義が必要でその周りの
コードを理解したいときに使えるコマンドです。他のいろいろな識別子で
`:GoDescribe` を実行してみてどんな出力がされるか実験してみましょう。

---

最もよく聞かれる質問の1つはある型が実装しているインターフェースを
どうやって知れば良いのかということです。ある型といくつかのメソッドの組が
あるとしましょう。あなたはその型がどのインタフェースを実装しているかを
知りたいです。 `guru` の `implement` がまさにそれをしてくれて、型が
実装しているインターフェースを探すのを手伝ってくれます。

同じ前の `main.go` ファイルで続けましょう。 `main()` 関数直後の
`handler` 識別子にカーソルを置いてください。 `:GoImplements` を実行すると
クイックフィクスリストが以下の内容になるのが見られるでしょう。


```
main.go|23 col 6| chan type handler
/usr/local/go/src/net/http/server.go|57 col 6| implements net/http.Handler
```

1行目は選択した型で2行目はその型が実装しているインターフェースです。
1つ型が多くのインターフェースを実装することが可能なのでナビゲートできる
ようにクイックフィクスリストを使っています。

---

`guru` のモードのうち役立ちそうな1つが `whicherrs` です。ご存知のように
エラーは単なる値です。ですのでそれらはプログラムで生成されることができ、
それゆえあらゆる型を表すことができます。 `guru` のマニュアルに書かれて
いることを見てみましょう。

> whicherrs モードはエラーの型の値に現れうる可能な定数の組、グローバル
> 変数、そして具象型を報告します。この情報はエラーを処理する際に、扱われて
> きた全ての重要なケースをカバーしていることを確実にするために役立ちます。

ではどのように使うのでしょうか？簡単です。引き続き同じ `main.go` ファイルを
使います。 `http.ListenAndServe` から返される `err` 識別子にカーソルを
置いてください。 `:GoWhicherrs` を実行すると以下の出力が見られるでしょう。

```
main.go|12 col 6| this error may contain these constants:
/usr/local/go/src/syscall/zerrors_darwin_amd64.go|1171 col 2| syscall.EINVAL
main.go|12 col 6| this error may contain these dynamic types:
/usr/local/go/src/syscall/syscall_unix.go|100 col 6| syscall.Errno
/usr/local/go/src/net/net.go|380 col 6| *net.OpError
```

これは古典的な `quick` の出力で、それらの間をナビゲートすることができます。
`err` の値が `syscall.EINVAL` 定数になるかもしれないことや動的な型の
`syscall.Errno` あるいは `*net.OpError` になるかもしれないことがわかるでしょう。
ご覧のようにこれは必要なら異なるエラー処理をするカスタムロジックを実装
するのに非常に有用です。このクエリには guru の `スコープ` を設定しておく
必要があることに注意してください。 `スコープ` とは何か、どのようにして
それを動的に変更するかについてはこの後すぐ説明します。

---

同じ `main.go` ファイルを使って続けましょう。 Go はチャンネルなど並列処理の
プリミティブで有名です。値がチャンネルの間でどのように送られるかを追跡するのは
時折困難になります。より良く理解するために `guru` の `peers` モードがあります。
これはチャンネルの演算子 (送信または受信の操作) について発生しうる送信／受信の
組を表示します。

カーソルを次の式に移動して行全体を選択してください (訳注: 行全体の選択は不要でした)。

```go
ch <- n
```

`:GoChannelPeers` を実行してください。クイックフィクスウィンドウが以下の内容で
表示されるのを見られるでしょう。

```
main.go|19 col 6| This channel of type chan<- int may be:
main.go|10 col 11| allocated here
main.go|19 col 6| sent to, here
main.go|27 col 53| received from, here
```

ご覧のようにチャンネルの作成、どこに送信しているか、どこから受信しているかが
表示されます。これはポインター解析を使っているので、スコープを定義する
必要があります。

---


関数呼び出しとターゲットがどう関係しているかを見てみましょう。今度は
以下の複数のファイルを作ります。 `main.go` は以下の内容にしてください。

```go
package main

import (
	"fmt"

	"github.com/fatih/vim-go-tutorial/example"
)

func main() {
	Hello(example.GopherCon)
	Hello(example.Kenya)
}

func Hello(fn func() string) {
	fmt.Println("Hello " + fn())
}
```

そして `example/example.go` ファイルを以下の内容で作成します。

```go
package example

func GopherCon() string {
	return "GopherCon"
}

func Kenya() string {
	return "Kenya"
}
```

では `main.go` 内の `Hello` 関数にジャンプして `fn()` という関数呼び出しの
先頭にカーソルを置きます。 `:GoCallees` を実行してください。このコマンドは
選択された関数呼び出しに対する可能性のある呼び出しターゲットを表示します。
ご覧のように `example` パッケージの関数宣言が表示されます。これらの関数は
呼び出される側 (callee) です。というのも `fn()` という関数呼び出しから
呼ばれるからです。

再び `main.go` に戻って今度は `Hello()` の関数定義にカーソルを置いてください。
この関数の呼び出し側を見るにはどうしたら良いでしょう？ `:GoCallers` を
実行してください。

以下の出力が見られるはずです。

```
main.go| 10 col 7 static function call from github.com/fatih/vim-go-tutorial.Main
main.go| 11 col 7 static function call from github.com/fatih/vim-go-tutorial.Main
```

他の使用方法と同じようにクイックフィクスウィンドウの中で呼び出し側に簡単に
ナビゲートできます。

最後に、 `callstack` というモードもあります。これは選択部分を含む関数への
コールグラフの根本からの任意のパスを表示します。


`Hello()` 関数内の `fn()` 関数呼び出しにカーソルを戻してください。関数を
選択して `:GoCallstack` を実行してください。出力は以下のようになる
はずです (一部省略しています)。

```
main.go| 15 col 26 Found a call path from root to (...)Hello
main.go| 14 col 5 (...)Hello
main.go| 10 col 7 (...)main
```

`15` 行目から開始して、次は `14` 行目で最後は `10` 行目となっています。
これは (`main()` 関数から始まる) 根本から私たちが選択した
関数 (今のケースでは `fn()`) へのグラフとなっています。

---

`guru` コマンドのほとんどについてはスコープを定義する必要はありません。
`スコープ` とは何でしょう？ 以下に `guru` の
[マニュアル](http://golang.org/s/using-guru) からそのまま引用します。

> ポインタ解析スコープ: いくつかのクエリはポインタ解析を必要とします。
> それは「このポインタはどこを指しうるか？」という疑問に応える技術です。
> 通常は作業領域のすべてのパッケージにポインタ解析を行うのは高くつきすぎる
> のでこれらのクエリはスコープと呼ばれる追加の設定パラメータを要求します。
> スコープが解析対象のパッケージの組を決定します。スコープをあなたが現在
> 作業しているアプリケーション (あるいは一組のアプリケーション ---
> たとえばクライアントとサーバかもしれません) に設定してください。
> ポインタ解析はプログラム全体の解析になります。ですので問題になる
> スコープにあるパッケージは main とテストパッケージだけです。
>
> スコープは通常カンマ区切りのパッケージの組か github.com/my/dir/... の
> ようなワイルドカードのサブツリーとして指定されます。どのようにスコープを
> 設定し変更するかを知るにはお使いのエディタ固有のドキュメントを参照
> してください。


`vim` は自動的にスマートになろうとして現状のパッケージインポートパスを
あなたに代わって `スコープ` として設定します。コマンドがスコープを必要と
する場合はこれでほとんどの場合はカバーできます。ほとんどの場合はこれで
十分ですが、いくつかのクエリではスコープの設定を変更したいかもしれません。
その場で `スコープ` を特定の設定に簡単に変更できるように `:GoGuruScope`
があります。

実行すると `guru scope is not set` (guruのスコープが設定されていません)
というエラーが出ます。 `github.com/fatih/vim-go-tutorial` というスコープ
に明示的に変更してみましょう。

```
:GoGuruScope github.com/fatih/vim-go-tutorial
```

以下のメッセージが表示されるはずです。

```
guru scope changed to: github.com/fatih/vim-go-tutorial
```

`:GoGuruScope` を引数無しで実行すると、以下のように出力されます。

```
current guru scope: github.com/fatih/vim-go-tutorial
```

`GOPATH` 全体を選択するには `...` 引数が使えます。

```
:GoGuruScope ...
```

また複数のパッケージはさらにサブディレクトリを定義することもできます。
以下の例は `github.com` 以下の全てのパッケージと `golang.org/x/tools`
パッケージを選択します。

```
:GoGuruScope github.com/... golang.org/x/tools
```

パッケージの先頭に `-` (マイナス) 記号をつけることでパッケージを除外
することができます。以下のは `encoding` 配下のすべてのパッケージを
`encoding/xml` は除いて選択します。

```
:GoGuruScope encoding/... -encoding/xml
```

スコープをクリアするには空文字列を渡します。

```
:GoGuruScope ""
```

あなたがとあるプロジェクトで作業していてスコープをいつも同じ値に設定している
がVimを起動するたびに `:GoGuruScope` を実行したくない場合は `.vimrc` に
設定を追加することで永続的なスコープを定義することもできます。
値は文字列のリスト型である必要があります。以下に上記のコマンドと同じ内容の
設定例をいくつか示します。

```
let g:go_guru_scope = ["github.com/fatih/vim-go-tutorial"]
let g:go_guru_scope = ["..."]
let g:go_guru_scope = ["github.com/...", "golang.org/x/tools"]
let g:go_guru_scope = ["encoding/...", "-encoding/xml"]
```

最後に `vim-go` はあなたが `:GoGuruScope` を使っている最中にパッケージ名を
オートコンプリートしようとしてもくれます。ですので
`github.com/fatih/vim-go-tutorial` と書こうとしているときは `gi` とだけ
入力してタブを押すと `github.com` と展開されるのが見られるでしょう。

---

あなたが意識するべき別の設定はビルドタグです (ビルド制約とも呼ばれます)。
例えばあなたの Go のソースコードに以下のビルドタグを入れるかもしれません。

```
// +build linux darwin
```

あるときはあなたのソースコードに以下のようなカスタムタグを入れるかもしれません。

```
// +build mycustomtag
```

このケースでは裏で使われている `go/build` パッケージがパッケージを
ビルドできないので guru は失敗するでしょう。すると `guru` に関連するコマンド
は全て (`guru` を使う場合の `:GoDef` でさえも) 失敗するでしょう。
幸いにも `guru` は `-tags` フラグがあり、それを使えばカスタムタグを
スキップすることができます。 `vim-go` ユーザの手間を省けるように
`:GoBuildTags` も提供しています。

例えばちょっと以下のコマンドを実行してみてください。

```
:GoBuildTags mycustomtag
```

こうすると `guru` にこのタグを渡すようになり、これ以降は期待通りに機能します。
`:GoGuruScope` とちょうど同じように、以下のコマンドでクリアできます。

```
:GoBuildTags ""
```

そして最後に、もしあなたが望むなら以下の設定によって永続的にすることもできます。

```
let g:go_build_tags = "mycustomtag"
```

# リファクタリングする

### 識別子をリネームする

最もよく行う作業の1つが識別子をリネームすることです。しかし、それは他の
パッケージを壊さないように注意深く行わなければならないことでもあります。
また `sed` のようなツールを使うだけでは有用でないこともあります。
抽象構文木を理解したリネームを行うには抽象構文機の一部の識別子のみを
リネームする必要があります (例えばビルドスクリプトなど Go のソースでない
別のファイルの中の識別子をリネームすべきではありません)。

あなたのためにこういうリネームを行ってくれるツールがあります。それは
`gorename` と言います。 `vim-go` では `:GoRename` コマンドを使います。
それは裏では `gorename` を使っています。 `main.go` の内容を以下のように
変更しましょう。

```go
package main

import "fmt"

type Server struct {
	name string
}

func main() {
	s := Server{name: "Alper"}
	fmt.Println(s.name) // print the server name
}

func name() string {
	return "Zeynep"
}
```

`Server` 構造体の `name` フィールドの先頭にカーソルを置いて
`:GoRename bar` と実行してください。 全ての `name` の参照が `bar` と
リネームされるのが見られるでしょう。最終的な内容は以下のようになります。

```go
package main

import "fmt"

type Server struct {
	bar string
}

func main() {
	s := Server{bar: "Alper"}
	fmt.Println(s.bar) // print the server name
}

func name() string {
	return "Zeynep"
}
```

ご覧のように必要な識別子だけがリネームされています。でも `name` 関数や
コメントの中の文字列はリネームされていません。もっと良いことに `:GoRename`
は `GOPATH` 以下にある全てのパッケージを検索してその識別子に依存している
全ての識別子をリネームします。とても強力なツールです。

### 関数を抽出する

別の例に移りましょう。 `main.go` ファイルを以下のように変更してください。

```go
package main

import "fmt"

func main() {
	msg := "Greetings\nfrom\nTurkey\n"

	var count int
	for i := 0; i < len(msg); i++ {
		if msg[i] == '\n' {
			count++
		}
	}

	fmt.Println(count)
}
```

これは `msg` 変数に含まれる改行を数えるだけの基本的な例です。実行すると
`3` と出力するのが見られるでしょう。

改行を数えるロジックを外でも使いたいとしましょう。リファクタリングしてみましょう。
Guru は `freevars` モードでこのような状況で私たちを助けてくれます。
`freevars` モードは指定された選択範囲の中で参照されているが定義されていない
変数を表示してくれます。

`visual` モードで以下の部分を選択しましょう。

```go
var count int
for i := 0; i < len(msg); i++ {
	if msg[i] == '\n' {
		count++
	}
}
```

選択したら `:GoFreevars` を実行してください。
`:'<,'>GoFreevars` という形式になるはずです。
結果はまたしてもクイックフィクスリストで自由変数である全ての変数を含んで
います。私たちのケースでは以下の単一の変数になります。


```go
var msg string
```

さてこれがどのように役立つのでしょう？このちょっとした情報でスタンドアローンの
関数にリファクタリングするには十分です。以下の内容で新しい関数を作ってください。

```go
func countLines(msg string) int {
	var count int
	for i := 0; i < len(msg); i++ {
		if msg[i] == '\n' {
			count++
		}
	}
	return count
}
```

関数の中身は私たちが先程選択したコードであることがわかるでしょう。そして
関数への入力は `:GoFreevars` の結果である自由変数です。私たちは何を返すか
(返すものがあれば) を決めただけです。このケースでは count を返します。
`main.go は以下のようになります。

```go
package main

import "fmt"

func main() {
	msg := "Greetings\nfrom\nTurkey\n"

	count := countLines(msg)
	fmt.Println(count)
}

func countLines(msg string) int {
	var count int
	for i := 0; i < len(msg); i++ {
		if msg[i] == '\n' {
			count++
		}
	}
	return count
}
```

このようにして一片のコードをリファクタリングします。 `:GoFreevars` は
コードの複雑さを理解するために使用することができます。実行するだけで
どれだけ多くの変数に依存しているか見ることができます。

# コード生成する

コード生成はホットなトピックです。 go/ast 、 go/parser 、 go/printer などの
素晴らしい標準ライブラリのおかげで、Go は素晴らしいジェネレータを簡単に
作成できるという利点があります。

まず `:GoGenerate` コマンドがあります。これは裏では `go generate` を実行
します。 `:GoBuild` や `:GoTest` などと同じように動きます。エラーがあったら
クイックフィクスリストに表示するので簡単に修正できます。

### インターフェースを実装するメソッドスタブ

インターフェースはコンポジションをするのに非常に素晴らしいです。あなたの
コードをより扱いやすくしてくれます。また、インタフェース型を受け取る関数
をそのインターフェースを実装するメソッドを持つ型でモックすることもできます。


`vim-go` は [impl](https://github.com/josharian/impl) というツールを
サポートしています。 `impl` は指定したインターフェースを実装するメソッド
スタブを生成します。 `main.go` の内容を以下のように変更しましょう。

```go
package main

import "fmt"

type T struct{}

func main() {
	fmt.Println("vim-go")
}
```

`T` にカーソルを置いて `:GoImpl` とタイプしてください。インターフェースを
入力するプロンプトが表示されます。 `io.ReadWriteCloser` と入力してエンター
を押してください。ファイルの内容が以下のように変わるのが見られるでしょう。

```go
package main

import "fmt"

type T struct{}

func (t *T) Read(p []byte) (n int, err error) {
	panic("not implemented")
}

func (t *T) Write(p []byte) (n int, err error) {
	panic("not implemented")
}

func (t *T) Close() error {
	panic("not implemented")
}

func main() {
	fmt.Println("vim-go")
}
```

ご覧のようにこれはとても素敵です。型の上にカーソルを置いて
`:GoImpl io.ReadWriteCloser` と入力しても同じ結果になります。

でも型の先頭にカーソルを置く必要はありません。どこからでも
実行することができます。例えば以下のように実行してください。

```
:GoImpl b *B fmt.Stringer
```

以下のコードが作成されるのが見られるでしょう。

```go
func (b *B) String() string {
	panic("not implemented")
}
```

ご覧のようにこれはとても助かります。特に多数のメソッドの組を持つ
大きなインターフェースを実装するときはそうです。簡単にコードが
生成でき、 `panic()` を使っているのでなんの問題もなくコンパイル
できます。必要な部分を埋めればそれで完了です。

# シェアする

`vim-go` はあなたのコードを https://play.golang.org/ で他の人と簡単に
シェアする機能も持っています。ご存知のように Go playground は小さな
スニペット、練習問題やチップスやトリックをシェアするのに完璧な場所です。
あなたがとあるアイディアを試していて他の人にシェアしたいときがある
でしょう。 `vim-go` はこういったこと全てを `:GoPlay` コマンドでより良く
します。

まず `main.go` ファイルを以下のシンプルなコードに変更しましょう。

```go
package main

import "fmt"

func main() {
	fmt.Println("vim-go")
}
```

では `:GoPlay` と入力してエンターを押しましょう。 `vim-go` が自動的に
ソースコードをアップロードしてブラウザのタブを開いて表示するのが
見られるでしょう。まだあります。スニペットのリンクは自動的にクリップ
ボードにコピーされます。あとはどこかにリンクをペーストするだけです。
リンクは play.golang.org で表示されているものと同じであることがわかるでしょう。

`:GoPlay` は範囲も受け付けます。一片のコードを選択して `:GoPlay` を
実行してください。選択された部分だけがアップロードされます。

`:GoPlay` の振る舞いを変更する設定が2つあります。 `vim-go` がブラウザの
タブを開くのが好きではない場合は以下の設定で無効にできます。

```
let g:go_play_open_browser = 0
```

次にブラウザが誤判定される (私たちは `open` か `xdg-open` を使っています) なら
以下の設定で手動でブラウザを設定できます。

```
let g:go_play_browser_command = "chrome"
```

# 寄付

このチュートリアルは私のプレイベートの時間を使って作られました。この
チュートリアルが気に入って寄付したいと思われたら、
[パトロンになる](https://www.patreon.com/fatih) でぜひ完全なサポーターに
なってください！

パトロンになることで、あなたは vim-go をより成長し成熟させることができ、
私がバグ修正のために調査したり、新しいドキュメントを書いたり、今ある
機能や将来の機能追加を私が行うのを支援することができます。これは完全に
任意で vim-go の現在進行中の開発を直接支援する方法の1つです。
ありがとう！

[https://www.patreon.com/fatih](https://www.patreon.com/fatih)

## 今後説明を追記する予定のコマンド
* :GoPath
* :AsmFmt

