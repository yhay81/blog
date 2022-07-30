---
title: "Djangoのバージョンをアップデートする時に"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "django"]
published: true
---

Django をアップデートする時に注意する点についてまとめます。

## Django のバージョンについて

Django は 2 年周期でメジャーバージョンが上がり、その中で例えば、3.0, 3.1, 3.2 というように 3 つのマイナーバージョンが 8 ヶ月ごとにリリースされます。
また、このうち最後の 3.2 は LTS(長期サポート)リリースであり、約 3 年間セキュリティアップデートなのどサポートが継続されます。
[Supported Versions](https://www.djangoproject.com/download/#supported-versions)

現在(2021/02)の最新安定版は `3.1` で開発版は `3.2a1`です。
(マイナーバージョンのバージョンアップリリースをフィーチャーリリース、マイクロバージョンのバージョンアップリリースをパッチリリースとしています。)
[Django のリリースプロセス](https://docs.djangoproject.com/ja/3.1/internals/release-process/)

## Django のバージョンアップに関するドキュメント

公式に[Django の新しいバージョンへの更新](https://docs.djangoproject.com/ja/3.1/howto/upgrade-version/)というドキュメントがあります。

[リリースノート](https://docs.djangoproject.com/ja/3.1/releases/)にてどのような変更が行われてきたかを確認することができます。
[Django Deprecation Timeline](https://docs.djangoproject.com/ja/3.1/internals/deprecation/)にて削除された機能を確認することができます。
Django では機能の削除が行われる場合には最低で 1 つのフィーチャーリリース（マイナーバージョン）でそれが非推奨として警告されるようになります。

## Django のバージョンアップ時の手順

大まかな手順としてはまず

- [リリースノート](https://docs.djangoproject.com/ja/3.1/releases/)
- [Django Deprecation Timeline](https://docs.djangoproject.com/ja/3.1/internals/deprecation/)

にて廃止された機能を利用していないかを確認し、それがあれば対応します。

また、念の為一つのマイナーバージョンごとにアップデートを行い、

```shell
python -Wa manage.py test
```

```shell
PYTHONWARNINGS=always pytest tests --capture=no
```

などでテストを実行することで、非推奨機能が利用されていた場合の警告を見ることができます。
