---
title: "migration、fixtureと開発時の起動スクリプトに関する私見"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "django", "開発手法"]
published: true
---

個人的に考えたことをまとめたものなので、一般に言われている知識とかではないです。
コメント欄で「それ間違ってるよ」とか「こう思うとか」とか「あの人はこう言ってるよ」とかなんでも意見もらえるとできると嬉しいです。

(migration と fixture というのは python Django のもの、開発時の起動スクリプトというのは `docker-compose up` を想定しています。)

## migration と fixture について

### migration と fixture についての理想論

- 開発の起点となるブランチの全てコミットは、そのコミットにおいてゼロから migration(スキーマ)と fixture(データ)を使って皆同じ状態で開発を始めることができる。
- DB のスキーマが違う状態で始めたい時はまずその migration を追加したコミットを作って（ブランチを切ったりして）行う。そのコミットにおける最新の migration と違うスキーマで開発することを許容しない。
- データに関しては複数の状態を一つのコミット（スキーマ）で確認したいことを許容する。（つまり fixture は複数種類あって使い分けられる。）<-「このデータがないときはこういう挙動」で、「ある時はこういう挙動」とか確認したいから。
- マージやリバート時にはコンフリクトを矛盾なく解消するのと同様に、マージ・リバート後に DB スキーマが矛盾を引き起こさないことを確認する必要がある。
  - 普通はテストを書く。テストは複数種類の fixture を利用しながら行うことができる。スキーマは複数種類確認する必要はなく、そのコミットにおいて最新のもののみテストすれば良い。

と言うことになるかなと思いました。

### 前述の理想論を守ると起きる事

- レビュー時に常に実装者と同じ状態で動作確認を行うことができる。
- ネカフェなどで毎日違う PC で開発することになっても、開発者が総入れ替えになっても問題が起きず、問題発生時にもいつの commit にでもロールバックすることができる。
- commit=DB スキーマなので、意図しない `migrate` が起こる=もってきた commit が間違ってるで、と言うことになる。

これは厳しい理想で、もちろん手でちょろっと変更を加えたりして commit が指定する状態とは違うもので開発を進める場合も多々あって当然です。
ただ、そこで加えた変更というのは今の自分以外誰にもわからないので、いわゆるサポート外としてやってもらうべきかなと思います。  
他の人のレビューやサポートが欲しければ、それを表す migration や fixture を書いてくださいということです。  
（それでも開発速度を優先させるために migration, fixture なしで回避策みたいなのを用意してやっていくのは小さい規模だったり、一時的なものであることがわかっている部分ならアリかもだけど、一般的には関わる人数が増えたり開発が進んでいくうちに逆にどんどん時間がかかるようになっていってしまう。）

## デフォルトの開発時の起動スクリプトはどうあるべきか

これは前述の理想論に比べるとどちらでも良いという感じがするんだけど、二つの選択肢を比べて考えてみる。

### 選択肢 1

`migrate` と `loaddata fixture` は `docker-compose up` では**起きない**ようにし、README に、

- (開発始めやレビューなどで) commit を移動した際には、まず `migrate` と `loaddata fixture` をしてください。（あるいはそれが起こるスクリプトを起動してください。）
- その後開発・動作確認には `docker-compose up` をしてください。

と書く。

### 選択肢 2

migrate と `loaddata fixture` は `docker-compose up` で**起きる**ようにし、README に、

- 開発・動作確認には `docker-compose up` をしてください。

と書く。

### 選択肢 1 の利点・欠点

- 利点:自分の DB に変更を加えている人には変更を勝手に加えられることがなく安心。（<-上記理想論でいうところサポート外の人が嬉しい）
- 欠点:気にしなければならない操作が増える。（これは特に新規さん、デザイナーさんとかを罠にかけその人やサポートする人の時間を消費しそう。）

### 選択肢 2 の利点・欠点

選択肢 1 の逆で

- 利点:気にしなければならない操作が減る。
- 欠点:自分の DB に変更を加えている人にとって意図しない DB の改変が行われる。

あと、毎度起動するとは言っても 2 回目以降はパスできてるところがほとんどなので、起動速度はあまり変わらないはず。

ということで、選択肢 2 が良さそうな感じがする。

## まとめ

- 開発規模が大きくなってきたら手動更新はやめ、migration, fixture, テストを書きましょう。
- `migrate` と `loaddata fixture` は `docker-compose up` で起きるようにする。
- 変更させたくない人用に `migrate` と `loaddata fixture` が起動しない特別な docker-compose.yml を用意して `docker-compose up -f ファイル名`
  で起動してもらうなどして、回避策を用意する。
  - 詳しくない人・多数向けには全く説明のいらない方の操作をしてもらうことにし、詳しい人・少数向けには少し説明のいる方の操作をしてもらうことにする方針。