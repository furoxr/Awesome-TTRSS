# 🐋 Awesome TTRSS

[![Docker Pulls](https://img.shields.io/docker/pulls/wangqiru/ttrss.svg)](https://hub.docker.com/r/wangqiru/ttrss)
[![Docker Stars](https://img.shields.io/docker/stars/wangqiru/ttrss.svg)](https://hub.docker.com/r/wangqiru/ttrss)

[![Docker Automated build](https://img.shields.io/docker/automated/wangqiru/ttrss.svg)](https://hub.docker.com/r/wangqiru/ttrss/builds)
[![Docker Build Status](https://img.shields.io/docker/build/wangqiru/ttrss.svg)](https://hub.docker.com/r/wangqiru/ttrss/builds)

[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FHenryQW%2Fdocker-ttrss-plugins.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2FHenryQW%2Fdocker-ttrss-plugins?ref=badge_shield)

## 概要

[Tiny Tiny RSS](https://tt-rss.org/) は PHP ベースのフリーでオープンソースな RSS アグリゲーターリーダーです。🐋 Awesome TTRSS は、簡単なデプロイ方法と追加のプラグインを提供することで、Tiny Tiny RSS の「コンテナ化」されたワンストップソリューションを目指しています。

## デプロイ

**より良いユーザーエクスペリエンスのために、このイメージは postgres データベースのみをサポートしています。** [tag 3.5_mysql_php5](https://github.com/HenryQW/docker-ttrss-plugins/tree/3.5_mysql_php5) から mysql のサポートは停止しました。

### Docker を使用してデプロイ

```dockerfile
docker run -it --name ttrss --restart=always \
-e SELF_URL_PATH=[TTRSSのインスタンスURL] \
-e DB_HOST=[データベースホスト] \
-e DB_PORT=[データベースポート] \
-e DB_NAME=[データベース名] \
-e DB_USER=[データベースユーザー名] \
-e DB_PASS=[データベースパスワード] \
-p [コンテナの外部ポート]:80 \
-d wangqiru/ttrss
```

### docker-compose を使用してデプロイ

[docker-compose.yml](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml) には 4 つのイメージが含まれています:

1. [TTRSS](https://hub.docker.com/r/wangqiru/ttrss)
1. [PostgreSQL](https://hub.docker.com/r/sameersbn/postgresql)
1. [Mercury Parser API](https://hub.docker.com/r/wangqiru/mercury-parser-api)
1. [OpenCC API](https://hub.docker.com/r/wangqiru/opencc-api-server)

#### 手順

1. [docker-compose.yml](https://github.com/HenryQW/docker-ttrss-plugins/blob/master/docker-compose.yml) を任意のディレクトリにダウンロードします。
1. `docker-compose.yml` の設定を変更します。postgres のユーザーパスワードの変更を必ず行ってください。
1. ターミナルで同じディレクトリに移動し、`docker-compose up -d` を実行してデプロイが完了するのを待ちます。
1. デフォルトでは 181 ポートで TTRSS にアクセスします。デフォルトのアカウントは `admin`、パスワードは `password` です。初回アクセス時に変更してください。
1. `wangqiru/mercury-parser-api` と `wangqiru/opencc-api-server` は高度な機能をサポートするために追加されたオプションのサービスコンテナです。削除しても TTRSS の基本機能には影響しません。

### サポートされている環境変数のリスト

* SELF_URL_PATH: TTRSS のインスタンスURL
* DB_HOST: データベースホスト
* DB_PORT: データベースポート
* DB_NAME: データベース名
* DB_USER: データベースユーザー名
* DB_PASS: データベースパスワード
* ENABLE_PLUGINS: システムレベルで有効にするプラグイン名。`auth_internal` はログインプラグインとして必ず有効にする必要があります。
* SESSION_COOKIE_LIFETIME: ウェブ版でログインする際の cookie の有効期限（時間単位）。デフォルトは 24 時間です。

## プラグイン

### [Mercury 全文取得](https://github.com/HenryQW/mercury_fulltext)

全文コンテンツを抽出するプラグインで、独立した Mercury Parser API サーバーと連携して使用します。[docker-compose の例](#docker-compose-を使用してデプロイ) には [HenryQW/mercury-parser-api](https://github.com/HenryQW/mercury-parser-api) サーバーが含まれています。

#### 設定手順

1. 設定で `mercury-fulltext` プラグインを有効にします。
    ![Mercury を有効にする](https://share.henry.wang/92AGp5/x9xYB93cnX+)
1. 設定で Mercury Parser API の URL を入力します。
    ![Mercury Parser API の URL を入力](https://share.henry.wang/KFrzMD/O2xonuy9ta+)

### [Fever API](https://github.com/HenryQW/tinytinyrss-fever-plugin)

Fever API のサポートを提供します。

**このプラグインはデフォルトでシステムプラグインとして有効になっています。**

#### 設定手順

1. 設定で API を有効にします。
    ![API を有効にする](https://share.henry.wang/X2AnXi/bVVDg9mGDm+)
1. プラグイン設定で Fever パスワードを設定します。
    ![Fever パスワードを設定](https://share.henry.wang/HspODo/xRSbZQheVN+)
1. Fever をサポートするリーダーでは、`https://[あなたのアドレス]/plugins/fever` をサーバーアドレスとして使用します。あなたのアカウントとステップ 2 で設定したパスワードでログインします。
1. このプラグインは未加工の MD5 でパスワードを暗号化して通信するため、https を有効にすることを強く推奨します。[Let's Encrypt](https://letsencrypt.org/) で無料の SSL 証明書を取得できます。

### [OpenCC 繁体字・簡体字変換](https://github.com/HenryQW/ttrss_opencc)

[OpenCC](https://github.com/BYVoid/OpenCC) を使用して TTRSS に中国語の繁体字と簡体字の変換機能を提供するプラグインです。独立した OpenCC API サーバーと連携して使用します。[docker-compose の例](#docker-compose-を使用してデプロイ) には [HenryQW/OpenCC.henry.wang](https://github.com/HenryQW/OpenCC.henry.wang) サーバーが含まれています。

#### 設定手順

1. 設定で `opencc` プラグインを有効にします。
    ![opencc を有効にする](https://share.henry.wang/EvN5Nl/2RHNnMV2iP+)
1. 設定で OpenCC API の URL を入力します。
    ![OpenCC API の URL を入力](https://share.henry.wang/JdJeUB/vIsRBk3EXn+)

デモサーバーは、利用可能性は保証されません：[https://opencc.henry.wang](https://opencc.henry.wang) または [http://opencc2.henry.wang](http://opencc2.henry.wang)。

### [FeedReader API](https://github.com/jangernert/FeedReader/tree/master/data/tt-rss-feedreader-plugin)

FeedReader API のサポートを提供します。

システムプラグインとして、`api_feedreader` を **ENABLE_PLUGINS** 環境変数に追加して有効にします。

使用ガイドは [FeedReader API](https://github.com/jangernert/FeedReader/tree/master/data/tt-rss-feedreader-plugin) を参照してください。

### [News+ API](https://github.com/voidstern/tt-rss-newsplus-plugin/)

Android アプリ [News+](http://github.com/noinnion/newsplus/) と iOS アプリ [Fiery Feeds](http://cocoacake.net/apps/fiery/) により高速な同期を提供します。

システムプラグインとして、`api_newsplus` を **ENABLE_PLUGINS** 環境変数に追加して有効にします。

使用ガイドは [News+ API](https://github.com/voidstern/tt-rss-newsplus-plugin/) を参照してください。

### [Feediron](https://github.com/feediron/ttrss_plugin-feediron)

記事の DOM を操作する機能を提供するプラグインです。

使用ガイドは [Feediron](https://github.com/feediron/ttrss_plugin-feediron) を参照してください。

## テーマ

### [Feedly](https://github.com/levito/tt-rss-feedly-theme)

![Feedly](https://share.henry.wang/f3WNje/Q7RoLBSUFp+)

### [RSSHub](https://github.com/DIYgod/ttrss-theme-rsshub)

![RSSHub](https://share.henry.wang/E5Lifa/1ykvdTWuew+)

## 使用の提案

* [RSSHub](https://docs.rsshub.app/) を使用して、より多くの興味深い購読ソースを発見することをお勧めします。
* iOS および macOS ユーザーには、内蔵された [Fever API エミュレーションプラグイン](#fever-api) が [Reeder 4](http://reederapp.com/) のバックエンドサポートを提供します。
* Linux ユーザーには、内蔵された [FeedReader API](#feedreader-api) が [FeedReader](https://jangernert.github.io/FeedReader/) のバックエンドサポートを提供します。

## サポートとヘルプ

[GitHub issue](https://github.com/HenryQW/docker-ttrss-plugins/issues) を通じて問題を提出してください。できるだけ早く回答します。

## ライセンス

MIT

[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2FHenryQW%2Fdocker-ttrss-plugins.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2FHenryQW%2Fdocker-ttrss-plugins?ref=badge_large)
