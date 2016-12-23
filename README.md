# VSCodeでtextlintを使って文章をチェックする

[textlint](https://textlint.github.io/ "textlint")での文章チェックを[VS Code](https://code.visualstudio.com/ "VS Code")上で行う方法についてのメモ。

VSCodeでtextlintを動かく拡張してvscode-textlintがあるのでそれをインストールして動かすまでの流れを見ていきます。
（vscode-textlintでグローバルの設定を使う方法はよくわからなかったので、今回はプロジェクトのローカルにインストールしたtextlintを使うまでの見ていきます）

- [vscode-textlint - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint "vscode-textlint - Visual Studio Marketplace")

## インストール

VSCodeを開き、Cmd+P(Ctrl+P)でコマンドパレットを開いて、拡張をインストールするだけです。

```
ext install vscode-textlint
```

既に、textlintの設定をしているプロジェクト(node_modulesにtextlintやルールがインストールされていて、.textlintrcが配置されている場所)なら、VSCodeでそのディレクトリを開くだけで動作します。

今回は、textlintを設定したプロジェクトを作る所からやってみます。

## プロジェクトの作成

適当なディレクトリを作ります。
今回は、`vscode-textlint-example/`という名前のディレクトリを作ってみます。

以下の実際に作ったリポジトリがあるので、次のコマンドで試すことができます。

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
この辺は、通常のtextlintのインストールと同じなので既に色々な環境でのインストール方法があるので参考にしてみてください。

- [textlint/getting-started.md at master · textlint/textlint](https://github.com/textlint/textlint/blob/master/docs/getting-started.md)
- [textlintで日本語の文章をチェックする | Web Scratch](http://efcl.info/2015/09/10/introduce-textlint/)
- [textlintで日本語テキストの文字校正を試してみた - the code to rock](http://note103.hateblo.jp/entry/2016/06/13/074744)
- [KenshoFujisaki/TextlintAtom: Atomでtextlintを使うための手順](https://github.com/KenshoFujisaki/TextlintAtom)
- [Ubuntu 15.04でtextlint - メモ](http://mickey-happygolucky.hatenablog.com/entry/2016/05/22/141054)

また、textlintはデフォルトでは何もルールを持っていないため何もチェックできません。
そのため、合わせて幾つかルールをインストールしてみます。

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

## VSCodeで開く

後は、作成したプロジェクトをVSCodeで開くだけです。

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