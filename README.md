# vim-go チュートリアル <!-- vim-go-tutorial -->

これは vim-go のチュートリアルです。 vim-go のインストール方法と使い方についての簡単なチュートリアルとなっています。
<!--
Tutorial for vim-go. A simple tutorial on how to install and use vim-go.
-->

(訳注: このチュートリアルは [fatih/vim-go-tutorial: Tutorial for vim-go](https://github.com/fatih/vim-go-tutorial) を翻訳したものです。リンク先の冒頭にあるとおり原文はアーカイブされメンテナンス終了となっています。翻訳に修正・改善がある場合はぜひプルリクエストを送ってください！)

# 目次

1. [クイックセットアップ](#クイックセットアップ-) <!-- [Quick Setup](#quick-setup) -->
2. [Hello World](#hello-world) <!-- [Hello World](#hello-world) -->
3. [実行する](#実行する-) <!-- [Run it](#run-it) -->
4. [ビルドする](#ビルドする-) <!-- [Build it](#build-it) -->
5. [修正する](#修正する-) <!-- [Fix it](#fix-it) -->
6. [テストする](#テストする-) <!-- [Test it](#test-it) -->
7. [カバレッジを見る](#カバレッジを見る-) <!-- [Cover it](#cover-it) -->
8. [編集する](#編集する-) <!-- [Edit it](#edit-it) -->
  * [import文](#import文-) <!-- [Imports](#imports) -->
  * [テキストオブジェクト](#テキストオブジェクト-) <!-- [Text Objects](#text-objects) -->
  * [構造体リテラルの行分割と結合](#構造体リテラルの行分割と結合-) <!-- [Struct split&join](#struct-split-and-join) -->
  * [スニペット](#スニペット-) <!-- [Snippets](#snippets) -->
9. [美しく表示する](#美しく表示する-) <!-- [Beautify it](#beautify-it) -->
10. [チェックする](#チェックする-) <!-- [Check it](#check-it) -->
11. [ナビゲートする](#ナビゲートする-) <!-- [Navigate it](#navigate-it) -->
  * [代替ファイル](#代替ファイル-) <!-- [Alternate files](#alternate-files) -->
  * [定義へジャンプ](#定義へジャンプ-) <!-- [Go to definition](#go-to-definition) -->
  * [関数の間を移動する](#関数の間を移動する-) <!-- [Move between functions](#move-between-functions) -->
12. [理解する](#理解する-) <!-- [Understand it](#understand-it) -->
  * [ドキュメント検索](#ドキュメント検索-) <!-- [Documentation Lookup](#documentation-lookup) -->
  * [識別子の解決](#識別子の解決-) <!-- [Identifier resolution](#identifier-resolution) -->
  * [識別子のハイライト](#識別子のハイライト-) <!-- [Identifier highlighting](#identifier-highlighting) -->
  * [依存パッケージとファイル](#依存パッケージとファイル-) <!-- [Dependencies and files](#dependencies-and-files)
 -->
  * [Guru](#guru) <!-- [Guru](#guru) -->
13. [リファクタリングする](#リファクタリングする-) <!-- [Refactor it](#refactor-it) -->
  * [識別子をリネームする](#識別子をリネームする-) <!-- [Rename identifiers](#rename-identifiers) -->
  * [関数を抽出する](#関数を抽出する-) <!-- [Extract function](#extract-function) -->
14. [コード生成する](#コード生成する-) <!-- [Generate it](#generate-it) -->
  * [インターフェースを実装するメソッドスタブ](#インターフェースを実装するメソッドスタブ-) <!-- [Method stubs implementing an interface](#method-stubs-implementing-an-interface)
 -->
15. [シェアする](#シェアする-) <!-- [Share it](#share-it) -->
16. [HTML テンプレート](#html-テンプレート-) <!-- [HTML template](#html-template) -->

# クイックセットアップ <!-- Quick Setup -->

vim-go をインストールするのに私たちは `vim-plug` を使います。
他のプラグインマネージャを使っても構いません。
私たちは最小限の `~/.vimrc` を作って必要に応じて設定を追加していきます。
<!--
We're going to use `vim-plug` to install vim-go. Feel free to use other plugin
managers instead. We will create a minimal `~/.vimrc`, and add to it as we go along.
-->

まず `vim-go` と共に `vim-plug` を取得してインストールしてください。
<!--
First fetch and install `vim-plug` along with `vim-go`:
-->

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
git clone https://github.com/fatih/vim-go.git ~/.vim/plugged/vim-go
```

以下の内容で `~/.vimrc` を作成してください。
<!--
Create `~/.vimrc` with following content:
-->

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
<!--
Or open Vim and execute `:GoInstallBinaries`. This is a `vim-go` command that
installs all `vim-go` dependencies for you. It doesn't download pre compiled
binaries, instead it calls `go get` under the hood, so the binaries are all
compiled in your host machine (which is both safe and simplifies the
installation process as we don't need to provide binaries for multiple
platforms). If you already have some of the dependencies (such as `guru`,
`goimports`) call `:GoUpdateBinaries` to update the binaries.
-->

このチュートリアルのすべてのサンプルは
`GOPATH/src/github.com/fatih/vim-go-tutorial/`
フォルダで実行されます。
カレントフォルダがこのフォルダになっていることを確認してください。
そうすることでチュートリアルを進めることが簡単になります。
既に `GOPATH` を設定済みであれば、単に以下のように実行してください。
<!--
For the tutorial, all our examples will be under
`GOPATH/src/github.com/fatih/vim-go-tutorial/`. Please be sure you're inside
this folder. This will make it easy to follow the
tutorial. If you already have a `GOPATH` set up just execute:
-->

```
go get github.com/fatih/vim-go-tutorial
```

あるいは必要ならフォルダを作成してください。
<!--
Or create the folder, if necessary.
-->


# Hello World!

ターミナルで `main.go` を開いてください。
<!--
Open the `main.go` file from your terminal:
-->

```
vim main.go
```

これは標準出力に `vim-go` と出力する非常に基本的なファイルになっています。
<!--
It's a very basic file that prints `vim-go` to stdout.
-->

# 実行する <!-- Run it -->

`:GoRun %` でファイルを簡単に実行できます。裏ではカレントファイルに対して
`go run` が実行されます。実行すると `vim-go` と出力されるはずです。
<!--
You can easily run the file with `:GoRun %`. Under the hood it calls `go run` for
the current file. You should see that it prints `vim-go`.
-->

`:GoRun` でパッケージ全体を実行します。
<!--
For whole package run with `:GoRun`.
-->

# ビルドする <!-- Build it -->

`vim-go` を `Hello Gophercon` に置き換えてください。実行する代わりにコンパイル
してみましょう。このために `:GoBuild` があります。実行すると以下のメッセージが
表示されるはずです。
<!--
Replace `vim-go` with `Hello Gophercon`. Let us compile the file instead of running it.
For this we have `:GoBuild`. If you call it, you should see this message:
-->

```
vim-go: [build] SUCCESS
```

裏では `go build` が実行されますが、それよりは少し賢いです。以下の点が異なります。
<!--
Under the hood it calls `go build`, but it's a bit smarter. It does a couple of
things differently:
-->

* バイナリは作られません。 `:GoBuild` を複数回実行しても作業領域を汚染
  することはありません。 <!-- No binaries are created; you can call `:GoBuild` multiple times without
  polluting your workspace. -->
* ソースのパッケージディレクトリに自動的に `cd` します。 <!-- It automatically `cd`s into the source package's directory -->
* ビルドエラーがある場合はパースしてクイックフィクスリストに表示します。 <!-- It parses any errors and shows them inside a quickfix list -->
* 自動的に GOPATH を検知して必要に応じて修正します (`gb` や `Godeps` などの
  プロジェクトを検知します)。 <!-- It automatically detects the GOPATH and modifies it if needed (detects
  projects such as `gb`, `Godeps`, etc..) -->
* NeoVimまたはVim8.0以降のバージョンで実行された場合は非同期に実行します。 <!-- Runs async if used within Vim 8.0.xxx or NeoVim -->

# 修正する <!-- Fix it -->

2つのコンパイルエラーを追加して2つのビルドエラーが起きるようにしてみましょう。
<!--
Let's introduce two errors by adding two compile errors:
-->

```go
var b = foo()

func main() {
	fmt.Println("Hello GopherCon")
	a
}
```

ファイルを保存して再度 `:GoBuild` を実行してください。
<!--
Save the file and call `:GoBuild` again.
-->

今回はクイックフィクスビューが開きます。エラー間をジャンプするのに
`:cnext` と `:cprevious` が使えます。最初のエラーを修正しファイルを保存して
再度 `:GoBuild` を実行してみましょう。クイックフィクスリストはエラー1つに
更新されるでしょう。2つめのエラーも消しファイルを保存して再度 `:GoBulid` を
実行してください。今度はもうエラーがないので vim-go はクイックフィクスウィンドウを
自動的に閉じてくれます。
<!--
This time the quickfix view will be opened. To jump between the errors you can
use `:cnext` and `:cprevious`. Let us fix the first error, save the
file and call `:GoBuild` again. You'll see the quickfix list is updated with a
single error. Remove the second error as well, save the file and call
`:GoBuild` again. Now because there are no more errors, vim-go automatically
closes the quickfix window for you.
-->

もう少し改善してみましょう。 Vim には `autowrite` という設定があり
`:make` を実行したらファイルの内容を自動的に保存できます。 vim-go も
この設定を活用できます。 `.vimrc` を開いて以下の内容を追加してください。
<!--
Let us improve it a little bit. Vim has a setting called `autowrite` that
writes the content of the file automatically if you call `:make`. vim-go also
makes use of this setting. Open your `.vimrc` and add the following:
-->

```
set autowrite
```

(訳注: 原文では省略されていますが `~/.vimrc` に設定を追加した場合は、
vimを再起動するか `:source ~/.vimrc` として設定を反映する必要があります。
以下で `~/.vimrc` に設定を追加する場合も同様です)

これでもう `:GoBuild` を実行するときにファイルを保存する必要はありません。
再度2つのエラーを追加して `:GoBuild` を実行するには `:GoBuild` だけを
実行すれば良いのでより迅速に繰り返すことができます。
<!--
Now you don't have to save your file anymore when you call `:GoBuild`.  If we
reintroduce the two errors and call `:GoBuild`, we can now iterate much more
quickly by only calling `:GoBuild`.
-->

`:GoBuild` を実行すると最初のエラーにジャンプします。ジャンプしたくない場合は
最後に `!` (感嘆符)を付けて `:GoBuild!` と実行してください。
<!--
`:GoBuild` jumps to the first error encountered. If you don't want to jump
append the `!` (bang) sign: `:GoBuild!`.
-->

`:GoRun` 、 `:GoInstall` 、 `:GoTest` など全ての `go` コマンドで、エラーが
あるときはクイックフィクスウィンドウが必ず開きます。
<!--
In all the `go` commands, such as `:GoRun`, `:GoInstall`, `:GoTest`, etc..,
whenever there is an error the quickfix window always will pop up.
-->

### vimrc の改善 <!-- vimrc improvements -->

* 以下のショートカットを追加すればクイックフィクスリスト内のエラー間でジャンプ
するのがより簡単になります。 <!-- You can add some shortcuts to make it easier to jump between errors in quickfix
list: -->

```vi
map <C-n> :cnext<CR>
map <C-m> :cprevious<CR>
nnoremap <leader>a :cclose<CR>
```

* 私はさらに以下のショートカットを使ってGoのプログラムを `<leader>b` と
`<leader>r` でビルド、実行しています。 <!-- I also use these shortcuts to build and run a Go program with `<leader>b` and
`<leader>r`: -->

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
などと使い分けることができます。 <!-- There are two types of error lists in Vim. One is called `location list` the
other `quickfix`. Unfortunately the commands for each lists are different. So
`:cnext` only works for `quickfix` list, for `location lists` you have to use
`:lnext`. Some of the commands in `vim-go` open a location list, because
location lists are associated with a window and each window can have a
separate list. This means you can have multiple windows, and multiple
location lists, one for `Build`, one for `Check`, one for `Tests`, etc..
-->

しかし `クイックフィクス` だけを使いたいユーザもいます。以下の設定を `vimrc` に
追加すれば、すべてのリストは `クイックフィクス` になります。
<!--
Some people prefer to use only `quickfix` though. If you add the following to
your `vimrc` all lists will be of type `quickfix`:
-->

```vim
let g:go_list_type = "quickfix"
```

# テストする <!-- Test it -->

簡単な関数とその関数のテストを書いてみましょう。以下を追加してください。
<!--
Let's write a simple function and a test for the function. Add the following:
-->


```go
func Bar() string {
	return "bar"
}
```

`main_test.go` という新規ファイルを開いてください (どのように開いても
構いません。起動済みのVimからでも良いですし、別のVimセッションからでも良いです。
ご自由にどうぞ)。 ここでは `:edit main_test.go` を実行しカレントバッファを使って
Vimから開きましょう。
<!--
Open a new file called `main_test.go` (it doesn't matter how you open it, from
inside Vim, a separate Vim session, etc.. it's up to you). Let us use the
current buffer and open it from Vim via `:edit main_test.go`.
-->

新規のファイルを開くと何かに気づくでしょう。ファイルには自動的にパッケージ宣言が
追加されています。
<!--
When you open the new file you notice something. The file automatically has the
package declaration added:
-->

```go
package main
```

これは vim-go によって自動的に追加されています。ファイルが有効なパッケージ内に
あることを検出し、パッケージ名に基づいたファイルを作ったのです (今回のケースでは
パッケージ名は `main` でした)。 もしファイルが存在しなかった場合は、 vim-go は
自動的にシンプルな main パッケージの内容を生成します。
<!--
This is done by vim-go automatically. It detected that the file is inside a
valid package and therefore created a file based on the package name (in our
case the package name was `main`). If there are no files, vim-go automatically
populates the content with a simple main package.
-->

テストファイルを以下のコードに更新してください。
<!--
Update the test file with the following code:
-->

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
<!--
Call `:GoTest`. You'll see the following message:
-->

```
vim-go: [test] PASS
```

`:GoTest` は裏で `go test` を実行します。 `:GoBuild` のときと同じ改善点があります。
テストでエラーがある場合、クイックフィクスリストが再び開き簡単にジャンプできます。
<!--
`:GoTest` calls `go test` under the hood. It has the same improvements
we have for `:GoBuild`. If there is any test error, a quickfix list is
opened again and you can jump to it easily.
-->

別のちょっとした改善はテストファイルそのものを開く必要がないということです。
試してみましょう。 `main.go` を開いて `:GoTest` を実行してください。これでも
テストが実行されることがわかるでしょう。
<!--
Another small improvement is that you don't have to open the test file itself.
Try it yourself: open `main.go` and call `:GoTest`. You'll see the tests will
be run for you as well.
-->

`:GoTest` はデフォルトでは10秒でタイムアウトします。Vimはデフォルトでは非同期では
ないのでこの設定は有用です。 `let g:go_test_timeout = '10s'` のように書くことで
タイムアウトの値を変えることができます。
<!--
`:GoTest` times out after 10 seconds by default. This is useful because Vim is
not async by default. You can change the timeout value with `let g:go_test_timeout = '10s'`
-->

テストファイルを簡単に扱うためにさらに2つのコマンドがあります。最初の1つは
`:GoTestFunc` です。これはカーソルの下にある関数だけをテストします。
テストファイル (`main_test.go`) の内容を以下のように変えましょう。
<!--
We have two more commands that make it easy to deal with test files. The first
one is `:GoTestFunc`. This only tests the function under your cursor.
Let us change the content of the test file (`main_test.go`) to:
-->

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
<!--
Now when we call `:GoTest` a quickfix window will open with two errors.
However if go inside the `TestBar` function and call `:GoTestFunc`, you'll see
that our test passes!  This is really useful if you have a lot of tests that
take time and you only want to run certain tests.
-->

もう1つのテストに関連したコマンドは `:GoTestCompile` です。テストは成功する
必要があるだけではなく、何の問題もなくコンパイルが通らなければなりません。
`:GoTestCompile` はテストファイルをコンパイルし、 `:GoBuild` と全く同じように
エラーがあればクイックフィクスを開きます。しかしこの場合はテストは **実行しません** 。
これは頻繁に編集する多くのテストがある場合に非常に便利です。カレントのテストファイル
の中で `:GoTestCompile` を実行すれば、以下のような出力が見られるはずです。
<!--
The other test-related command is `:GoTestCompile`. Tests not only need to
pass with success, they must compile without any problems.
`:GoTestCompile` compiles your test file, just like `:GoBuild` and opens a
quickfix if there are any errors. This however **doesn't run** the tests. This
is very useful if you have a large test which you're editing a lot. Call
`:GoTestCompile` in the current test file, you should see the following:
-->

```
vim-go: [test] SUCCESS
```

### vimrc の改善 <!-- vimrc improvements -->

* `:GoBuild` のときと同様にキーのコンビネーションで簡単に `:GoTest` を実行する
ようなマッピングを追加できます。以下の設定を `.vimrc` に追加してください。 <!-- As with `:GoBuild` we can add a mapping to easily call `:GoTest` with a key
combination. Add the following to your `.vimrc`:
-->

```vim
autocmd FileType go nmap <leader>t  <Plug>(go-test)
```

これで `<leader>t` で簡単にファイルをテストできます。
<!--
Now you can easily test your files via `<leader>t`
-->

* GoファイルのBuildをさらに簡単にしましょう。最初に、先ほど追加した以下のマッピ
  ングを削除してください。 <!-- Let's make building Go files simpler. First, remove the following mapping we added
  previously: -->

```vim
autocmd FileType go nmap <leader>b  <Plug>(go-build)
```

改善したマッピングをさらに追加していきます。どんなGoファイルに対しても
シームレスにするために、Goのファイルの種別をチェックして `:GoBuild` または
`:GoTestCompile` を実行するような簡単な Vim の関数を作ることができます。
以下のヘルパー関数を `.vimrc` に追加してください。
<!--
We're going to add an improved mapping. To make it seamless for
any Go file we can create a simple Vim function that checks the type of the Go
file, and executes `:GoBuild` or `:GoTestCompile`.  Below is the helper function
you can add to your `.vimrc`:
-->

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
<!--
Now whenever you hit `<leader>b` it'll build either your Go file or it'll
compile your test files seamlessly.
-->

* デフォルトではリーダーショートカットは `\` として定義されています。
私は `,` のほうがより便利だと気づいたので以下の設定 (これを .vimrc の
先頭に入れてください) でリーダーを `,` にマップしています。 <!-- By default the leader shortcut is defined as: `\` I've mapped my leader to
`,` as I find it more useful with the following setting (put this in the
beginning of .vimrc): -->

```vim
let mapleader = ","
```

この設定をすれば、テストもテストでないファイルも `,b` で簡単にビルドできます。
<!--
So with this setting, we can easily build any test and non test files with `,b`.
-->

# カバレッジを見る <!-- Cover it -->

テストの世界により深く飛び込んでみましょう。テストは非常に大切です。
Go はソースコードのカバレッジを表示するとても素晴らしい方法を提供しています。
vim-go はVimを抜けることなくとてもエレガントにコードカバレッジを見るのを
簡単にします。
<!--
Let's dive further into the world of tests. Tests are really important. Go
has a really great way of showing the coverage of your source code. vim-go
makes it easy to see the code coverage without leaving Vim in a very elegant
way.
-->

まず `main_test.go` ファイルを以下のように戻しましょう。
<!--
Let's first change our `main_test.go` file back to:
-->

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
<!--
And `main.go` to
-->


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
<!--
Now let us call `:GoCoverage`. Under the hood this calls `go test -coverprofile
tempfile`. It parses the lines from the profile and then dynamically changes
the syntax of your source code to reflect the coverage. As you see, because we
only have a test for the `Bar()` function, that is the only function that is
green.
-->

ハイライトをクリアするには `:GoCoverageClear` を実行します。テストケースを
追加してカバレッジがどのように変化するか見てみましょう。以下のコードを
`main_test.go` に追加してください。
<!--
To clear the syntax highlighting you can call `:GoCoverageClear`. Let us add a
test case and see how the coverage changes. Add the following to `main_test.go`:
-->

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
<!--
If we call `:GoCoverage` again, you'll see that the `Quz` function is now
tested as well and that it has a larger coverage. Call `:GoCoverageClear` again
to clear the syntax highlighting.
-->

`:GoCoverage` と `:GoCoverageClear` を実行するのははセットで使われることが
多いので実行して結果をクリアするのを簡単にする別のコマンド `:GoCoverageToggle`
が用意されています。これはトグルとして動作し、一度実行するとカバレッジを表示し、
再度実行するとカバレッジをクリアします。これらのコマンドをどのように使うかは
あなたのワークフロー次第です。
<!--
Because calling `:GoCoverage` and `:GoCoverageClear` are used a lot together,
there is another command that makes it easy to call and clear the result. You
can also use `:GoCoverageToggle`. This acts as a toggle and shows the coverage,
and when called again it clears the coverage.  It's up to your workflow how you
want to use them.
-->

最後に、もし vim-go の内蔵ビューが好きでない場合は、 `:GoCoverageBrowser` を
実行することもできます。これは裏で `go tool cover` を実行してHTMLページを
作成しデフォルトブラウザで開きます。人によってはこちらのほうが好みでしょう。
<!--
Finally, if you don't like vim-go's internal view, you can also call
`:GoCoverageBrowser`. Under the hood it uses `go tool cover` to create a HTML
page and then opens it in your default browser. Some people like this more.
-->

`:GoCoverageXXX` 系のコマンドを使ってもどんな種類の中間ファイルも作らず
あなたのワークフローを汚染しません。ですので不要なファイルを毎回消すような
必要はありません。
<!--
Using the `:GoCoverageXXX` commands does not create any kind of temporary files
and doesn't pollute your workflow. So you don't have to deal with removing
unwanted files every time.
-->

### vimrc の改善 <!-- vimrc improvements -->

以下の設定を `.vimrc` に追加してください。
<!--
Add the following to your `.vimrc`:
-->

```vim
autocmd FileType go nmap <Leader>c <Plug>(go-coverage-toggle)
```

この設定を追加すると `<leader>c` で `:GoCoverageToggle` を簡単に実行できます。
<!--
With this you can easily call `:GoCoverageToggle` with `<leader>c`
-->


# 編集する <!-- Edit it -->

### import文 <!-- Imports -->

以下のサンプルの `main.go` から始めましょう。
<!--
Let us start with a sample `main.go` file:
-->

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
<!--
Let's start with something we know already. If we save the file, you'll see that
it'll be formatted automatically. It's enabled by default but can be disabled
if desired (not sure why you would though :)) with `let g:go_fmt_autosave = 0`.
Optionally we also provide `:GoFmt` command, which runs `gofmt` under the hood.
-->

`"gopher"` の文字列を全て大文字で出力してみましょう。そのために `strings`
パッケージを使用します。定義を以下のように変更しましょう。
<!--
Let's print the `"gopher"` string in all uppercase. For it we're going to use
the `strings` package. Change the definition to:
-->

```go
fmt.Println(strings.ToUpper("gopher"))
```

ビルドすると当然エラーが出ます。
<!--
When you build it you'll get an error of course:
-->

```
main.go|8| undefined: strings in strings.ToUpper
```

このエラーが出るのは `strings` パッケージがインポートされていないからです。
vim-go はimport文の宣言を操作するためにいくつかのコマンドを提供しています。
<!--
You'll see we get an error because the `strings` package is not imported. vim-go
has a couple of commands to make it easy to manipulate the import declarations.
-->

移動してファイルを編集することも簡単にできますが、代わりに `:GoImport` という
Vimコマンドを使います。このコマンドは指定したパッケージをimportパスに追加します。
`:GoImport strings` と実行してください。 `strings` パッケージが追加されるのが
見られるでしょう。このコマンドの素晴らしいところは補完をサポートしていることです。
ですので `:GoImport s` とだけ入力してタブを押すことができます。
<!--
We can easily go and edit the file, but instead we're going to use the Vim
command `:GoImport`. This command adds the given package to the import path.
Run it via: `:GoImport strings`. You'll see the `strings` package is being
added.  The great thing about this command is that it also supports
completion. So you can just type `:GoImport s` and hit tab.
-->

importパスを編集するために `:GoImportAs` と `:GoDrop` も用意されています。
`:GoImportAs` は `:GoImport` と同じですが、パッケージ名を変更することが
可能です。例えば `:GoImportAs str strings` は `strings` を `str` という
パッケージ名でインポートします。
<!--
We also have `:GoImportAs` and `:GoDrop` to edit the import paths.
`:GoImportAs` is the same as `:GoImport`, but it allows changing the package
name. For example `:GoImportAs str strings`, will import `strings` with the
package name `str.`
-->

最後に `:GoDrop` はimport宣言からimportパスを簡単に削除できます。
`:GoDrop strings` は `strings` をimport宣言から削除します。
<!--
Finally `:GoDrop` makes it easy to remove any import paths from the import
declarations. `:GoDrop strings` will remove it from the import declarations.
-->

もちろんインポートパスを手動で操作するのはとても 2010 年っぽい (訳注: 時代遅れ)
です。この問題を扱うもっとよいツールを提供しています。もしまだ聞いたことがなければ
それは `goimports` と呼ばれています。 `goimports` は `gofmt` を置き換えるものです。
使用方法は2通りあります。最初の(そして推奨される)使い方はファイルを保存したときに
実行するように vim-go に伝えることです。
<!--
Of course manipulating import paths is so 2010. We have better tools to handle
this case for us. If you haven't heard yet, it's called `goimports`.
`goimports` is a replacement for `gofmt`. You have two ways of using it. The
first (and recommended) way is telling vim-go to use it when saving the
file:
-->

```
let g:go_fmt_command = "goimports"
```

これでファイルを保存するたびに `goimports` が自動的にコードを整形しimport宣言も
書き換えます。コードベースが非常に巨大な場合は遅くなるかもしれないので `goimports`
を好まない人もいます。このために私たちは `:GoImports` コマンド (最後の `s` に
注意) も用意しています。これで明示的に `goimports` を実行することができます。
<!--
Now whenever you save your file, `goimports` will automatically format and also
rewrite your import declarations. Some people do not prefer `goimports` as it
might be slow on very large codebases. In this case we also have the
`:GoImports` command (note the `s` at the end). With this, you can explicitly
call `goimports`
-->


### テキストオブジェクト <!-- Text objects -->

さらなる編集のチップスとトリックをお見せしましょう。関数を変更するのに使える
2つのテキストオブジェクトがあります。 `if` と `af` です。 `if` は関数の内側を
意味し関数の囲いの中身を選択できるようにします。 `main.go` を以下のように
変更してください。
<!--
Let us show more editing tips/tricks. There are two text objects that we can
use to change functions. Those are `if` and `af`. `if` means inner function and
it allows you to select the content of a function enclosure. Change your `main.go` file to:
-->


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
<!--
Put your cursor on the `func` keyword  Now execute the following in `normal`
mode and see what happens:
-->

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
<!--
You'll see that the function body is removed. Because we used the `d` operator.
Undo your changes with `u`. The great thing is that your cursor can be anywhere
starting from the `func` keyword until the closing right brace `}`. It uses the tool
[motion](https://github.com/fatih/motion) under the hood. I wrote motion
explicitly for vim-go to support features like this. It's Go AST aware and thus
its capabilities are really good. Like what you might ask? Change `main.go` to:
-->

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
<!--
Previously we were using regexp-based text objects, which leads to problems.
For example in this example, put your cursor to the anonymous functions' `func`
keyword and execute `dif` in `normal` mode. You'll see that only the body of
the anonymous function is deleted.
-->

ここまでは `d` オペレータ (削除) だけを使ってきました。しかし、これはあなた
次第で、例えば `vif` を使って選択もできますし、 `yif` でヤンク(コピー)も
できます。
<!--
We have only used the `d` operator (delete) so far. However it's up to you. For
example you can select it via `vif` or yank(copy) with `yif`.
-->

さらに `af` もあり、これは `a function` という意味です。このテキストオブジェクト
は関数の宣言全体を含みます。 `main.go` を以下のように変更してください。
<!--
We also have `af`, which means `a function`. This text object includes the
whole function declaration. Change your `main.go` to:
-->


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
<!--
So here is the great thing. Because of `motion` we have full knowledge about
every single syntax node. Put your cursor on top of the `func`  keyword or
anywhere below or above (doesn't matter). If you now execute `vaf`, you'll see
that the function declaration is being selected, along with the doc comment as
well! You can for example delete the whole function with `daf`, and you'll see
that the comment is gone as well. Go ahead and put your cursor on top of the
comment and execute `vif` and then `vaf`. You'll see that it selects the
function body, even though your cursor is outside the function, or it selects
the function comments as well.
-->

これはとても強力です。これは全て
`let g:go_textobj_include_function_doc = 1` という設定で `motion` から得られる
情報のおかげです。コメントが関数宣言の一部として扱われてほしくない場合は
以下の設定で簡単に無効にできます。
<!--
This is really powerful and this all is thanks to the knowledge we have from
`let g:go_textobj_include_function_doc = 1` `motion`. If you don't like comments
being a part of the function declaration, you can easily disable it with:
-->

```vim
let g:go_textobj_include_function_doc = 0
```

`motion` についてもっと知りたければ、より詳細を書いたブログ記事を参照してください。
[Treating Go types as objects in vim](https://medium.com/@farslan/treating-go-types-as-objects-in-vim-ed6b3fad9287#.45q2rtqgf)
<!--
If you are interested in learning more about `motion`, check out the blog post I wrote for
more details: [Treating Go types as objects in vim](https://medium.com/@farslan/treating-go-types-as-objects-in-vim-ed6b3fad9287#.45q2rtqgf)
-->

(追加の任意の質問: `go/ast` パッケージを見ていませんが、ドキュメントコメント
は関数宣言の一部なのかそうでないのかどちらでしょうか?)
<!--
(Optional question: without looking at the `go/ast` package, is the doc comment
a part of the function declaration or not?)
-->

### 構造体リテラルの行分割と結合 <!-- Struct split and join -->
Goの構造体リテラルを行分割と結合してくれる素晴らしいプラグインがあります。
実際はGo用のプラグインではないのですが、Goの構造体もサポートしています。
有効にするには `vimrc` の `plug` 定義の中にプラグインのディレクティブを追加
し vim エディタ内で `:source ~/.vimrc` を実行し、 `:PlugInstall` を実行してください。
例を示します。
<!--
There is a great plugin that allows you to split or join Go structs. It's
actually not a Go plugin, but it has support for Go structs. To enable it add
plugin directive between the `plug` definition into your `vimrc`, then do a `:source ~/.vimrc` in your vim editor and run
`:PlugInstall`. Example:
-->

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'AndrewRadev/splitjoin.vim'
call plug#end()
```

プラグインをインストールしたら `main.go` を以下のように変更してください。
<!--
Once you have installed the plugin, change the `main.go` file to:
-->

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
<!--
Put your cursor on the same line as the struct expression. Now type `gS`. This
will `split` the struct expression into multiple lines. And you can even
reverse it. If your cursor is still on the `foo` variable, execute `gJ` in
`normal` mode. You'll see that the field definitions are all joined.
-->

これはAST (抽象構文木) を理解するツールを使っていないので、例えばフィールドの
先頭で `gJ` を押すと2つのフィールドだけが行結合されるのを見ることになるでしょう。
<!--
This doesn't use any AST-aware tools, so for example if you type `gJ` on top of
the fields, you'll see that only two fields are joined.
-->

### スニペット <!-- Snippets -->

vim-go は2つのポピュラーなスニペットプラグインをサポートしています。
[Ultisnips](https://github.com/SirVer/ultisnips) と
[neosnippet](https://github.com/Shougo/neosnippet.vim) です。デフォルトでは
`Ultisnips` をインストールしていればそのまま動きます。まず `ultisnips` を
インストールしてみましょう。 `vimrc` の `plug` ディレクティブの間に追加して
vim エディタ内で :`source ~/.vimrc` を実行し、`:PlugInstall` を実行しましょう。
例を示します。
<!--
Vim-go supports two popular snippet plugins.
[Ultisnips](https://github.com/SirVer/ultisnips) and
[neosnippet](https://github.com/Shougo/neosnippet.vim). By default,
if you have `Ultisnips` installed it'll work.  Let us install `ultisnips`
first. Add it between the `plug` directives in your `vimrc`, then do a `:source ~/.vimrc` in your vim editor and then run
`:PlugInstall`. Example:
-->

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'SirVer/ultisnips'
call plug#end()
```

多くの有用なスニペットがあります。私たちの最新のスニペットの完全な一覧を見るには
https://github.com/fatih/vim-go/blob/master/gosnippets/UltiSnips/go.snippets
を参照してください。
<!--
There are many helpful snippets. To see the full list check our current
snippets:
https://github.com/fatih/vim-go/blob/master/gosnippets/UltiSnips/go.snippets
-->

[UltiSnips と YouCompleteMe は [tab] ボタンのバインディングで衝突するかもしれません](https://stackoverflow.com/questions/14896327/ultisnips-and-youcompleteme)
<!--
[UltiSnips and YouCompleteMe may conflict on [tab] button](https://stackoverflow.com/questions/14896327/ultisnips-and-youcompleteme)
-->

私は最も使っているいくつかのスニペットを紹介しましょう。 `main.go` の内容を
以下のように変更してください。
<!--
Let me show some of the snippets that I'm using the most. Change your `main.go`
content to:
-->

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
<!--
Let's put our cursor just after the `newFoo()` expression. Let's panic here if
the err is non-nil. Type `errp` in insert mode and just hit `tab`. You'll see
that it'll be expanded and put your cursor inside the `panic()`` function:
-->

```
if err != nil {
    panic( )
          ^
          カーソル位置
}
```
<!--
```
if err != nil {
    panic( )
          ^
          cursor position
}
```
-->

panicの中身に `err` と入力して `json.Marshal` の行にカーソルを移動してください。
同様にコードを追加しましょう。
<!--
Fill the panic with `err` and move on to the `json.Marshal` statement. Do the
same for it.
-->

今度は `out` 変数を出力してみましょう。変数を出力するのはとても頻繁に行われる
ので、いくつかのスニペットが用意されています。
<!--
Now let us print the variable `out`. Because variable printing is so popular,
we have several snippets for it:
-->

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
<!--
Here `ff` and `lf` are special. They dynamically copy the variable name into
the format string as well. Try it yourself. Move your cursor to the end of the
main function and type `ff` and hit tab. After expanding the snippet you can
start typing. Type `string(out)` and you'll see that both the format string and
the variadic arguments will be filled with the same string you have typed.
-->

これはデバッグ用に変数を出力するのを素早く行うのに非常に便利です。
`:GoRun` で実行すると以下のような出力が見られるはずです。
<!--
This comes very handy to quickly print variables for debugging.
Run your file with `:GoRun` and you should see the following output:
-->

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
<!--
Great. Now let me show one last snippet that I think is very useful. As you see
from the output the fields `Message` and `Ports` begin with uppercase
characters. To fix it we can add a json tag to the struct field. vim-go makes it
very easy to add field tags. Move your cursor to the end of the `Message`
string line in the field:
-->

```
type foo struct {
    Message string .
                   ^ ここにカーソルを置いてください。
}
```
<!--
```
type foo struct {
    Message string .
                   ^ put your cursor here
}
```
-->

インサートモードで `json` とタイプしタブを押します。自動的に有効なフィールド
タグに展開されるのが見られるはずです。フィールド名は自動的に小文字に変換されて
入力されます。以下のような状態になっているはずです。
(訳注: タブを1回押した時点では `message` が選択された状態になっていてそこから
タイプすると違う文字列に変更することもできます。逆に `message` のまま確定するには
もう一度タブを押せばOKです)。
<!--
In `insert` mode, type `json` and hit tab. You'll see that it'll be
automatically expanded to valid field tag. The field name is converted
automatically to a lowercase and put there for you. You should now see the
following:
-->

```
type foo struct {
	Message  string `json:"message"`
}
```

とても素晴らしいです。でももっと良くできます！さらに進んで `ServerName` フィールド
のスニペット展開をしてみましょう。今度は `server_name` と変換されます。
素晴らしいでしょう？
<!--
It's really amazing. But we can do even better! Go ahead and create a
snippet expansion for the `ServerName` field. You'll see that it's converted to
`server_name`. Amazing right?
-->

```go
type foo struct {
	Message    string `json:"message"`
	Ports      []int
	ServerName string `json:"server_name"`
}
```

### vimrc の改善 <!-- vimrc improvements -->

* 忘れずに `gofmt` を `goimports` に変更しましょう。 <!-- Don't forget to change `gofmt` to `goimports` -->

```vim
let g:go_fmt_command = "goimports"
```

* ファイルを保存すると、 `gofmt` がそのファイルをパーズしたときにエラーがあれば
  それを表示します。パーズエラーがあればクイックフィクスリスト内に表示します。
  これはデフォルトで有効になっています。これが好きでない人もいます。無効にするには
  以下の設定を追加してください。 <!-- When you save your file, `gofmt` shows any errors during parsing the file. If
  there are any parse errors it'll show them inside a quickfix list. This is
  enabled by default. Some people don't like it. To disable it add: -->

```vim
let g:go_fmt_fail_silently = 1
```

* 大文字小文字がどのように変換されるかを変更することができます。デフォルトでは
  vim-go は `snake_case` を使います。でも希望すれば `camelCase` を使うことも
  できます。例えばデフォルト値をキャメルケースに変更したい場合は以下の設定が
  使えます。 <!-- You can change which case it should apply while converting. By default vim-go
  uses `snake_case`. But you can also use `camelCase` if you wish. For example
  if you wish to change the default value to camel case use the following
  setting: -->

```vim
let g:go_addtags_transform = "camelcase"
```

# 美しく表示する <!-- Beautify it -->

デフォルトでは限定されたシンタクスハイライトのみが有効になっています。
これには2つの大きな理由があります。1つめは人々は色が多いと気が散るので
色が多いのが好きではないことです。2つめの理由はVimのパフォーマンスに
大きく影響を与えることです。有効にするには明示的に設定する必要があります。
まず `.vimrc` に以下の設定を追加してください。
<!--
By default we only have a limited syntax highlighting enabled. There are two
main reasons. First is that people don't like too much color because it causes
too much distraction. The second reason is that it impacts
the performance of Vim a lot. We need to enable it explicitly. First add the
following settings to your `.vimrc`:
-->

```vim
let g:go_highlight_types = 1
```

これで以下のコードの `bar` と `foo` がハイライトされます。
<!--
This highlights the `bar` and `foo` below:
-->

```
type foo struct{
  quz string
}

type bar interface{}
```

さらに以下の設定を追加します。
<!--
Adding the following:
-->

```vim
let g:go_highlight_fields = 1
```

すると以下のコードの `quz` がハイライトされます。
<!--
Will highlight the `quz` below:
-->

```go
type foo struct{
  quz string
}


f := foo{quz:"QUZ"}
f.quz # ここの quz がハイライトされます
```
<!--
```go
type foo struct{
  quz string
}


f := foo{quz:"QUZ"}
f.quz # quz here will be highlighted
```
-->

以下の設定を追加すると
<!--
If we add the following:
-->

```vim
let g:go_highlight_functions = 1
```

今度は宣言内の関数名とメソッド名もハイライトされます。 `Foo` と `main` が新たに
ハイライトされますが、`Println` は呼び出しなのでハイライトされません。
<!--
We are now also highlighting function and method names in declarations. `Foo`
and `main` will now be highlighted, but `Println` is not as that is an
invocation:
-->

```go
func (t *T) Foo() {}

func main() {
  fmt.Println("vim-go")
}
```

関数とメソッドの呼び出しもハイライトしたい場合は、以下の設定を追加してください。
<!--
If you also want to highlight function and method invocations, add the
following:
-->

```vim
let g:go_highlight_function_calls = 1
```

すると `Println` もハイライトされます。
<!--
Now, `Println` will also be highlighted:
-->

```go
func (t *T) Foo() {}

func main() {
  fmt.Println("vim-go")
}
```

`let g:go_highlight_operators = 1` を追加すると以下の演算子がハイライト
されます。
<!--
If you add `let g:go_highlight_operators = 1` it will highlight the following
operators such as:
-->

```
- + % < > ! & | ^ * =
-= += %= <= >= != &= |= ^= *= ==
<< >> &^
<<= >>= &^=
:= && || <- ++ --
```

`let g:go_highlight_extra_types = 1` を追加すると以下のような型もハイライト
されます。
<!--
If you add `let g:go_highlight_extra_types = 1` the following extra types
will be highlighted as well:
-->

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
<!--
Let's move on to more useful highlights. What about build tags? It's not easy
to implement it without looking into the `go/build` document. Let us first add
the following: `let g:go_highlight_build_constraints = 1` and change your
`main.go` file to:
-->

```
// build linux
package main
```

`// build linux` の行がグレーになります。有効ではないことを示しています。
`build` の前に `+` を追加して再度保存します。
<!--
You'll see that it's gray, thus it's not valid. Prepend `+` to the `build` word and save it again:
-->

```
// +build linux
package main
```

(訳注: まだグレーです。)
なぜだかわかりますか？ `go/build` パッケージのドキュメントをよく読むと
以下のように書かれているのに気づくでしょう。
<!--
Do you know why? If you read the `go/build` package you'll see that the
following is buried in the document:
-->

> ... 他の行コメントの前に空行のみを入れる必要があります。
<!--
> ... preceded only by blank lines and other line comments.
-->

再度変更して保存してみましょう。
<!--
Let us change our content again and save it to:
-->

```
// +build linux

package main
```

有効なことを示す色で自動的にハイライトされるのを見るでしょう。これはとても
素晴らしいです。 `linux` を他の何かに変更して有効な公式のタグになっているか
チェックしてみましょう (`darwin` 、 `race` 、 `ignore` などがあります)。
<!--
You'll see that it automatically highlighted it in a valid way. It's really
great. If you go and change `linux` to something you'll see that it also checks
for valid official tags (such as `darwin`,`race`, `ignore`, etc... )
-->


別の似たような機能として `//go:generate` のGoディレクティブをハイライトする
機能があります。 .vimrcに `let g:go_highlight_generate_tags = 1` を追加すると
`go generate` コマンドで処理される有効なディレクティブがハイライトされます。
<!--
Another similar feature is to highlight the Go directive `//go:generate`. If
you put `let g:go_highlight_generate_tags = 1` into your vimrc, it'll highlight
a valid directive that is processed with the `go generate` command.
-->

ハイライトの設定はもっとたくさんあって、これらはほんの一部です。より多くの
設定について知るには `:help go-settings` を参照してください。
<!--
We have a lot more highlight settings, these are just a sneak peek of it. For
more check out the settings via `:help go-settings`
-->


### vimrc の改善 <!-- vimrc improvements -->

* 一部の人はタブの表示され方が好きではありません。デフォルトではVimは1つのタブ
  を `8` つのスペースとして表示します。しかしVimでどのように表示されるかは
  私たち次第です。以下のように設定すれば1つのタブが4つのスペースとして表示
  されます。 <!-- Some people don't like how the tabs are shown. By default Vim shows `8`
  spaces for a single tab. However it's up to us how to represent in Vim. The
  following will change it to show a single tab as 4 spaces: -->

```
autocmd BufNewFile,BufRead *.go setlocal noexpandtab tabstop=4 shiftwidth=4
```

この設定はタブをスペースに展開はしません。1つのタブを `4` つのスペースとして
表示するだけです。1段のインデントを表すのに `4` つのスペースを使います。
(訳注: この設定の場合見た目は `4` つのスペースですが入力されるのは1つのタブです)。
<!--
This setting will not expand a tab into spaces. It'll show a single tab as `4`
spaces. It will use `4` spaces to represent a single indent.
-->

* 多くの人が私のカラースキームについて質問します。私は `molokai` を少し
修正したものを使っています。これを有効にするにはプラグイン定義の間に以下の
Plug ディレクティブを追加してください。 <!-- A lot of people ask for my colorscheme. I'm using a slightly modified
`molokai`. To enable it add the Plug directive just between the plug
definitions: -->

```vim
call plug#begin()
Plug 'fatih/vim-go'
Plug 'fatih/molokai'
call plug#end()
```

さらに以下の設定を追加するとオリジナルのカラースキームと256色バージョンの
molokai を有効にできます。
<!--
Also add the following to enable molokai with original color scheme and 256
color version:
-->

```vim
let g:rehash256 = 1
let g:molokai_original = 1
colorscheme molokai
```

設定を追加したらVimを再起動して `:source ~/.vimrc` を実行し `:PlugInstall` を
実行してください。これでプラグインがダウンロードされインストールされます。
プラグインがインストールされたら再びVimを再起動してください。
<!--
After that restart Vim and call `:source ~/.vimrc`, then `:PlugInstall`. This will pull the plugin
and install it for you. After the plugin is installed, you need to restart Vim
again.
-->

# チェックする <!-- Check it -->

これまでの例で多くのコマンドが何か問題があったらクイックフィクスウィンドウを
表示するのを見てきました。例えば `:GoBuild` は (もしあれば) コンパイル出力
からエラーを表示します。あるいは例えば `:GoFmt` はカレントファイルを整形する
際にパーズエラーがあればそれを表示します。
<!--
From the previous examples you saw that we had many commands that would show
the quickfix window when there was an issue. For example `:GoBuild` shows
errors from the compile output (if any). Or for example `:GoFmt` shows the
parse errors of the current file while formatting it.
-->

私たちには他にも実行してその後エラー、警告、提案を集める多くのコマンドがあります。
<!--
We have many other commands that allows us to call and then collect errors,
warnings or suggestions.
-->

例えば `:GoLint` です。裏では `golint` というGoのコードをより慣用的にする
変更を提案してくれるコマンドを実行します。また `:GoVet` もあり、こちらは
裏で `go vet` を実行します。他にもさまざまなことをチェックするツールが多数
あります。より簡単にするために、これらのチェックツールを全部まとめて呼び出す
ツールを作った人がいます。このツールは `gometalinter` と呼ばれています。
そして vim-go は `:GoMetaLinter` コマンドでこれをサポートしています。
これは何をしてくれるのでしょう？
<!--
For example `:GoLint`. Under the hood it calls `golint`, which is a command
that suggests changes to make Go code more idiomatic. There
is also `:GoVet`, which calls `go vet` under the hood. There are many other
tools that check certain things. To make it easier, someone decided to
create a tool that calls all these checkers. This tool is called
`gometalinter`. And vim-go supports it via the command `:GoMetaLinter`. So what
does it do?
-->

あるGoのソースコードに対して `:GoMetaLinter` を単に実行すると、デフォルトでは
`go vet` 、 `golint` と `errcheck` を同時に実行します。 `gometalinter` は全ての
出力を集めて共通のフォーマットに標準化します。このため、 `:GoMetaLinter` を
実行すると vim-go はこれら全てのチェッカーの結果をクイックフィクスリスト内に
表示します。 lint 、 vet と errcheck の結果の間を簡単にジャンプできます。
このデフォルトの設定は以下のとおりです。
<!--
If you just call `:GoMetaLinter` for a given Go source code. By default it'll run
`go vet`, `golint` and `errcheck` concurrently. `gometalinter` collects
all the outputs and normalizes it to a common format. Thus if you call
`:GoMetaLinter`, vim-go shows the result of all these checkers inside a
quickfix list. You can then jump easily between the lint, vet and errcheck
results. The setting for this default is as following:
-->


```vim
let g:go_metalinter_enabled = ['vet', 'golint', 'errcheck']
```

他にも多くのツールが有りこれらのリストは簡単にカスタマイズできます。
もし `:GoMetaLinter` を実行すると上記のリストを自動的に使います。
<!--
There are many other tools and you can easily customize this list yourself. If
you call `:GoMetaLinter` it'll automatically uses the list above.
-->

`:GoMetaLinter` は普通は速いので vim-go はファイルを保存したらいつでも
(ちょうど `:GoFmt` のように) それを実行します。これを有効にするには
`.vimrc` に以下の設定を追加します。
<!--
Because `:GoMetaLinter` is usually fast, vim-go also can call it whenever you
save a file (just like `:GoFmt`). To enable it you need to add the following to
your `.vimrc:`
-->

```vim
let g:go_metalinter_autosave = 1
```

素晴らしいことに自動保存の際に実行するチェッカーは `:GoMetaLinter` コマンドを
実行したときと違うものにできます。ファイルを保存したときには速いチェッカーだけを
実行し、 `:GoMetaLinter` を実行したときには他のチェッカーも実行するように
カスタマイズ可能となるのでこれは素晴らしいです。以下の設定で自動保存機能に対して
実行されるチェッカーをカスタマイズできます。
<!--
What's great is that the checkers for the autosave is different than what you
would use for `:GoMetaLinter`.  This is great because you can customize it so only
fast checkers are called when you save your file, but others if you call
`:GoMetaLinter`. The following setting let you customize the checkers for the
`autosave` feature.
-->


```vim
let g:go_metalinter_autosave_enabled = ['vet', 'golint']
```

このようにデフォルトでは `vet` と `golint` が有効になっています。最後に
`:GoMetaLinter` の実行時間が長くなりすぎないように、指定したタイムアウトを
超えたら実行をキャンセルするための設定もあります。デフォルトでは `5秒` ですが
以下の設定で変更可能です。
<!--
As you see by default `vet` and `golint` are enabled. Lastly, to prevent
`:GoMetaLinter` running for too long, we have a setting to cancel it after a
given timeout. By default it is `5 seconds` but can be changed by the following
setting:
-->

```vim
let g:go_metalinter_deadline = "5s"
```

# ナビゲートする <!-- Navigate it -->

これまで私たちは `main.go` と `main_test.go` の2つのファイル間だけでジャンプ
していました。同じディレクトリに2つのファイルしかない場合は切り替えはとても
簡単です。でもプロジェクトが時間とともにどんどん大きくなってきたらどうでしょうか？
あるいはファイル自体がとても巨大になりナビゲートするのが大変になってきたらどうでしょう？
<!--
So far we have only jumped between two files, `main.go` and `main_test.go`. It's
really easy to switch if you have just two files in the same directory. But
what if the project gets larger and larger with time? Or what if the file
itself is so large that you have hard time navigating it?
-->


### 代替ファイル <!-- Alternate files -->

vim-go はナビゲーションを改善するいくつかの方法を提供しています。まず
Goのソースファイルとそのテストファイルの間を素早くジャンプする方法を
紹介します。
<!--
vim-go has several ways of improving navigation. First let me
show how we can quickly jump between a Go source code and its test file.
-->

`foo.go` とそのテストファイル `foo_test.go` の両方があるとしましょう。
これまでの例の `main.go` があればそれを開いてください。開いたら
以下の Vim コマンドを実行してください。
<!--
Suppose you have both a `foo.go` and its equivalent test file `foo_test.go`.
If you have `main.go` from the previous examples with its test file you can
also open it. Once you open it just execute the following Vim command:
-->

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
<!--
You'll see that you switched immediately to `main_test.go`. If you execute it
again, it'll switch to `main.go`. `:GoAlternate` works as a toggle and is
really useful if you have a package with many test files.  The idea is very
similar to the plugin [a.vim](https://github.com/vim-scripts/a.vim) command
names. This plugin jumps between a `.c` and `.h` file. In our case
`:GoAlternate` is used to switch between a test and non-test file.
-->

### 定義へジャンプ <!-- Go to definition -->

最も使われる機能の一つが `定義へジャンプ` です。 vim-go は当初から
`:GoDef` コマンドを持っており、あらゆる識別子の宣言箇所にジャンプする
ことができます。まず `main.go` を作って、実際に動く様子を見てみましょう。
以下の内容で作成してください。
<!--
One of the most used features is `go to definition`. From the beginning, vim-go
had the `:GoDef` command that jumps to any identifier's declaration. Let
us first create a `main.go` file to show it in action. Create it with the
following content:
-->

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
<!--
Now we have here several ways of jumping to declarations. For example if you put
your cursor on top of `T` expression just after the main function and call
`:GoDef` it'll jump to the type declaration.
-->

main関数のすぐ後の `t` 変数の宣言の先頭にカーソルを置いて `:GoDef` を
実行すると何も起きません。これはジャンブする先がない (訳注: すでに宣言の
場所にいる) からです。でも数行下に移動して `fmt.Printf()` の中で使われている
`t` 変数にカーソルを移動して `:GoDef` を実行すると、変数の宣言箇所にジャンプ
するのが見られるでしょう。
<!--
If you put your cursor on top of the `t` variable declaration just after the
main function and call `:GoDef`, you'll see that nothing will happen. Because
there is no place to go, but if you scroll down a few lines and put your cursor
to the `t` variable used in `fmt.Printf()` and call `:GoDef`, you'll see that
it jumped to the variable declaration.
-->

`:GoDef` はローカルスコープで機能するだけではなくグローバルでも (`GOPATH`
の範囲で) 機能します。例えば、 `Printf()` 関数の先頭にカーソルを置いて
`:GoDef` を実行すると `fmt` パッケージに直接ジャンプします。
これをとても頻繁に使用されるので vim-go はVimにビルトインのショートカット
`gd` と `ctrl-]` の設定を上書きします。ですので `:GoDef` を実行する代わりに
`gd` か `ctrl-]` を簡単に使用できます。
<!--
`:GoDef` not only works for local scope, it works also globally
(across`GOPATH`).  If, for example, you put your cursor on top of the `Printf()`
function and call `:GoDef`, it'll jump directly to the `fmt` package.  Because
this is used so frequently, vim-go overrides the built in Vim shortcuts `gd`
and `ctrl-]` as well. So instead of `:GoDef` you can easily use `gd` or
`ctrl-]`
-->

一旦宣言にジャンプしたら、元の場所に戻りたいでしょう。デフォルトでは元の
カーソル位置にジャンプする `ctrl-o` という Vim のショートカットがあります。
これはきちんと機能するときは素晴らしいのですが、Goの宣言の間をナビゲート
するときはあまり良くありません。例えば `:GoDef` でジャンプした後ファイルの
末尾までスクロールし、また先頭に移動したりすると `ctrl-o` はそれらの位置も
覚えてしまいます。ですので `:GoDef` を実行したときの元の位置に戻りたい場合
`ctrl-o` を複数回押す必要があります。これは本当にうっとうしいです。
<!--
Once we jump to a declaration, we also might want to get back into our previous
location. By default there is the Vim shortcut  `ctrl-o` that jumps to the
previous cursor location. It works great when it does, but not good enough if
you're navigating between Go declarations. If, for example, you jump to a file
with `:GoDef` and then scroll down to the bottom, and then maybe to the top,
`ctrl-o` will remember these locations as well. So if you want to jump back to
the previous location when invoking `:GoDef`, you have to hit `ctrl-o` multiple
times. And this is really annoying.
-->

でもこのショートカットを使う必要はありません。というのもvim-goはもっとよい
実装を提供しているからです。まさにこれを実現する `:GoDefPop` というコマンド
があります。vim-go は `:GoDef` で訪れた全ての場所を管理する内部のスタックリスト
を維持しています。これは `:GoDefPop` で元いた場所に簡単に戻れることを意味し、
ファイル内で上下にスクロールしても機能します。そしてこれも非常に何度も使われる
ため、私たちは `ctrl-t` というショートカットを用意しています。これは裏では
`:GoDefPop` を実行します。ということで要約すると以下のようになります。
<!--
We don't need to use this shortcut though, as vim-go has a better implementation
for you. There is a command `:GoDefPop` which does exactly this. vim-go
keeps an internal stack list for all the locations you visit with `:GoDef`.
This means you can jump back easily again via `:GoDefPop` to your older
locations, and it works even if you scroll down/up in a file. And because this
is also used so many times we have the shortcut `ctrl-t` which calls under the
hood `:GoDefPop`. So to recap:
-->

* ローカルまたはグローバルに定義にジャンプするには `ctrl-]` か `gd` を使います。 <!-- Use `ctrl-]` or `gd` to jump to a definition, locally or globally -->
* 元の場所に戻るには `ctrl-t` を使います。 <!-- Use `ctrl-t` to jump back to the previous location  -->

別の疑問に移りましょう。ここまでジャンプしてきて最初の地点に戻りたいとしましょう。
上に書いたように vim-go は `:GoDef` を実行して移動したすべての場所の履歴を
維持しています。これら全てを表示するコマンド `:GoDefStack` があります。
これを実行するとカスタムウィンドウが開き、これまで移動した場所の一覧が
表示されます。あとは希望する場所の行に移動してエンターキーを押すだけです。
そして最後にスタックリストをクリアしたい場合は `:GoDefStackClear` を実行します。
<!--
Let us move on with another question, suppose you jump so far that you just
want to back to where you started? As mentioned earlier,
vim-go keeps an history of all your locations invoked via `:GoDef`. There is a
command that shows all these and it's called `:GoDefStack`. If you call it,
you'll see that a custom window with a list of your old locations will be
shown. Just navigate to your desired location and hit enter.  And finally to
clear the stack list anytime call `:GoDefStackClear`.
-->


### 関数の間を移動する <!-- Move between functions -->

この前の例でジャンプしたい場所がわかっている場合は `:GoDef` が便利でした。
でも次の目的地が何かわかっていない場合はどうでしょうか？あるいは関数の名前
の一部しか知らない場合はどうでしょうか？
<!--
From the previous example we see that `:GoDef` is nice if you know where you want to
jump. But what if you don't know what your next destination is? Or you just
partially know the name of a function?
-->

`編集する` の項で私は `motion` と呼ばれるツールに言及しました。それは
vim-go のためだけに作られたカスタムツールです。 `motion` は別の能力も
持っており、あなたの GO パッケージをパーズして全ての宣言をしっかり理解
しています。私たちはこの機能の利点を活かして宣言の間をジャンプできます。
2つのコマンドがありますが、特定のプラグインをインストールしないと利用可能に
なりません。コマンドは以下の2つです。
<!--
In our `Edit it` section I mentioned a tool called `motion`, which is a
custom built tool just for vim-go. `motion` has other capabilities as well.
`motion` parses your Go package and thus has a great understanding of all
declarations. We can take advantage of this feature for jumping between
declarations. There are two commands, which are not available until you install
a certain plugin. The commands are:
-->

```
:GoDecls
:GoDeclsDir
```

まず必要なプラグインをインストールしてこれら2つのコマンドを有効にしましょう。
プラグインは [ctrlp](https://github.com/ctrlpvim/ctrlp.vim) と呼ばれています。
長い間 Vim を使っているユーザなら既にインストールしていることでしょう。
インストールするには `plug` ディレクティブに以下の設定を追加して
vim エディタ内で `:source ~/.vimrc` を実行し、 `:PlugInstall` コマンドを実行します。
<!--
First let us enable these two commands by installing the necessary plugin. The
plugin is called [ctrlp](https://github.com/ctrlpvim/ctrlp.vim). Long-time Vim
users have it installed already. To install it add the following line between
your `plug` directives, then do a `:source ~/.vimrc` in your vim editor and call `:PlugInstall` to install it:
-->

```vim
Plug 'ctrlpvim/ctrlp.vim'
```

インストールされたら、 `main.go` の中身を以下のように変更してください。
<!--
Once you have it installed, use the following `main.go` content:
-->

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
<!--
And a `main_test.go` file with the following content:
-->

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
<!--
Open `main.go` and call `:GoDecls`. You'll see that `:GoDecls` shows all type
and function declarations for you. If you type `ma` you'll see that `ctrlp`
filters the list for you. If you hit `enter` it will automatically jump to it.
The fuzzy search capabilities combined with `motion`'s AST capabilities brings
us a very simple to use but powerful feature.
-->

例えば `:GoDecls` を実行して `foo` と入力します。すると `BarFoo` が絞り込み
されます。 Go のパーザは非常に速く何百という宣言を持つ巨大なファイルでも
非常によく機能します。
<!--
For example, call `:GoDecls` and write `foo`. You'll see that it'll filter
`BarFoo` for you. The Go parser is very fast and works very well with large files
with hundreds of declarations.
-->

ときにはカレントファイル内で検索するだけでは十分ではないこともあります。
Go のパッケージは (テストのように) 複数のファイルを持ちます。
1つの型宣言は1つのファイルにあるかもしれませんが、ある別の1組の機能に
関わるある関数は別のファイルにあるかもしれません。こういうときは
`:GoDeclsDir` が便利です。あるファイルのディレクトリ全体をパーズして
そのディレクトリ (サブディレクトリは除く) のファイルの全ての宣言を
一覧表示します。
<!--
Sometimes just searching within the current file is not enough. A Go package can
have multiple files (such as tests). A type declaration can be in one file,
whereas a some functions specific to a certain set of features can be in
another file. This is where `:GoDeclsDir` is useful. It parses the whole
directory for the given file and lists all the declarations from the files in the
given directory (but not subdirectories).
-->

`:GoDeclsDir` を実行してみてください。今度は `main_test.go` ファイルに
含まれる定義も一覧に追加されるのが見られるでしょう。 `Bar` とタイプすると
`Bar` と `TestBar` 関数の両方が表示されます。全ての型と関数の宣言の
概要を知りたいだけ、あるいはそれらにジャンプしたいときも、これは非常に
素晴らしいです。
<!--
Call `:GoDeclsDir`. You'll see this time it also included the declarations from
the `main_test.go` file as well. If you type `Bar`, you'll see both the `Bar`
and `TestBar` functions. This is really great if you just want to get an
overview of all type and function declarations, and also jump to them.
-->

質問を続けましょう。次の関数または前の関数に移動したいときはどうすれば
良いでしょう？今カーソルがある関数のボディが長い場合はおそらく関数名が
見えないでしょう。あるいは今いる関数と別の関数の間に別の宣言があるかも
しれません。
<!--
Let's continue with a question. What if you just want to move to the next or
previous function? If your current function body is long, you'll probably will
not see the function names. Or maybe there are other declarations between the
current and other functions.
-->

Vim には既に次の単語に移動する `w` や前の単語に移動する `b` という
移動操作を提供しています。でもGoの抽象構文木に対して移動できるとしたら
どうでしょう？例えば関数の宣言とか。
<!--
Vim already has motion operators like `w` for words or `b` for backwards words.
But what if we could add motions for Go ast? For example for function declarations?
-->

vim-go では関数の間を移動する2つのモーションオブジェクトを (上書きして)
提供しています。以下の2つです。
<!--
vim-go provides(overrides) two motion objects to move between functions. These
are:
-->


```
]] -> jump to next function
[[ -> jump to previous function
```

Vim はこれらのショートカットをデフォルトで持っています。でもこれらは C の
ソースコードに適していて波括弧の間をジャンプするものです。私たちは
もっとうまくやれます。私たちのこれまでの例のようにこの操作のために裏で
`motion` が使われています。
<!--
Vim has these shortcuts by default. But those are suited for C source code and
jumps between braces. We can do it better. Just like our previous example,
`motion` is used under the hood for this operation
-->

`main.go` を開いてファイルの先頭に移動してください。ノーマルモードで `]]` と
タイプして何が起きるか見てみましょう。 `main()` 関数にジャンプするのが
見られるでしょう。もう一度 `]]` を押すと `Bar()` に移動します。
`[[` を押すと `main()` 関数に戻ります。
<!--
Open `main.go` and move to the top of the file. In `normal` mode, type `]]` and
see what happens. You'll see that you jumped to the `main()` function. Another
`]]` will jump to `Bar()` If you hit `[[` it'll jump back to the `main()`
function.
-->

`]]` と `[[` はカウントも受け付けます。例えば、再びファイルの先頭に移動して
`3]]` と実行するとソースファイル内で3番めの関数にジャンプします。
そしてさらに、これらは有効なモーションなので、操作とも組み合わせられます！
<!--
`]]` and `[[` also accepts `counts`. For example if you move to the top again
and hit `3]]` you'll see that it'll jump to the third function in the source file.
And going forward, because these are valid motions, you can apply operators to
it as well!
-->

ファイルの先頭に移動して `d]]` と押すと次の関数の前の全てが削除されます。
例えば1つの便利な使い方としては `v]]` と押して次に `]]` を押すと次の関数も
選択されます。同様にして必要な関数を選択に追加していくことができます。
<!--
If you move your file to the top  and hit `d]]` you'll see that it deleted
anything before the next function. For example one useful usage would be typing
`v]]` and then hit `]]` again to select the next function, until you've done
with your selection.
-->


### .vimrc の改善 <!-- .vimrc improvements -->

* 代替ファイルをどのように開くかを改善することができます。以下の設定を `.vimrc` に
  追加してください。 <!-- We can improve it to control how it opens the alternate file. Add the
  following to your `.vimrc`: -->


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
<!--
This will add new commands, called `:A`, `:AV`, `:AS` and `:AT`. Here `:A`
works just like `:GoAlternate`, it replaces the current buffer with the
alternate file. `:AV` will open a new vertical split with the alternate file.
`:AS` will open the alternate file in a new split view and `:AT` in a new tab.
These commands are very productive depending on how you use them, so I think
it's useful to have them.
-->

* 定義にジャンプするコマンドファミリーは非常に強力でいて使うのは簡単です。
裏ではデフォルトでは `guru` (以前は `oracle` と呼ばれていました) というツールを
使っています。 `guru` はその高度な推測能力において優れた実績を誇ります。ドットインポートに対しても機能しますし、
ベンダライズされたインポートや明白ではない他の多くの識別子についても機能します。
以前は vim-go は `godef` を使っていて、これは問い合わせに応答するのが非常に
速かったです。最新のリリースでは `:GoDef` に使うコマンドを切り替えることが
簡単にできます。 `godef` を使うように戻すには以下の設定を追加してください。 <!-- The "go to definition" command families are very powerful but yet easy to use.
Under the hood it uses by default the tool `guru` (formerly `oracle`). `guru` has
an excellent track record of being very predictable. It works for dot imports,
vendorized imports and many other non-obvious identifiers. But sometimes it's
very slow for certain queries. Previously vim-go was using `godef` which is
very fast on resolving queries. With the latest release one can easily use or
switch the underlying tool for `:GoDef`.  To change it back to `godef` use the
following setting: -->

```vim
let g:go_def_mode = 'godef'
```

* 現状はデフォルトでは `:GoDecls` と `:GoDeclsDir` は型と関数の宣言を表示します。
  これは `g:go_decls_includes` の設定でカスタマイズ可能です。
  デフォルトでは以下の設定になっています。 <!-- Currently by default `:GoDecls` and `:GoDeclsDir` show type and function
  declarations. This is customizable with the `g:go_decls_includes` setting. By
  default it's in the form of: -->

```
let g:go_decls_includes = "func,type"
```

もし関数の宣言だけを表示したいときは、以下のように変更してください。
<!--
If you just want to show function declarations, change it to:
-->

```
let g:go_decls_includes = "func"
```

# 理解する <!-- Understand it -->

コードを書き、編集し、変更するのはまずコードが何をしているか理解してからで
ないと通常はできません。 vim-go はコードがいったいどういうことをしているのは
理解するのを容易にする方法をいくつか提供しています。
<!--
Writing/editing/changing code is usually something we can do only if we first
understand what the code is doing. vim-go has several ways to make it easy to
understand what your code is all about.
-->

### ドキュメント検索 <!-- Documentation lookup -->

基本から始めましょう。 Go のドキュメントは非常に良く書けていて Go の抽象構文木
にも高度に統合されています。あるコメントを書くだけでパーザーが簡単にそれを
パーズし、抽象構文木のノードに関連付けることができます。抽象構文木からの
ノードがあればドキュメント (もしあれば) を簡単に読むことができます！
<!--
Let's start with the basics. Go documentation is very well-written and is
highly integrated into the Go AST as well. If you just write some comments, the
parser can easily parse it and associate with any node in the AST. So what it
means is that we can easily find the documentation in the reverse order. If
you have the node from an AST, you can easily read the documentation (if you
have it)!
-->

私たちは `:GoDoc` というコマンドを用意していてそれはカーソルの下の識別子に
関連付けられたドキュメントを表示してくれます。 `main.go` の内容を以下のように
変更しましょう。
<!--
We have a command called `:GoDoc` that shows any documentation associated with
the identifier under your cursor. Let us change the content of `main.go` to:
-->

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
<!--
Put your cursor on top of the `Println` function just after the `main` function
and call `:GoDoc`. You'll see that it vim-go automatically opens a scratch
window that shows the documentation for you:
-->

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
<!--
It shows the import path, the function signature and then finally the doc
comment of the identifier. Initially vim-go was using plain `go doc`, but it
has some shortcomings, such as not resolving based on a byte identifier. `go
doc` is great for terminal usages, but it's hard to integrate into editors.
Fortunately we have a very useful tool called `gogetdoc`, which resolves and
retrieves the AST node for the underlying node and outputs the associated doc
comment.
-->

このため `:GoDoc` はどんな種類の識別子に対しても機能します。カーソルを
`sayHi()` に置いて `:GoDoc` を実行してもドキュメンテーションコメントが
表示されるでしょう。そして `sayYoo()` にカーソルを置いて実行すると
単に `no documentation` と表示されます。この抽象構文木ノードには
ドキュメンテーションコメントが無いからです。
<!--
That's why `:GoDoc` works for any kind of identifier. If you put your cursor under
`sayHi()` and call `:GoDoc` you'll see that it shows it as well. And if you put
it under `sayYoo()` you'll see that it just outputs `no documentation` for AST
nodes without doc comments.
-->

他の機能と同様に私たちはノーマルモードのデフォルトのショートカット `K`
をオーバーライドして `man` (や他の何か) の代わりに `:GoDoc` を実行する
ようにしています。ドキュメントを見つけるのはとても簡単で、ノーマルモード
で `K` を押すだけです！
<!--
As usual with other features, we override the default normal shortcut `K` so
that it invokes `:GoDoc` instead of `man` (or something else). It's really easy
to find the documentation, just hit `K` in normal mode!
-->

`:GoDoc` は指定された識別子に対するドキュメントだけを表示します。しかし
それは **ドキュメント エクスプローラ** ではありません。ドキュメントを
探索したい場合はそれを行ってくれるサードパーティのプラグインがあります。
[go-explorer](https://github.com/garyburd/go-explorer) です。これを
vim-go に含めるべきという未対応のバグがあります
(訳注: 2017-07-24時点では vim-go の github レポジトリのイシューや
プルリクエストには見当たらなかったので、いつか対応したいリスト的な
ものだと推測します)。
<!--
`:GoDoc` just shows the documentation for a given identifier. But it's not a
**documentation explorer**, if you want to explore the documentation there is
third-party plugin that does it:
[go-explorer](https://github.com/garyburd/go-explorer). There is an open bug to
include it into vim-go.
-->

### 識別子の解決 <!-- Identifier resolution -->

時々関数が何を受け付けて何を返すのか知りたいことがあるでしょう。あるいは
カーソルの下にある識別子が何かを知りたいこともあるでしょう。このような
質問はよくあるので私たちはそれに応えるコマンドを用意しています。
<!--
Sometimes you want to know what a function is accepting or returning. Or what
the identifier under your cursor is. Questions like this are common and we have
a command to answer it.
-->

同じ `main.go` を使い、 `Println` 関数の上に移動して `:GoInfo` を実行
してください。関数のシグネチャがステータスラインに出力されるのが
見られるでしょう。関数が何をするのかを見られるのでこれは非常に素晴らしい
です。このおかげでわざわざジャンプしてシグネチャをチェックしなくても
よくなります。
<!--
Using the same `main.go` file, go over the `Println` function and call
`:GoInfo`. You'll see that the function signature is being printed in the
status line. This is really great to see what it's doing, as you don't have to
jump to the definition and check out what the signature is.
-->

`:GoInfo` を毎回実行するのは退屈です。より速く実行するために改善する
ことができます。例によってより速くする方法はショートカットを追加する
ことです。
<!--
But calling `:GoInfo` every time is tedious. We can make some improvements to
call it faster. As always a way of making it faster is to add a shortcut:
-->

```vim
autocmd FileType go nmap <Leader>i <Plug>(go-info)
```

これで `<leader>i` を押すだけで簡単に `:GoInfo` を実行できるようになりました。
でももう少し改善の余地があります。 vim-go にはカーソルを移動するたびに自動的に
情報を表示するためのサポートがあります。有効にするには `.vimrc` に以下の
設定を追加してください。
<!--
Now you easily call `:GoInfo` by just hitting `<leader>i`. But there is still
room to improve it. vim-go has a support to automatically show the information
whenever you move your cursor. To enable it add the following to your `.vimrc`:
-->

```vim
let g:go_auto_type_info = 1
```

これで有効な識別子にカーソルを移動するたびに、ステータスラインが自動的に
更新されるようになります。デフォルトでは `800ms` 毎に更新します。これは
vim の設定で `updatetime` という設定で変更することができます。 `100ms` に
変更するには `.vimrc` に以下の設定を追加してください。
<!--
Now whenever you move your cursor onto a valid identifier, you'll see that your
status line is updated automatically. By default it updates every `800ms`. This
is a vim setting and can be changed with the `updatetime` setting. To change it
to `100ms` add the following to your `.vimrc`
-->

```vim
set updatetime=100
```

### 識別子のハイライト <!-- Identifier highlighting -->

マッチする全ての識別子を素早く見たいことがあります。変数、関数などです。
以下の Go コードがあるとします。
<!--
Sometimes we just want to quickly see all matching identifiers. Such as variables,
functions, etc.. Suppose you have the following Go code:
-->

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
<!--
If you put your cursor on top of `err` and call `:GoSameIds` you'll see that
all the `err` variables get highlighted. Put your cursor on the `sayHi()`
function call, and you'll see that the `sayHi()` function identifiers all are
highlighted. To clear them just call `:GoSameIdsClear`
-->

毎回手動で実行しなくて済むようになればもっと便利です。 vim-go はマッチする
識別子を自動的にハイライトできます。 `.vimrc` に以下の設定を追加してください。
<!--
This is more useful if we don't have to call it manually every time. vim-go
can automatically highlight matching identifiers. Add the following to your
`vimrc`:
-->

```vim
let g:go_auto_sameids = 1
```

vim を再起動するともう `:GoSameIds` を手動で実行する必要が無いことが
わかるでしょう。マッチする識別子の変数が自動的にハイライトされるように
なっています。
<!--
After restarting vim, you'll see that you don't need to call
`:GoSameIds` manually anymore. Matching identifier variables are now highlighted
automatically for you.
-->

### 依存パッケージとファイル <!-- Dependencies and files -->

ご存知のようにパッケージは複数の依存パッケージとファイルから構成されます。
ディレクトリ内に多くのファイルがあったとしても、 package 節が正しく
書かれているファイルだけがパッケージの一部となります。
<!--
As you know a package can consist of multiple dependencies and files. Even
if you have many files inside the directory, only the files that have the
package clause correctly are part of a package.
-->

パッケージを構成するファイル一覧を見るには以下のコマンドを実行してください。
<!--
To see the files that make a package you can call the following:
-->

```
:GoFiles
```

すると以下のように出力されます (私の `$GOPATH` は `~/Code/Go` に設定されています)。
<!--
which will output (my `$GOPATH` is set to `~/Code/Go`):
-->

```
['/Users/fatih/Code/go/src/github.com/fatih/vim-go-tutorial/main.go']
```

他にもファイルがあれば、それもリストされるでしょう。このコマンドはビルドの
一部になる Go ファイルだけをリストすることに注意してください。テストファイル
はリストされません。
<!--
If you have other files those will be listed as well. Note that this command is
only for listing Go files that are part of the build. Test files will be not
listed.
-->

ファイルが依存しているパッケージを見るには `:GoDeps` を実行してください。
実行すると以下のように出力されるでしょう。
<!--
For showing the dependencies of a file you can call `:GoDeps`. If you call  it
you'll see:
-->

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
<!--
The previous feature was using the `guru` tool under the hood. So let's talk a
little bit about guru. So what is guru? Guru is an editor integrated tool for
navigating and understanding Go code. There is a user manual that shows all the
features: [https://golang.org/s/using-guru](https://golang.org/s/using-guru)
-->

---

このマニュアルに含まれる例と同じものを使って vim-go に統合している
機能のいくつかを紹介しましょう。
<!--
Let us use the same examples from that manual to show some of the features we've
integrated into vim-go:
-->

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
`guru` の `referrers` モードを呼び出します。これは作業領域内の必要な
パッケージ全てをスキャンして、選択された識別子への参照を見つけます。
結果はローケーションリストになります。
<!--
Put your cursor on top of the `handler` and call `:GoReferrers`. This calls the
`referrers` mode of `guru`, which finds references to the selected identifier,
scanning all necessary packages within the workspace. The result will be a
location list.
-->

---

`guru` のモードの1つに `describe` モードがあります。これは `:GoInfo` と
ほぼ同じですが少しだけより進歩しています (より多くの情報を表示してくれます)。
例えば、もし型のメソッドの組があればそれを表示してくれます。選択すると
パッケージの宣言を表示します。
<!--
One of the modes of `guru` is also the `describe` mode. It's just like
`:GoInfo`, but it's a little bit more advanced (it gives us more information).
It shows for example the method set of a type if there is any. It shows the
declarations of a package if selected.
-->

同じ `main.go` ファイルで続けましょう。 (`ServeHTTP` 関数内の)  `URL`
フィールドか `req.URL` にカーソルを置いて `:GoDescribe` を実行してください。
ロケーションリストが以下の内容で表示されます。
<!--
Let's continue with same `main.go` file.  Put the cursor on top of the `URL`
field or `req.URL` (inside the `ServeHTTP` function). Call `:GoDescribe`.  You'll
see a location list populated with the following content:
-->

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
<!--
You'll see that we can see the definition of the field, the method set and the
`URL` struct's fields. This is a very useful command and it's there if you need
it and want to understand the surrounding code. Try and experiment by calling
`:GoDescribe` on various other identifiers to see what the output is.
-->

---

最もよく聞かれる質問の1つはある型が実装しているインターフェースを
どうやって知れば良いのかということです。ある型といくつかのメソッドの組が
あるとしましょう。あなたはその型がどのインタフェースを実装しているかを
知りたいです。 `guru` の `implement` がまさにそれをしてくれて、型が
実装しているインターフェースを探すのを手伝ってくれます。
<!--
One of the most asked questions is how to know the interfaces a type is
implementing. Suppose you have a type and with a method set of several methods.
You want to know which interface it might implement. The mode `implement` of
`guru` just does it and it helps to find the interface a type implements.
-->

同じ前の `main.go` ファイルで続けましょう。 `main()` 関数直後の
`handler` 識別子にカーソルを置いてください。 `:GoImplements` を実行すると
ロケーションリストが以下の内容になるのが見られるでしょう。
<!--
Just continue with the same previous `main.go` file. Put your cursor on the
`handler` identifier just after the `main()` function. Call `:GoImplements`
You'll see a location list populated with the following content:
-->


```
main.go|23 col 6| chan type handler
/usr/local/go/src/net/http/server.go|57 col 6| implements net/http.Handler
```

1行目は選択した型で2行目はその型が実装しているインターフェースです。
1つ型が多くのインターフェースを実装することが可能なのでナビゲートできる
ようにロケーションリストを使っています。
<!--
The first line is our selected type and the second line will be the interface
it implements. Because a type can implement many interfaces it's a location
list.
-->

---

`guru` のモードのうち役立ちそうな1つが `whicherrs` です。ご存知のように
エラーは単なる値です。ですのでそれらはプログラムで生成されることができ、
それゆえあらゆる型を表すことができます。 `guru` のマニュアルに書かれて
いることを見てみましょう。
<!--
One of the `guru` modes that might be helpful is `whicherrs`. As you know
errors are just values. So they can be programmed and thus can represent any
type. See what the `guru` manual says:
-->

> whicherrs モードはエラーの型の値に現れうる可能な定数の組、グローバル
> 変数、そして具象型を報告します。この情報はエラーを処理する際に、扱われて
> きた全ての重要なケースをカバーしていることを確実にするために役立ちます。
<!--
> The whicherrs mode reports the set of possible constants, global variables,
> and concrete types that may appear in a value of type error. This information
> may be useful when handling errors to ensure all the important cases have
> been dealt with.
-->

ではどのように使うのでしょうか？簡単です。引き続き同じ `main.go` ファイルを
使います。 `http.ListenAndServe` から返される `err` 識別子にカーソルを
置いてください。 `:GoWhicherrs` を実行すると以下の出力が見られるでしょう。
<!--
So how do we use it? It's easy. We still use the same `main.go` file. Put your
cursor on top of the `err` identifier which is returned from `http.ListenAndServe`.
Call `:GoWhicherrs` and you'll see the following output:
-->

```
main.go|12 col 6| this error may contain these constants:
/usr/local/go/src/syscall/zerrors_darwin_amd64.go|1171 col 2| syscall.EINVAL
main.go|12 col 6| this error may contain these dynamic types:
/usr/local/go/src/syscall/syscall_unix.go|100 col 6| syscall.Errno
/usr/local/go/src/net/net.go|380 col 6| *net.OpError
```

`err` の値が `syscall.EINVAL` 定数になるかもしれないことや動的な型の
`syscall.Errno` あるいは `*net.OpError` になるかもしれないことがわかるでしょう。
ご覧のようにこれは必要なら異なるエラー処理をするカスタムロジックを実装
するのに非常に有用です。このクエリには guru の `スコープ` を設定しておく
必要があることに注意してください。 `スコープ` とは何か、どのようにして
それを動的に変更するかについてはこの後すぐ説明します。
<!--
You'll see that the `err` value may be the `syscall.EINVAL` constant or it also
might be the dynamic types `syscall.Errno` or `*net.OpError`. As you see this is
really helpful when implementing custom logic to handle the error differently if
needed. Note that this query needs a guru `scope` to be set. We'll going to
cover in a moment what a `scope` is and how you can change it dynamically.
-->

---

同じ `main.go` ファイルを使って続けましょう。 Go はチャンネルなど並列処理の
プリミティブで有名です。値がチャンネルの間でどのように送られるかを追跡するのは
時折困難になります。より良く理解するために `guru` の `peers` モードがあります。
これはチャンネルの演算子 (送信または受信の操作) について発生しうる送信／受信の
組を表示します。
<!--
Let's continue with the same `main.go` file.  Go is famous for its concurrency
primitives, such as channels. Tracking how values are sent between channels can
sometimes be hard. To understand it better we have the `peers` mode of `guru`.
This query shows the set of possible send/receives on the channel operand (send
or receive operation).
-->

カーソルを次の式に移動して行全体を選択してください (訳注: 行全体の選択は不要でした)。
<!--
Move your cursor to the following expression and select the whole line:
-->

```go
ch <- n
```

`:GoChannelPeers` を実行してください。ロケーションリストが以下の内容で
表示されるのを見られるでしょう。
<!--
Call `:GoChannelPeers`. You'll see a location list window with the following content:
-->

```
main.go|19 col 6| This channel of type chan<- int may be:
main.go|10 col 11| allocated here
main.go|19 col 6| sent to, here
main.go|27 col 53| received from, here
```

ご覧のようにチャンネルの作成、どこに送信しているか、どこから受信しているかが
表示されます。これはポインター解析を使っているので、スコープを定義する
必要があります。
<!--
As you see you can see the allocation of the channel, where it's sending and
receiving from. Because this uses pointer analysis, you have to define a scope.
-->

---


関数呼び出しとターゲットがどう関係しているかを見てみましょう。今度は
以下の複数のファイルを作ります。 `main.go` は以下の内容にしてください。
<!--
Let us see how function calls and targets are related. This time create the
following files. The content of `main.go` should be:
-->

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
<!--
And the file should be under `example/example.go`:
-->

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
<!--
So jump to the `Hello` function inside `main.go` and put your cursor on top of
the function call named `fn()`. Execute `:GoCallees`. This command shows the
possible call targets of the selected function call. As you see it'll show us
the function declarations inside the `example` function. Those functions are
the callees, because they were called by the function call named `fn()`.
-->

再び `main.go` に戻って今度は `Hello()` の関数定義にカーソルを置いてください。
この関数の呼び出し側を見るにはどうしたら良いでしょう？ `:GoCallers` を
実行してください。
<!--
Jump back to `main.go` again and this time put your cursor on the function
declaration `Hello()`. What if we want to see the callers of this function?
Execute `:GoCallers`.
-->

以下の出力が見られるはずです。
<!--
You should see the output:
-->

```
main.go| 10 col 7 static function call from github.com/fatih/vim-go-tutorial.Main
main.go| 11 col 7 static function call from github.com/fatih/vim-go-tutorial.Main
```

最後に、 `callstack` というモードもあります。これは選択部分を含む関数への
コールグラフの根本からの任意のパスを表示します。
<!--
Finally there is also the `callstack` mode, which shows an arbitrary path from
the root of the call graph to the function containing the selection.
-->


`Hello()` 関数内の `fn()` 関数呼び出しにカーソルを戻してください。関数を
選択して `:GoCallstack` を実行してください。出力は以下のようになる
はずです (一部省略しています)。
<!--
Put your cursor back to the `fn()` function call inside the `Hello()` function.
Select the function and call `:GoCallstack`. The output should be like
(simplified form):
-->

```
main.go| 15 col 26 Found a call path from root to (...)Hello
main.go| 14 col 5 (...)Hello
main.go| 10 col 7 (...)main
```

`15` 行目から開始して、次は `14` 行目で最後は `10` 行目となっています。
これは (`main()` 関数から始まる) 根本から私たちが選択した
関数 (今のケースでは `fn()`) へのグラフとなっています。
<!--
It starts from line `15`, and then to line `14` and then ends at line `10`.
This is the graph from the root (which starts from `main()`) to the function we
selected (in our case `fn()`)
-->

---

`guru` コマンドのほとんどについてはスコープを定義する必要はありません。
`スコープ` とは何でしょう？ 以下に `guru` の
[マニュアル](http://golang.org/s/using-guru) からそのまま引用します。
<!--
For most of the `guru` commands you don't need to define any scope. What is a
`scope`? The following excerpt is straight from the `guru`
[manual](http://golang.org/s/using-guru):
-->

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
<!--
> Pointer analysis scope: some queries involve pointer analysis, a technique for
> answering questions of the form “what might this pointer point to?”.  It is
> usually too expensive to run pointer analysis over all the packages in the
> workspace, so these queries require an additional configuration parameter
> called the scope, which determines the set of packages to analyze.  Set the
> scope to the application (or set of applications-\-\-a client and server,
> perhaps) on which you are currently working.  Pointer analysis is a
> whole-program analysis, so the only packages in the scope that matter are the
> main and test packages.
>
> The scope is typically specified as a comma-separated set of packages, or
> wildcarded subtrees like github.com/my/dir/...; consult the specific
> documentation for your editor to find out how to set and vary the scope.
-->


`vim` は自動的にスマートになろうとして現状のパッケージインポートパスを
あなたに代わって `スコープ` として設定します。コマンドがスコープを必要と
する場合はこれでほとんどの場合はカバーできます。ほとんどの場合はこれで
十分ですが、いくつかのクエリではスコープの設定を変更したいかもしれません。
その場で `スコープ` を特定の設定に簡単に変更できるように `:GoGuruScope`
があります。
<!--
`vim-go` automatically tries to be smart and sets the current packages import
path as the `scope` for you. If the command needs a scope, you're mostly
covered. Most of the times this is enough, but for some queries you might to
change the scope setting. To make it easy to change the `scope` on the fly with
have a specific setting called `:GoGuruScope`
-->

実行すると `guru scope is not set` (guruのスコープが設定されていません)
というエラーが出ます。 `github.com/fatih/vim-go-tutorial` というスコープ
に明示的に変更してみましょう。
<!--
If you call it, it'll return an error: `guru scope is not set`. Let us change
it explicitly to the `github.com/fatih/vim-go-tutorial" scope:
-->

```
:GoGuruScope github.com/fatih/vim-go-tutorial
```

以下のメッセージが表示されるはずです。
<!--
You should see the message:
-->

```
guru scope changed to: github.com/fatih/vim-go-tutorial
```

`:GoGuruScope` を引数無しで実行すると、以下のように出力されます。
<!--
If you run `:GoGuruScope` without any arguments, it'll output the following
-->

```
current guru scope: github.com/fatih/vim-go-tutorial
```

`GOPATH` 全体を選択するには `...` 引数が使えます。
<!--
To select the whole `GOPATH` you can use the `...` argument:
-->

```
:GoGuruScope ...
```

また複数のパッケージはさらにサブディレクトリを定義することもできます。
以下の例は `github.com` 以下の全てのパッケージと `golang.org/x/tools`
パッケージを選択します。
<!--
You can also define multiple packages and also subdirectories. The following
example selects all packages under `github.com` and the `golang.org/x/tools`
package:
-->

```
:GoGuruScope github.com/... golang.org/x/tools
```

パッケージの先頭に `-` (マイナス) 記号をつけることでパッケージを除外
することができます。以下のは `encoding` 配下のすべてのパッケージを
`encoding/xml` は除いて選択します。
<!--
You can exclude packages by prepending the `-` (negative) sign to a package.
The following example selects all packages under `encoding` but not
`encoding/xml`:
-->

```
:GoGuruScope encoding/... -encoding/xml
```

スコープをクリアするには空文字列を渡します。
<!--
To clear the scope just pass an empty string:
-->

```
:GoGuruScope ""
```

あなたがとあるプロジェクトで作業していてスコープをいつも同じ値に設定している
がVimを起動するたびに `:GoGuruScope` を実行したくない場合は `.vimrc` に
設定を追加することで永続的なスコープを定義することもできます。
値は文字列のリスト型である必要があります。以下に上記のコマンドと同じ内容の
設定例をいくつか示します。
<!--
If you're working on a project where you have to set the scope always to the
same value and you don't want to call `:GoGuruScope` everytime you start Vim,
you can also define a permanent scope by adding a setting to your `vimrc`. The
value needs to be a list of string types. Here are some examples from the
commands above:
-->

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
<!--
Finally, `vim-go` tries to auto complete packages for you while using
`:GoGuruScope` as well. So when you try to write
`github.com/fatih/vim-go-tutorial` just type `gi` and hit `tab`, you'll see
it'll expand to `github.com`
-->

---

あなたが意識するべき別の設定はビルドタグです (ビルド制約とも呼ばれます)。
例えばあなたの Go のソースコードに以下のビルドタグを入れるかもしれません。
<!--
Another setting that you should be aware are build tags  (also called build
constraints). For example the following is a build tag you put in your Go
source code:
-->

```
// +build linux darwin
```

あるときはあなたのソースコードに以下のようなカスタムタグを入れるかもしれません。
<!--
Sometimes there might be custom tags in your source code, such as:
-->

```
// +build mycustomtag
```

このケースでは裏で使われている `go/build` パッケージがパッケージを
ビルドできないので guru は失敗するでしょう。すると `guru` に関連するコマンド
は全て (`guru` を使う場合の `:GoDef` でさえも) 失敗するでしょう。
幸いにも `guru` は `-tags` フラグがあり、それを使えばカスタムタグを
スキップすることができます。 `vim-go` ユーザの手間を省けるように
`:GoBuildTags` も提供しています。
<!--
In this case, guru will fail as the underlying `go/build` package will be not
able to build the package. So all `guru` related commands will fail (even
`:GoDef` when it uses `guru`). Fortunately `guru` has a `-tags` flag that
allows us to pass custom tags. To make it easy for `vim-go` users we have a
`:GoBuildTags`
-->

例えばちょっと以下のコマンドを実行してみてください。
<!--
For the example just call the following:
-->

```
:GoBuildTags mycustomtag
```

こうすると `guru` にこのタグを渡すようになり、これ以降は期待通りに機能します。
`:GoGuruScope` とちょうど同じように、以下のコマンドでクリアできます。
<!--
This will pass this tag to `guru` and from now on it'll work as expected. And
just like `:GoGuruScope`, you can clear it with:
-->

```
:GoBuildTags ""
```

そして最後に、もしあなたが望むなら以下の設定によって永続的にすることもできます。
<!--
And finally if you wish you can make it permanent with the following setting:
-->

```
let g:go_build_tags = "mycustomtag"
```

# リファクタリングする <!-- Refactor it -->

### 識別子をリネームする <!-- Rename identifiers -->

最もよく行う作業の1つが識別子をリネームすることです。しかし、それは他の
パッケージを壊さないように注意深く行わなければならないことでもあります。
また `sed` のようなツールを使うだけでは有用でないこともあります。
抽象構文木を理解したリネームを行うには抽象構文機の一部の識別子のみを
リネームする必要があります (例えばビルドスクリプトなど Go のソースでない
別のファイルの中の識別子をリネームすべきではありません)。
<!--
Renaming identifiers is one of the most common tasks. But it's also something
that needs to be done carefully in order not to break other packages as well. Also just
using a tool like `sed` is sometimes not useful, as you want AST aware
renaming, so it only should rename identifiers that are part of the AST (it
should not rename for example identifiers in other non Go files, say build
scripts)
-->

あなたのためにこういうリネームを行ってくれるツールがあります。それは
`gorename` と言います。 `vim-go` では `:GoRename` コマンドを使います。
それは裏では `gorename` を使っています。 `main.go` の内容を以下のように
変更しましょう。
<!--
There is a tool that does renaming for you, which is called `gorename`.
`vim-go` uses the `:GoRename` command to use `gorename` under the hood.  Let us
change `main.go` to the following content:
-->

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
<!--
Put your cursor on top of the `name` field inside the `Server` struct and call
`:GoRename bar`.  You'll see all `name` references are renamed to `bar`. The
final content would look like:
-->

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
<!--
As you see, only the necessary identifiers are renamed, but the function `name`
or the string inside the comment is not renamed. What's even better is that
`:GoRename` searches all packages under `GOPATH` and renames all identifiers
that depend on the identifier. It's a very powerful tool.
-->

### 関数を抽出する <!-- Extract function -->

別の例に移りましょう。 `main.go` ファイルを以下のように変更してください。
<!--
Let's move to another example. Change your `main.go` file to:
-->

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
<!--
This is a basic example that just counts the newlines in our `msg` variable. If
you run it, you'll see that it outputs `3`.
-->

改行を数えるロジックを外でも使いたいとしましょう。リファクタリングしてみましょう。
Guru は `freevars` モードでこのような状況で私たちを助けてくれます。
`freevars` モードは指定された選択範囲の中で参照されているが定義されていない
変数を表示してくれます。
<!--
Assume we want to reuse the newline counting logic somewhere else. Let us
refactor it. Guru can help us in these situations with the `freevars` mode. The
`freevars` mode shows variables that are referenced but not defined within a
given selection.
-->

`visual` モードで以下の部分を選択しましょう。
<!--
Let us select the piece in `visual` mode:
-->

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
<!--
After selecting it, call `:GoFreevars`. It should be in form of
`:'<,'>GoFreevars`. The result is again a quickfix list and it contains all the
variables that are free variables. In our case it's a single variable and the
result is:
-->


```go
var msg string
```

さてこれがどのように役立つのでしょう？このちょっとした情報でスタンドアローンの
関数にリファクタリングするには十分です。以下の内容で新しい関数を作ってください。
<!--
So how useful is this? This little piece of information is enough to refactor
it into a standalone function. Create a new function with the following content:
-->

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
`main.go` は以下のようになります。
<!--
You'll see that the content is our previously selected code. And the input to
the function is the result of `:GoFreevars`, the free variables. We only
decided what to return (if any). In our case we return the count. Our `main.go` will be in the form of:
-->

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
<!--
That's how you refactor a piece of code. `:GoFreevars` can be used also to
understand the complexity of a code. Just run it and see how many variables are
dependent to it.
-->

# コード生成する <!-- Generate it -->

コード生成はホットなトピックです。 go/ast 、 go/parser 、 go/printer などの
素晴らしい標準ライブラリのおかげで、Go は素晴らしいジェネレータを簡単に
作成できるという利点があります。
<!--
Code generation is a hot topic. Because of the great std libs such as go/ast,
go/parser, go/printer, etc.. Go has the advantage of being able to create great generators
easily.
-->

まず `:GoGenerate` コマンドがあります。これは裏では `go generate` を実行
します。 `:GoBuild` や `:GoTest` などと同じように動きます。エラーがあったら
クイックフィクスリストに表示するので簡単に修正できます。
<!--
First we have the `:GoGenerate` command that calls `go generate` under the
hood. It just works like `:GoBuild`, `:GoTest`, etc.. If there are any errors it
also shows them so you can easily fix it.
-->

### インターフェースを実装するメソッドスタブ <!-- Method stubs implementing an interface -->

インターフェースはコンポジションをするのに非常に素晴らしいです。あなたの
コードをより扱いやすくしてくれます。また、インタフェース型を受け取る関数
をそのインターフェースを実装するメソッドを持つ型でモックすることもできます。
<!--
Interfaces are really great for composition. They make your code easier to deal
with. It's also easier for you to create tests as you can mock functions that
accept an interface type with a type that implements methods for testing.
-->


`vim-go` は [impl](https://github.com/josharian/impl) というツールを
サポートしています。 `impl` は指定したインターフェースを実装するメソッド
スタブを生成します。 `main.go` の内容を以下のように変更しましょう。
<!--
`vim-go` has support for the tool [impl](https://github.com/josharian/impl).
`impl` generates method stubs that implement a given interface. Let us change
`main.go`'s content to the following:
-->

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
<!--
Put your cursor on top of `T` and type `:GoImpl`. You'll be prompted to write
an interface. Type `io.ReadWriteCloser` and hit enter. You'll see the content
changed to:
-->

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
<!--
That's really neat as you see. You can also just type `:GoImpl
io.ReadWriteCloser` when you're on top of a type and it'll do the same.
-->

でも型の先頭にカーソルを置く必要はありません。どこからでも
実行することができます。例えば以下のように実行してください。
<!--
But you don't need to put your cursor on top of a type.  You can invoke it from
everywhere. For example execute this:
-->

```
:GoImpl b *B fmt.Stringer
```

以下のコードが作成されるのが見られるでしょう。
<!--
You'll see the following will be created:
-->

```go
func (b *B) String() string {
	panic("not implemented")
}
```

ご覧のようにこれはとても助かります。特に多数のメソッドの組を持つ
大きなインターフェースを実装するときはそうです。簡単にコードが
生成でき、 `panic()` を使っているのでなんの問題もなくコンパイル
できます。必要な部分を埋めればそれで完了です。
<!--
As you see this is very helpful, especially if you have a large interface with
large method set. You can easily generate it, and because it uses `panic()`
this compiles without any problem. Just fill the necessary parts and you're
done.
-->

# シェアする <!-- Share it -->

`vim-go` はあなたのコードを https://play.golang.org/ で他の人と簡単に
シェアする機能も持っています。ご存知のように Go playground は小さな
スニペット、練習問題やチップスやトリックをシェアするのに完璧な場所です。
あなたがとあるアイディアを試していて他の人にシェアしたいときがある
でしょう。 `vim-go` はこういったこと全てを `:GoPlay` コマンドでより良く
します。
<!--
`vim-go` has also features to easily share your code with other via
https://play.golang.org/. As you know the Go playground is a perfect place to
share small snippets, exercises and/or tips & tricks. There are times you are
playing with an idea and want to share with others. You copy the code and visit
play.golang.org and then paste it.  `vim-go` makes all these better with the
`:GoPlay` command.
-->

まず `main.go` ファイルを以下のシンプルなコードに変更しましょう。
<!--
First let us change our `main.go` file with the following simple code:
-->

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
<!--
Now call `:GoPlay` and hit enter. You'll see that `vim-go` automatically
uploaded your source code `:GoPlay` and also opened a browser tab that shows
it. But there is more. The snippet link is automatically copied to your
clipboard as well. Just paste the link to somewhere. You'll see the link is the
same as what's on play.golang.org.
-->

`:GoPlay` は範囲も受け付けます。一片のコードを選択して `:GoPlay` を
実行してください。選択された部分だけがアップロードされます。
<!--
`:GoPlay` also accepts a range. You can select a piece of code and call
`:GoPlay`. It'll only upload the selected part.
-->

`:GoPlay` の振る舞いを変更する設定が2つあります。 `vim-go` がブラウザの
タブを開くのが好きではない場合は以下の設定で無効にできます。
<!--
There are two settings to tweak the behavior of `:GoPlay`. If you don't like
that `vim-go` opens a browser tab for you, you can disable it with:
-->

```
let g:go_play_open_browser = 0
```

次にブラウザが誤判定される (私たちは `open` か `xdg-open` を使っています) なら
以下の設定で手動でブラウザを設定できます。
<!--
Secondly, if your browser is misdetected (we're using `open` or `xdg-open`) you
can manually set the browser via:
-->

```
let g:go_play_browser_command = "chrome"
```

# HTML テンプレート <!-- HTML template -->

Go の HTML テンプレートのシンタクスハイライトはデフォルトでは `.tmpl` ファイルに
対して有効になっています。別のファイル種別に対して有効にしたい場合は、 `.vimrc` に
以下の設定を追加してください。
<!--
By default syntax highlighting for Go HTML template is enabled for `.tmpl` files.
If you want to enable it for another filetype add the following setting to your `.vimrc`:
-->

```
au BufRead,BufNewFile *.gohtml set filetype=gohtmltmpl
```


# 寄付 <!-- Donation -->

このチュートリアルは私のプライベートの時間を使って作られました。この
チュートリアルが気に入って寄付したいと思われたら、
[パトロンになる](https://www.patreon.com/fatih) でぜひ完全なサポーターに
なってください！
<!--
This tutorial was created by me in my spare time. If you like it and would
like to donate, you now you can be a fully supporter by [being a
patron](https://www.patreon.com/fatih)!
-->

パトロンになることで、あなたは vim-go をより成長し成熟させることができ、
私がバグ修正のために調査したり、新しいドキュメントを書いたり、今ある
機能や将来の機能追加を私が行うのを支援することができます。これは完全に
任意で vim-go の現在進行中の開発を直接支援する方法の1つです。
ありがとう！
<!--
By being a patron, you are enabling vim-go to grow and mature, helping me to
invest in bug fixes, new documentation, and improving both current and future
features. It's completely optional and is just a direct way to support vim-go's
ongoing development. Thanks!
-->

[https://www.patreon.com/fatih](https://www.patreon.com/fatih)

## 今後説明を追記する予定のコマンド <!-- TODO Commands -->
* :GoPath
* :AsmFmt

