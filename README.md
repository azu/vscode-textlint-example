# VS Codeでtextlintを使って文章をチェックする

[textlint](https://textlint.github.io/ "textlint")での文章チェックを[VS Code](https://code.visualstudio.com/ "VS Code")上で行う方法についての話です。

VS Codeでtextlintを動かす拡張として[vscode-textlint](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint)があるのでそれをインストールして動かすまでの流れを見ていきます。
（vscode-textlintでグローバルの設定を使う方法はよくわからなかったので、今回はプロジェクトのローカルにインストールしたtextlintを使うまでの手順を見ていきます）

- [vscode-textlint - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint "vscode-textlint - Visual Studio Marketplace")

## インストール

VS Codeを開き、Cmd+P(Ctrl+P)でコマンドパレットを開いて、拡張をインストールするだけです。

```
ext install vscode-textlint
```

既に、textlintの設定をしているプロジェクト(node_modulesにtextlintやルールがインストールされていて、.textlintrcが配置されている場所)なら、VS Codeでそのディレクトリを開くだけで動作します。

今回は、textlintを設定したプロジェクトを作る所からやってみます。

## プロジェクトの作成

適当なディレクトリを作ります。
今回は、`vscode-textlint-example/`という名前のディレクトリを作ってみます。

実際に作ったリポジトリがあるので、cloneすれば次のコマンドで試すことができます。

```sh
git clone https://github.com/azu/vscode-textlint-example.git
cd vscode-textlint-example
npm install
vscode .
```

- [azu/vscode-textlint-example: VS Codeを使ったtextlint projectのサンプル](https://github.com/azu/vscode-textlint-example "azu/vscode-textlint-example: VS Codeを使ったtextlint projectのサンプル")

### npm init

[textlint](https://textlint.github.io/ "textlint")はNode.jsで動くツールなので、
ローカル(プロジェクトのディレクトリ内という意味)にインストールする際は、`package.json`というNode.jsのモジュールを管理するファイルを作成します。

Note: textlintはNode.js v4以上が必要なので、`node -v`でバージョンを確認してみてください。

`npm init`を実行すると色々聞かれるので、とりあえずEnter押してれば`package.json`が作成されます。

```sh
$ npm init
name: (vscode-textlint-example)
version: (1.0.0)
description: VS Codeを使ったtextlint projectのサンプル
entry point: (index.js)
test command:
git repository:
keywords:
license: (MIT)
About to write to /Users/azu/.ghq/github.com/azu/vscode-textlint-example/package.json:

{
  "name": "vscode-textlint-example",
  "version": "1.0.0",
  "description": "VS Codeを使ったtextlint projectのサンプル",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "azu",
  "license": "MIT"
}


Is this ok? (yes)
```

### textlintのインストール

textlintをローカルにインストールします。
この辺は、通常のtextlintのインストールと同じなのです。
既に色々な環境でのインストール方法があるので参考にしてみてください。

- [textlint/getting-started.md at master · textlint/textlint](https://github.com/textlint/textlint/blob/master/docs/getting-started.md)
- [textlintで日本語の文章をチェックする | Web Scratch](http://efcl.info/2015/09/10/introduce-textlint/)
- [textlintで日本語テキストの文字校正を試してみた - the code to rock](http://note103.hateblo.jp/entry/2016/06/13/074744)
- [KenshoFujisaki/TextlintAtom: Atomでtextlintを使うための手順](https://github.com/KenshoFujisaki/TextlintAtom)
- [Ubuntu 15.04でtextlint - メモ](http://mickey-happygolucky.hatenablog.com/entry/2016/05/22/141054)

また、textlintはデフォルトでは何もルールを持っていないため何もチェックできません。
そのため、同時にルールもインストールしてみます。

textlint本体と[textlint-rule-preset-ja-technical-writing](https://github.com/textlint-ja/textlint-rule-preset-ja-technical-writing "textlint-rule-preset-ja-technical-writing")という技術書向けのルールセットをインストールしてみます。
インストールには、npmの`npm install`コマンドを使うだけです。

```sh
npm install --save-dev textlint textlint-rule-preset-ja-technical-writing
# devDependenciesとしてインストールする
```

インストールできたら、`node_modules/`というディレクトリにモジュールがインストールされています。
また、`pacakage.json`が更新され、インストールしたモジュールの[一覧が追記](https://github.com/azu/vscode-textlint-example/commit/0bccd013e3f0881b974e526b42292d57f09aab56)されています。

### textlintの設定

次にtextlintで利用するルールを設定してみます。
textlintの設定には`.textlintrc`という設定ファイルに、利用するルールを記述していきます。

ローカルへインストールした時に、`./node_modules/.bin/textlint`へ`textlint`コマンドもインストールされています。
`textlint --init`コマンドを叩くことで、設定ファイルを自動的に作成してくれます。

```sh
$ ./node_modules/.bin/textlint --init
# またはnpm binコマンドを使って
$ $(npm bin)/textlint --init
```

作成した`.textlintrc`には、そのプロジェクトにインストールしている(`package.json`に書かれてる)tetlintのルールを自動で記述してくれます。
そのため、これで設定は完了です。

```json
{
  "filters": {},
  "rules": {
    "preset-ja-technical-writing": true
  }
}
```

## VS Codeで開く

後は、作成したプロジェクトをVS Codeで開くだけです。

```sh
vscode ./
```

開いて見るとtextlintでエラーとなっている場所が既にありました。

![VSCode with textlint](https://monosnap.com/file/X6XVskvC2XCZ5sPtZ0sVWP50yEXEns.png)

`textlint`コマンドを使うことでも同じエラーをコマンドラインでみることができます。

```sh
$ $(npm bin)/textlint README.md

/Users/azu/.ghq/github.com/azu/vscode-textlint-example/README.md
  105:15  error  一文に二回以上利用されている助詞 "に" がみつかりました。  preset-ja-technical-writing/no-doubled-joshi
  134:34  error  一文に二回以上利用されている助詞 "が" がみつかりました。  preset-ja-technical-writing/no-doubled-joshi

✖ 2 problems (2 errors, 0 warnings)
```

後は、エラーに沿って文章を直していくだけです。

## 間違いを自動修正する

ここまで[Visual Studio Code](https://code.visualstudio.com/ "Visual Studio Code - Code Editing. Redefined")のことを`VSCode`と書いてることに気づきました。
公式サイトを見てみると、`VS Code`と書くのが正しいようです。

文章を書いているとこういうスペル的なミスはよくあるので意識していても回避することが難しいです。
[textlint-rule-prh](https://github.com/azu/textlint-rule-prh "textlint-rule-prh")というルールと辞書を作ってチェックしてみましょう。

- [textlint + prhで表記ゆれを検出する | Web Scratch](http://efcl.info/2015/09/14/textlint-rule-prh/ "textlint + prhで表記ゆれを検出する | Web Scratch")

まずは、[textlint-rule-prh](https://github.com/azu/textlint-rule-prh "textlint-rule-prh")をインストールします。

```sh
$ npm install --save-dev textlint-rule-prh
```

次に、textlint-rule-prhで使う辞書を作成します。
辞書と言っても正規表現書いたただのymlファイルを作るだけです。
詳しくは次のファイルを参考にしてみてください。

- [prh/prh.yml at master · prh/prh](https://github.com/prh/prh/blob/master/misc/prh.yml "prh/prh.yml at master · prh/prh")

`prh.yml`というファイルを作成して、次のように`VSCode`は`VS Code`という綴りにするという設定を書きます。

```yml
version: 1
rules:
  - expected: VS Code
    patterns: VSCode
    prh: 公式サイトによるとVS Codeらしい
```

次に、`.textlintrc`にprhを使う設定をします。
`prh`の`rulePaths`に辞書ファイルへのパスを指定します。

```json
{
  "filters": {},
  "rules": {
    "preset-ja-technical-writing": true,
    "prh": {
      "rulePaths": ["./prh.yml"]
    }
  }
}
```

この設定をすれば、`VSCode => VS Code`というエラーが自動的に検出されます。

![VSCode -> VS Code](https://monosnap.com/file/u6vzhtbQ0qMMv6EYxJ7PORfflYNu7t.png)

### 自動修正する

[vscode-textlint](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint "vscode-textlint - Visual Studio Marketplace")は、textlintの`--fix`にも対応しています。
エラーが出ている行でAlt+Enterを押せば、自動的にエラーを修正することができます。
(修正できるかはルールによってことなりますが、prhの辞書なら自動修正が可能です)

![autofix](http://i.giphy.com/3o7TKty7QymM429YE8.gif)

`textlint --fix`でも同じように自動修正が可能です。

```sh
$ $(npm bin)/textlint --fix README.md
```

## おわりに

vscode-textlintを使うことでリアルタイムにチェックしながら文章を書くことができます。
紹介してませんが、設定でタイプ時または保存時どちらのタイミングでチェックするかを設定できます。

- [textlint - pluggable linting tool for text and markdown](https://textlint.github.io/)
- [azu/vscode-textlint-example: VS Codeを使ったtextlint projectのサンプル](https://github.com/azu/vscode-textlint-example)
- [vscode-textlint - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint "vscode-textlint - Visual Studio Marketplace")

textlintはコマンドラインツールですが、他にもAtomやVimなどのエディタやブラウザでも動かすことができます。
詳しくはREADMEの方を見てみてください。

- [https://github.com/textlint/textlint#editors](https://github.com/textlint/textlint#editors)