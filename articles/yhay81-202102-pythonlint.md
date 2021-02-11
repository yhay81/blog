---
title: "2021年Python開発リンター導入のベストプラクティス"
emoji: "🐍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "black", "flake8", "isort", "mypy"]
published: true
---

## この記事は何？

Python 開発における

- リンターの利点
- 数ある Python リンターの違い・特徴
- 導入方法
- おすすめの設定
- リンターを無視する方法

を紹介しています。

昨今の状況を踏まえた上で、多くのプロジェクトで適用できる構成になっていると思います。

## リンターって何？

**コードをチェック・解析するツール**のことです。**静的解析ツール**とも呼ばれます。参考）[lint (Wikipedia)](https://ja.wikipedia.org/wiki/Lint)
導入することで、以下の効果が得られます。

- バグの発生が減る
- 保守性の高いコードを書けるようになる
- チーム内でのコーディング規約（コードの書き方のルール）を自動的に守れるようになる
- リンターが簡易自動テストのように働く場面もある

本稿では「チェックするだけでなく、自動であるべき形に整形までしてくれる」`フォーマッター`についても、`リンター`として扱い紹介します。

## リンターが導入されない理由

リンターは便利なもののようなのに、導入されていない Python プロジェクトは多くあります。  
ここでは導入されない理由あるあるを紹介します。

### 導入・設定方法が誰もわかんないし調べるのも大変

- 確かに npm 環境 などに比べると Python でのリンター導入はわかりにくい印象です。そこでこの記事です！

### リンターに合わせてコードを書くために逆に時間がかかる

- 書いてる時はそう思うかもしれませんが、コードは書く時間より読んで頭で整理する時間の方が圧倒的に多いです！特に他の人も読むコードはこの傾向が顕著で、**時間をかけてでも良いコードを書くことが時短**に繋がります。
- また、すぐに無意識に書けるようになります。エンジニアとしての成長です 💪
- 設定やオプションでチェックしない項目を設定することもできます。チームの有識者が最初にプロジェクトにあった形で設定しておけば、他のメンバーは何も気にしなくても良いコードが書けるようにリンターが導いてくれます。

### 時々どうしてもチェック通らないパターンが出てきて詰んだから辞めた

- **Python のコメントを特別な形式で書くことで個別にスキップすることができます**。この方法はチームメンバー全員に周知して利用してもらうと良いです。
- 特例として無視「すべき」場合はもちろん、「多分いい書き方あるけどすぐにはわからん」という時も積極的に利用して良いと思います。コメントが残っているため検索しやすく、いつか誰か有識者が解決してくれたりします。
- あまりにも頻出するチェックが通らないパターンは、設定で全部オフにすることを検討します。

### 「リンターが導入されない理由」のまとめ

リンターが導入されない理由を潰す形で導入できる理由を述べてきましたが、とはいえやはりゼロからの導入には多少の時間はかかります。  
「本当に簡易なプロジェクトである」「知識のあるメンバーがいない」などの理由で導入しない方が速いような場面もありえます。  
プロジェクトの性質に合わせることが大事です。

## Python のリンターは種類が多すぎ。結局何入れたらいいの？

Python のエコシステムはパッケージ管理ツール(pip, pipenv, poetory, pyflow)などもそうですが、今なお多様性があり圧倒的なデファクトスタンダード（事実上の業界標準）が存在しないものが多い状況です。  
（多様性があることは良い部分もありますが、言語の人気の割にエコシステムに関する整理された情報は多くなく、こういうところが他言語出身者の python 嫌いを産んでいる気がします、、、）

リンターも同様で種類が多く選ぶのが大変なのですが、選考基準の一つとして「ある程度ユーザーがいて、保守され続けそうなもの」を取ると良いです。ここではおすすめのものを紹介します。

### フォーマッター

black(整形) と isort(import のソート)を紹介します。両方の導入を勧めます。

#### [black](https://black.readthedocs.io/en/stable/index.html)

フォーマッターについてはデファクトスタンダードが出来上がってきたと言えると思います。
それが [black](https://black.readthedocs.io/en/stable/index.html) です。

しかし black にも批判意見がないわけではなく、特に以下の批判が目立ちます。

- 個別にルールを設定できない。
- 他の有名リンターのデフォルト設定と矛盾するルールがある。

black 開発チームはこれらの批判に対し、[PEP8 (python 公式文章の 1 つ) に準拠しているのは black である](https://black.readthedocs.io/en/stable/compatible_configs.html)と述べています。
僕自信は black を支持していて、他を black に合わせる運用を好んでいます。

#### [isort](https://pycqa.github.io/isort/)

isort は python のモジュール等を import する行(`import requests` など)をソートしてくれるツールです。
PEP8 では以下の順になるよう決められていて、それに加えて各行もアルファベット順にソートしてくれます。

```
標準ライブラリ
サードパーティに関連するもの
ローカルな アプリケーション/ライブラリ に特有のもの
```

些細な部分なのでわざわざ導入するほどでもないのかもしれませんが、地味に揺れが気になったりすることがあるためか結構多くのプロジェクトで導入されている印象です。

### 総合的なリンター

Flake8, pylint, Prospector を紹介します。これらは重複する機能を持つので、少なくとも 1 つの導入を勧めます。

#### [Flake8](https://flake8.pycqa.org/en/latest/)

Flake8 は pycodestyle(pep8 スタイルチェック), pyflakes(論理エラーのチェック), mccabe(複雑度チェック)をまとめたツールです。
また、プラグイン方式でルールの追加ができるのですが、プラグインの開発はあまり活発でないため期待はできません。
python のリンターとしては現在最もよく見かけるもののように思います。

#### [pylint](https://www.pylint.org/)

pylint は単体で pep8 スタイルチェックや論理エラーチェックなどを行なってくれるツールです。
設定ファイルによってかなり柔軟に設定を変えることができます。
`pylint --generate-rcfile > .pylintrc` コマンドでデフォルトの設定が生成できこれを書き換えていくのですが、項目が多く眩暈がします。

#### [Prospector](http://prospector.landscape.io/en/master/)

Prospector はさらに多くのリンターを抱き合わせることができるリンターまとめツールです。
isort,pylint も入っているし、後述の mypy や bandit もオプションで追加することができます。
多くのリンターを個別に導入すると設定ファイルや実行方法が乱雑になってしまい、混乱してしまいますが、Prospector を導入すると一度に`prospector .`のコマンドだけで全てを実行することができるようになります。
僕は期待しているツールなのですが、開発スピードはあまり早くなく、現状依存する isort のバージョンが古いままのため black と不整合を起こしてしまったりします。

### 型チェック

#### [mypy](http://www.mypy-lang.org/)

mypy は型チェックを行なってくれるツールです。
Python は動的な型を持つ言語ですがオプションとして TypeHint と呼ばれる方法で型情報を付与することができます。（参考)[PEP484](https://www.python.org/dev/peps/pep-0484/)
Python の TypeHint の方法は Python3.9 で拡張されましたが、2021/1 の v0.800 アップデートで mypy もその拡張に対応しました。

### セキュリティチェック

#### [Bandit](https://bandit.readthedocs.io/en/latest/)

bandit はセキュリティ的に問題がありそうな箇所を判定してくれるリンターです。

### Python のリンターもっと紹介

::: details VSCode にデフォルトで設定項目が存在するもの

- [black](https://black.readthedocs.io/en/stable/): python 公式管理のフォーマッター
- [yapf](https://github.com/google/yapf): google 製フォーマッター
- [autopep8](https://github.com/hhatto/autopep8): フォーマッター

- [pylint](https://www.pylint.org/): リンター
- [Flake8](https://flake8.pycqa.org/en/latest/): リンターまとめツール
- [Prospector](http://prospector.landscape.io/en/master/): リンターまとめツール
- [pylama](https://github.com/klen/pylama): リンターまとめツール (開発が実質止まっている)

- [Jedi](https://jedi.readthedocs.io/en/latest/): リンター＋自動補完
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance): pyright を利用した Microsoft 製の VSCode 拡張の自動補完ツール

- [mypy](http://www.mypy-lang.org/): 型チェック
- [Bandit](https://bandit.readthedocs.io/en/latest/): セキュリティ項目のチェック
- [pycodestyle](https://pycodestyle.pycqa.org/en/latest/): (旧 pep8:PEP8 は文章の名前なのでツールにこの名前は紛らわしかった)リンター。Flake8 に入っている。
- [pydocstyle](http://pydocstyle.org/): ドキュメントコメント指摘

:::

::: details PyCQA が管理している有名リンター

[PyCQA](https://github.com/PyCQA) は有名リンターを１箇所に集めて管理している GitHub Organization です。
前述のリストの中では pylint,prospector,mypy,Bandit,pycodestyle,pydocstyle がこの団体で管理されています。

- [isort](https://pycqa.github.io/isort/): import のソートだけするフォーマッター
- [pyflakes](https://github.com/PyCQA/pyflakes): 論理エラーをチェックするリンター。Flake8 に入っている。
- [mccabe](https://github.com/pycqa/mccabe): プログラムの複雑さの指標である McCabe のチェックを行う。Flake8 に入っている。

:::

## 導入方法

ここでは設定項目の詳細にまでは触れず、おすすめのものがとりあえず問題なく導入できるまでについて説明します。

### コマンドラインで実行する方法

紹介したツールは基本的にコマンドラインで実行することができ、設定項目をオプションとして渡して実行します。
しかし、オプションも含め長いコマンドを打つのは大変だし、チームメンバーにどういうオプションで実行していくか伝えるのも面倒です。
そこでリポジトリ内にコマンドが記載されたスクリプトを置くことがあります。(npm 環境の場合、package.json の scripts の項目に書くようなイメージ)
これをどう実現するかは言語ごとに傾向があって python の場合、npm ほどデファクトスタンダードな方式があるわけではないですが、僕が OSS などを見ていて目にするのは **Makefile** を設置する方法と **tox** を利用する方法です。

Makefile はファイルにコマンドのリストを記述しておくと `make lint` などのように簡単に実行できて良いのですが、
tox ではさらにそのコマンドの実行環境を独立に作成してくれるため、開発環境がチームで揃っていなかったり、環境を汚したくないような場面でおすすめです。
（後述の VSCode 連携のためにはどこかしらには lint プログラムを入れる必要がありますが、、）

#### tox

tox を利用するために tox をインストールし、tox.ini という設定ファイルを作ります。

```sh
pip install tox
```

ここでは今回紹介してきたリンターを実行するためのおすすめの tox.ini を紹介し、少しその意味を紹介するのに留めます。
tox は複数の Python バージョンでのテストの実行やドキュメントの作成なども行うことができるので詳しくは[tox 公式ドキュメント](https://tox.readthedocs.io/en/latest/)を見てください。

```ini:tox.ini
[tox]
envlist =
    py36
    lint
    strictlint

# tox -e py36 で実行するための内容。
[testenv]
deps =
    -rrequirements.txt
    pytest
commands =
    pytest -rsfp

# tox -e lint で実行するための内容。
[testenv:lint]
deps =
    black
    flake8
    isort
    mypy
commands =
    isort .
    black .
    flake8 .
    mypy --ignore-missing-imports .

# tox -e strictlint で実行するための内容。エラーでも途中終了せず最後まで実行はする。
[testenv:strictlint]
ignore_errors = true
deps =
    bandit
    flake8
    mypy
commands =
    bandit --exclude ./.tox,./**/tests --recursive .
    flake8 --ignore= .
    mypy --ignore-missing-imports --strict .

# 設定項目
[isort]
multi_line_output = 3
include_trailing_comma = True
force_grid_wrap = 0
use_parentheses = True
ensure_newline_before_comments = True
line_length = 88

[flake8]
max-line-length = 119
exclude =
    .git
    __pychache__
    .tox
    venv

```

#### 1 行の最大の文字数について

PEP8 では python のプログラムの適切な 1 行あたりの文字数は 79 文字となっていますが、これは小さい画面が主流であった古い時代のルールであるという批判があります。
black では 88 文字をデフォルトの設定としていて、僕は black の設定に基本的に乗っ取る方針ではありますが、これでも（特に長い str のハードコードなどで）不便に感じることが多いです。
そこで、black が自動でフォーマットする 88 文字制限はそのまま尊重し、black がフォーマットできない長い str のハードコードなどについてはより長い 119 文字までを許容する設定にしています。
119 文字というのは GitHub のコードレビューが表示できる長さで、この文字数を支持している意見はそこそこ目にするように思います。参考）[GigchainDB](http://docs.bigchaindb.com/projects/contributing/en/latest/cross-project-policies/python-style-guide.html#maximum-line-length), [stackoverflow#88942](https://stackoverflow.com/questions/88942/why-does-pep-8-specify-a-maximum-line-length-of-79-characters)
この設定にすることで、ほとんどの箇所で black の 88 文字に自動でフォーマットしてもらいながら手動で短くしないといけない場面では 119 文字まで許容できるため、手動でフォーマットしないといけない箇所を抑えることができます。

### VSCode と連携する方法

おすすめの VSCode の設定を紹介する。
以下を `settings.json` に書き込むか、
設定を開いて`python.formatting.provider`などで検索すると出てくる項目で "black"というように選択などして設定できる。

```json:settings.json
{
  "python.formatting.provider": "black",
  "python.languageServer": "Pylance",
  "python.linting.flake8Args": ["--max-line-length 119"],
  "python.linting.flake8Enabled": true,
  "python.linting.mypyEnabled": true
}
```

リンターとはあまり関係がない項目もあるが、python 関連では僕はさらに以下のような設定を入れている。

```json:settings.json
{
  "editor.rulers": [88, 119],
  "pylance.insidersChannel": "daily",
  "python.analysis.completeFunctionParens": true,
  "python.analysis.completeFunctionParens": true,
  "python.analysis.logLevel": "Warning",
  "python.analysis.memory.keepLibraryAst": true,
  "python.autoComplete.addBrackets": true,
  "python.diagnostics.sourceMapsEnabled": true,
  "python.insidersChannel": "daily",
  "python.linting.banditEnabled": true,
  "python.terminal.activateEnvInCurrentTerminal": true,
  "python.testing.pytestEnabled": true
}
```

### 強制的にチェックさせる方法

失敗しているコードを main ブランチに取り込ませないためにリンターの実行を強制する場合、実行できるタイミングは 2 つあります。

- ローカル開発中のコミット時にコミットしようとすると自動的にリンターが実行され、失敗するとコミットできないようにする。
- GitHub 上で PR を投げたタイミングで自動テスト（いわゆる CI）が（GitHub Actions などの機能を利用し）実行され、失敗するとマージできないようにする。

かなり時間のかかるテストを自動化したい場合、コミットごとに実行され待たされるのは大変なので後者を選ぶ場合が多いです。
しかし、リンター程度の数秒で終わるものであれば、前者の方が（失敗した場合にすぐ気付きすぐ対応できるという意味で）便利です。
今回はコミット前に実行させる方法を紹介します。

#### コミット前に実行させる方法

（ここで紹介するコミット前に実行する方法に関しては OSS でも見かけることが少なく、一般的な方法とは呼べないかもしれません。npm 環境では husky を用いて行う方法が一般に知られていて多くのリポジトリでそれを見ることができるので、commit 前にリントすること自体は普及してもおかしくなさそうなのですが、python 界隈では方法の確立だけでなくそれ自体が行われていることが少ない印象です。）

git の hook 機能を用いて commit する度に lint を行わせることができます。
.git/hooks/pre-commit というファイルに行いたい動作を書けば良いのですが、.git ディレクトリは共有されないので、今回は python の pre-commit というパッケージを利用する方法を紹介します。（git の基本機能の pre-commit と python のパッケージの pre-commit が同じ名前なのでややこしい。）

python の pre-commit パッケージでは以下のような設定ファイルを書き、`pre-commit install`というコマンドを打つことで`.git/hooks/pre-commit`のファイルをうまく作成してくれます。

```yaml:.pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: isort
        name: isort
        entry: .tox/lint/bin/isort --sp=tox.ini
        language: system
        types: [python]
      - id: black
        name: black
        entry: .tox/lint/bin/black
        language: system
        types: [python]
      - id: flake8
        name: flake8
        entry: .tox/lint/bin/flake8 --config=tox.ini
        language: system
        types: [python]
      - id: mypy
        name: mypy
        entry: .tox/lint/bin/mypy --ignore-missing-imports
        pass_filenames: false
        language: system
        types: [python]
```

ここで、これらのリンターは個別にその場で install しながら利用することもできるのですが、今回はせっかく設定等も作りこんだ tox 環境のものをそのまま使うようにしました。
そのために事前に tox を一度起動させる必要があるので、pre-commit install と合わせてインストール用のスクリプトも作成しました。
チームメンバーには git clone 後最初の設定時に `bash scripts/init-pre-commit.sh` を叩いてもらうことを想定しています。

ディレクトリ構成

```txt
/
|- scripts/
    |- .pre-commit-config.yaml
    |- init-pre-commit.sh
```

```sh:init-pre-commit.sh
ROOT_DIR=$(cd $(dirname $0);cd ..; pwd)
python -m venv ${ROOT_DIR}/.venv_temp_precommit
source ${ROOT_DIR}/.temp_venv_precommit/bin/activate
pip install pre-commit tox
tox -c ${ROOT_DIR}/tox.ini -e lint
pre-commit install -c ${ROOT_DIR}/.pre-commit-config.yaml
rm -rf ${ROOT_DIR}/.venv_temp_precommit
```

## リンターを（部分的に）無視する方法

それぞれのリンターはたくさんルールを持っていて、そのそれぞれに大抵名前がついています。（例: Flake8 の E731）
前述のようにツール自体の設定でその個別のルールを無視することももちろんできます。
ただ、プロジェクト全体ではルールを効かせたいものの、部分的に１ファイルだとか数行だけを例外的に無視したい場合があります。
JavaScript の eslint における `// eslint-ignore-next-line` のようにソースコード内のコメントでそれに対応することができます。

### black を部分的に無効化する方法

ドキュメント）<https://black.readthedocs.io/en/stable/the_black_code_style.html>

一部無視する場合: 無視したい行を `# fmt: off` と `# fmt: on` で囲む。

### isort を部分的に無効化する方法

ドキュメント）<https://pycqa.github.io/isort/docs/configuration/action_comments/>

ファイルごと無視する場合: `# isort: skip_file` とファイルの先頭に書く。
一部無視する場合: `# isort: skip`, `# isort: off`/`# isort: on`, `isort: split` がある。詳しくは[ドキュメント](https://pycqa.github.io/isort/docs/configuration/action_comments/)

### flake8 を部分的に無効化する方法

ドキュメント）<https://flake8.pycqa.org/en/3.1.1/user/ignoring-errors.html>

ファイルごと無視する場合: `# flake8: noqa` とファイルの先頭に書く。
一部無視する場合: `# noqa` とその行に書く。（特定のルールには`# noqa: E731,E123`のように書く）

### mypy を部分的に無効化する方法

ドキュメント）<https://mypy.readthedocs.io/en/stable/common_issues.html>

ファイルごと無視する場合: `# mypy: ignore-errors` とファイルの先頭に書く。
一部無視する場合: `# type: ignore` とその行に書く。

### bandit を部分的に無効化する方法

ドキュメント）<https://github.com/PyCQA/bandit#exclusions>

一部無視する場合: `# nosec` とその行に書く。

## まとめ

- リンターはいいぞ。
- Makefile や tox でコマンド一発で起動できるようにして、VSCode 連携もして、場合によっては pre-commit でコミット前に強制実行するようにしましょう。GitHub Actions での実行もいいかも。
- おすすめは `black, isort, flake8, mypy` を導入すること。
- 導入したら、リンターのせいで詰まるのを防ぐため、チーム全体にコメントなどによる回避策を共有しよう。
- 導入・設定方法は本文中の該当部分を読んでください。
