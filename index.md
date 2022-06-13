<script src="main.js"></script>
  <script type="text/javascript" id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
    </script>
  <script>
    MathJax = {
      loader: { load: ['[tex]/physics','[tex]/newcommand'] },
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        packages: { '[+]': ['physics', 'newcommand'] },
      },
      chtml: {
        matchFontHeight: false
      }
    };
  </script>
  

# Markdown で $\LaTeX$ する

Markdownは軽量のプログラミング言語である。数式をライブでレンダリングするMathJax,KaTeXを使えるところに目をつけて数式を書くために使ってみた。 $\LaTeX$ との違いはコンパイルの概念がないことだろうか。とはいえ導入に一筋縄ではいかないことに気付いたのでメモしておく。

## VSCodeのユーザースニペット
初期設定ではoffになっているので忘れずにonにしておくこと。
早速だが下の表を見てほしい。


|     |  latex | md | .tex | .md | "latex" |
|-------|--------|----|------|-----|---------|
| .     | ○      | ○  | ×    | ○   | ×       |
| /     | ○      | ○  | ×    | ○   | ×       |
| __と** | ○      | ×  | ○    | ×   | ○       |
| @     | ×      | ×  | ○    | ×   | ×       |
| \     | ×      | ×  | ○    | ○   | ×       |


これはVSCodeのユーザースニペットで一文字でスニペットを出してくれるかくれないかを表す表である。
先頭からjupyternotebookにおいてlatexは青と黄色にハイライトされている時、mdはオレンジの時、.tex,.mdはそれぞれ拡張子.tex、.mdのファイルで開いた時、"latex"はjupyterの言語設定を"latex"に変えたものである。この表は相当錯綜しているが、なんとなくMarkdownでは $\LaTeX$ でいうところの`@`と`\`の代わりに`.`と`/	`が使えそうだとわかるはずだ。

## ユーザースニペットの定義の仕方
###  $\LaTeX$ からの輸入
基本的には $\LaTeX$ のように数式を打つのだから全く同じように打ちたいと考える。どうやって打つかがきもになる。LaTeX Workshopに準拠したいと考えるならPC上の/Users/home/.vscode/extensions/james-yu.latex-workshop-8.27.2/data/at-suggestions.jsonから`@`で始まるスニペットを輸入するのがおすすめ。とはいえ元のコードを書き換えるわけにはいかないからコピペするのが最も安全だろう。/Users/home/.vscode/extensions/james-yu.latex-workshop-8.27.2/snippets/latex.jsonにもその他の役に立つスニペット（フォントに関することはMarkdownでは使わない）があるのでこちらもコピペがおすすめ。
### 変換
輸入したスニペットはそのままでは使えない。上の表にある仕様のせいである。回避するには例えば`cmd+shift+L`を押すなどしてat-suggestions内の全コードの`@`を`.`または`/`に変えてしまうのがいいだろう。ついでに例えば`\alpha`のコードをちょっといじって`\alpha `にしてしまう（空白を入れる）のもおすすめ。こうするとスニペットで`\alpha `を出した後に空白を打たなくて済む。

###　割り当て
コピーした上でどの記号を何に割り当てるのかは結構重要である。ちなみに自分は主要なものだと下のようにしている。
```json
~/Library/Application Support/Code/User/snippets/latexmd.code-snippets
	"inline_math2": {
		"prefix": "/",
		"scope": "latex,markdown",
		"body": " $$1$ $0"
	},
	"\\left( \\right)2": {
		"prefix": "..",
		"scope": "latex,markdown",
		"body": "\\left($1\\right)$0"
	},
	"\\left| \\right|2": {
		"prefix": ".|",
		"scope": "latex,markdown",
		"body": "\\left|$1\\right|$0"
	},
	"\\left{ \\right}2": {
		"prefix": ".{",
		"scope": "latex,markdown",
		"body": "\\left\\{$1\\right\\}$0"
	},
	"\\left[ \\right]2": {
		"prefix": ".[",
		"scope": "latex,markdown",
		"body": "\\left[$1\\right]$0"
	},
	"別行立て数式2": {
		"prefix": "ma",
		"body": [
			"",
			"$$",
			"${1}",
			"$$",
			"",
			"${0}"
		]
	},
	"and2": {
		"prefix": ".",
		"scope": "latex,markdown",
		"body": "& "
	},
	"dfrac": {
	"prefix": "//",
	"scope": "latex,markdown",
	"body": "\\dfrac{$1}{$2}$0"
	},
	"inline_code": {
	"prefix": ".,",
	"body": "`$1`$0"
	},
```
ポイントは`scope`を`"latex,markdown"`にしておくこととスニペットファイルをグローバルにしておくことである。命令が既にあるものと重なるものは必ず`2`をつけて表した。`inline_math2`は挙動がLaTeX Workshopのinline_mathと近くなるようにしておいた。（向こうでは`\`を打つところをこっちでは`/`を打っている。前後に空白を空けている。）`left` `right`系のコマンドはあまり変えなかったが`.|`を付け足したのと元の`@(`がちょっと打ちにくいので`..`に変えておいた。別行立ての数式は普通にmathのアルファベット。`inline_code`は打ちやすい位置にした。
