---
keywords: multi multisite multilingual translation
---

# サイトとローカライゼーション

Craft 3では、1つインストールするだけで複数のウェブサイトをホストできます。

ドメインが異なったり、異なるテンプレートセットを使用したり、エントリコンテンツの異なるバージョンを持っている、1つ以上のサイトを定義できます。

Craft のマルチサイト機能は、同じパブリッシングチームを持つサイトに向いています。サイトに含めたいセクションだけを有効化できる能力により、エントリレベルでマルチサイトのコンテンツを管理します。

## サイトの作成

Craft のインストールは、1つのデフォルトサイトからはじまります。サイト名はインストール時に定義され、ハンドルは `default` となります。

サイトを追加するには、「設定 > サイト」を使用します。

それぞれのサイトは、次の属性を持っています。

* グループ
* 名前
* ハンドル
* 言語
* これはプライマリサイトですか？
* ベース URL


### サイトグループ

サイトグループでは、言語や種類などの共通点によってサイトをひとまとめに整理できます。

Craft は（デフォルトサイトの名前を付けた）最初のサイトグループを作成し、そのグループにデフォルトサイトを割り当てます。

フィールドグループに似ていて、サイトグループは整理するためだけにあります。

現在のサイトグループの情報には、次のようにアクセスできます。

```twig
Site ID:            {{ currentSite.id }}
Site Handle:        {{ currentSite.handle }}
Site Name:          {{ currentSite.name }}
Site Language:      {{ currentSite.language }}
Is Primary Site?:   {{ currentSite.primary }}
Base URL:           {{ currentSite.baseUrl }}
```


### 言語

サイトの言語を選択すると、日付、時間、および、数字の書式と、翻訳の静的メッセージで使用するための言語を Craft に伝えます。

テンプレート内では、`craft.app.language` 経由で言語設定にアクセスすることもできます。これを条件文で使えます。

```twig
{% if craft.app.language == 'de' %}
    <p>Guten Tag!</p>
{% endif %}
```

あるいは、言語ごとの固有なテンプレートを自動的に含む方法もあります。

```twig
{% include '_share/footer-' ~ craft.app.language %}
```

この例では、読み込まれるテンプレート名は `_share/footer-de` になります。


### プライマリサイト

Craft はデフォルトサイトをプライマリサイトとしてセットします。すなわち、どのサイトをロードするか決定できない場合に Craft がフロントエンドのデフォルトとしてロードします。1つしかサイトがない場合、プライマリサイトを無効にできません。

追加のサイトを作成すると、プライマリサイトを変更できます。Craft は現在のプライマリサイトを自動的に切り替えます。

### サイト URL

すべてのサイトはベース URL を持っていて、Craft がエントリや他のサイトコンテンツの動的なリンクを生成する際に出発点として使用します。

マルチサイトは `https://craftcms.com/` や `https://craftcms.com/de/` のように同じホスト名を共有したり、`https://craftcms.com/` や `https://de.craftcms.com/` のように異なるホスト名を持つこともできます。

異なるホスト名でサイトを作成したい場合、それに対するトラフィックを処理するようサーバーを設定しなければなりません。ホスト名は現在のサイトと同じウェブルート（`web/`）を指すことも、独自の別のウェブルートにすることもできます。後者の場合、`.htaccess`、および、`index.php` ファイルを新しいウェブルートへ確実にコピーしてください。

::: tip
`https://site-a.com` や `https://site-b.com` のような異なるルートドメインを使用するマルチサイトを持つ場合、Craft の [license enforcements works](https://craftcms.com/support/license-enforcement) の仕組みによって、_すべて_ のサイトのためにアクセスする Craft コントロールパネルのドメインをその中の1つから選択できます。
:::

::: warning
サイトのベース URL を定義する場合、`@web` エイリアスを使用しないでください。それは[キャッシュポイズニング](https://www.owasp.org/index.php/Cache_Poisoning)の脆弱性をもたらすことができ、リクエストされたサイトを Craft が確実に判断することができなくなります。
:::

## すべての有効サイトにエントリを広げる

それぞれのチャンネルセクションの設定には、すべてのサイトにエントリを広げるためのオプションがあります。これはデフォルトで有効になっていて、シングルやストラクチャーセクションでは、必ず有効な状態となります。

有効にすると、Craft は送信されたコンテンツを利用して、そのセクションで有効になっているそれぞれのサイトに新しいエントリを作成します。

セクションのコンテンツをサイトごとに分離したい場合、そのセクションにあるこのオプションを無効にします。

## 新しいサイトの設定

これは、Craft で新しいサイトをセットアップするステップを段階的に説明するショートガイドです。このガイドは、すでに Craft がインストールされていて、デフォルトのセットアップや設定が済んでいることを前提としています。

### ステップ 1：設定でサイトを作成

最初のステップは、インストール済みの Craft の設定で新しいサイトを作成することです。

1. 「設定 > サイト」に移動し、「新しいサイト」ボタンをクリックします。
2. ドロップダウンメニューからサイトが所属するグループを選択します。グループの選択により、サイトの機能に影響することはありません。
3. 名前を付けてください。Craft はコントロールパネルのサイト名を利用し、`{{ siteName }}` を使ってテンプレート内に表示することもできます。
4. サイト名をベースにして、Craft はサイトのハンドルを生成します。あなたが望むなら、ハンドルを編集できます。テンプレート内でこのサイトを参照するために、サイトハンドルを利用します。
5. サイトの言語を選択してください。（言語の使い方の詳細は、上記を参照してください）
6. このサイトをプライマリサイトにする場合は、「これはプライマリサイトですか？」をオンにします。これで、有効になります。
7. 「このサイトには独自の基本 URL があります」のチェックボックスをオンにし、ベース URL を入力します。この例では `https://beta.craftcms.com` になります。
8. 新しいサイトを保存します。

### ステップ 2：テンプレートディレクトリの作成

新しいサイト向けに、テンプレートディレクトリとテンプレートを作成します。

サイトハンドルを名前に付けたテンプレートディレクトリ（例： `templates/default` と `templates/beta`）を持たせることをオススメします。それぞれのサイトのテンプレートディレクトリに、サイト固有のテンプレートを保管します。

### ステップ 3：セクションのアップデート

1. 新しいサイトで有効にしたいセクションに移動し、「サイト設定」テーブルを利用してサイトを有効にします。
2. 各セクションの新しいサイト向けに、エントリ URI 形式、テンプレート、および、デフォルトのステータスを定義します。
3. すべての有効サイトにエントリを広げるかどうかを選択します。チェックを入れると、そのセクションのエントリはセクションが有効になっているすべてサイトで保存されます。チェックされていない場合、エントリは作成元のサイトのみに保存されます。

### ステップ 4：フィールドの翻訳方法の定義

デフォルトでは、カスタムフィールドはすべてのサイトで同じ値を保存するように設定されています。サイトごとにユニークな値を持つフィールドの場合、[翻訳方法](fields.md#translation-methods)設定を編集する必要があります。

### ステップ 5：設定のテスト

新規または既存のエントリを利用し、セクション、フィールド、および、翻訳方法の設定が期待通りに機能しているかテストします。

### ステップ 6：アセットボリューム設定の確認

ローカルのアセットボリュームがある場合、それらのアセットがそれぞれのサイトから利用可能であることを確認する必要があります。

* 「ファイルシステムのパス」設定は、相対的（`uploads/images/`）であるべきです
* URL の設定は、 相対的（`/images`）であるべきです

### ステップ 7：ウェブサーバーと DNS の設定

1. ドメイン（例： `beta.craftcms.com`）が `web` ディレクトリを参照するよう、ウェブサーバーを設定します。Craft はブラウザがどのサイトを要求しているかを自動的に検出します。
2. ドメインがウェブサーバーを指すよう、DNS レコードをアップデートします。

## ローカライズされたサイトの設定

これは、Craft のマルチサイト機能と翻訳サポートを利用して、一般的に必要となるすべてのステップを段階的に説明するガイドです。

### ステップ 1：サイトと言語の定義

ローカライズされたサイトを作成する最初のステップは、サポートが必要な言語を決定することです。その後、[Craft のマルチサイトをセットアップする際のガイド](sites.md)を利用して、サポートされる言語ごとの新しいサイトを Craft で作成します。

### ステップ 2：セクションのアップデート

言語向けに新しいサイトを作成したら、それぞれのセクションで新しいサイトを有効にします。「設定 > セクション」で、ローカライズしたいサイトを含むセクションの設定に移動し、サイト設定でサイトを有効にします。そのサイトをどのような URL 構造にしたいかを反映するため、（チャンネルとストラクチャーセクション向けに）エントリ URI 形式、または（シングルセクション向けに）URI を記入します。

### ステップ 3：翻訳可能なフィールドの定義

「設定 > フィールド」で、翻訳可能にするフィールドを選択します。翻訳方法で「各言語に対して翻訳する」を選択します。

Craft は、それぞれのエントリにあるこのフィールドのコンテンツを言語単位で更新することを許可します。

### ステップ 4：テンプレートのアップデート

特定のサイトからだけ配信したいテンプレートがある場合、テンプレートフォルダに新しいサブフォルダを作成し、サイトのハンドルをフォルダ名にして、その中に手テンプレートを配置します。

例えば、ドイツ語のサイトのホームページを独自のテンプレートにする場合、テンプレートフォルダを次のように設定します。

```treeview
templates/
├── index.twig (default homepage template)
└── de/
    └── index.twig (German homepage template)
```

`craft.app.language` を利用して、言語に依存するテンプレートの特定パーツを切り替えます。

```twig
{% if craft.app.language == 'de' %}
    <p>I like bread and beer.</p>
{% endif %}
```

テンプレート全体の文字列に Craft の[静的翻訳](#static-message-translations)サポートを利用することもできます。

```twig
{{ "Welcome!"|t }}
```

### ステップ 5：投稿者にサイトへのアクセス権を付与

インストールされた Craft にサイトを追加すると、Craft はユーザーがコンテンツの編集を試みるたびに、サイトのパーミッションをチェックしはじめます。デフォルトでは、ユーザーやグループはどのサイトにもアクセスできないため、それらを割り当てる必要があります。

ユーザーグループやユーザーアカウントを編集するとき、すべてのサイトのリストに新しいサイトの「権利」セクションが表示されます。権限を与えたいものを割り当ててください。


## 静的メッセージの翻訳

ほとんどのウェブサイトやアプリには、テンプレートや PHP ファイルにハードコードされたいくつかの UI メッセージを持ちます。CMS のコンテンツによって動的に定義されていないため、それらは「静的メッセージ」と呼ばれます。

多言語のサイトやアプリを構築している場合、CMS 主導のコンテンツのようにそれらのメッセージも翻訳可能にする必要があるでしょう。

そのため、Craft は Yii の[メッセージ翻訳](https://www.yiiframework.com/doc/guide/2.0/en/tutorial-i18n#message-translation)機能を採用し、 特別な翻訳カテゴリを事前に定義しています。

- `site` はプロジェクトに属するメッセージに使用されます。
- `app` はコントロールパネルのメッセージに使用されます。
- それぞれのプラグインは、プラグインのハンドルに基づいて独自のカテゴリも取得します。

### メッセージの準備

最初のステップは、すべての静的メッセージをトランスレータを通して実行することです。テンプレートを操作している場合、[translate](dev/filters.md#translate-or-t) フィルタ（`|t`）を使用します。PHP コードを操作している場合、[Craft::t()](yii2:yii\BaseYii::t()) を使用します。

::: code
```twig
{# old #}
<a href="/contact">Contact us</a>

{# new #}
<a href="/contact">{{ 'Contact us'|t }}</a>
```
```php
// old
echo 'Contact us';

// new
echo Craft::t('site', 'Contact us');
```
:::

### 翻訳の提供

翻訳のためのメッセージを準備したら、実際の翻訳を提供する必要があります。

そのために、プロジェクトのベースディレクトリに `translations/` と呼ばれる新しいフォルダを作成し、その中に対象言語の ID を名前とした新しいフォルダを作成します。その中に、メッセージを作成したい翻訳カテゴリの名前をつけたファイルを作成します（プロジェクトメッセージのための `site.php`、Craft のコントロールパネルのメッセージを上書きするための `app.php`、または、プラグインのメッセージを上書きするための `<plugin-handle>.php`）。

例えば、プロジェクトのメッセージをドイツ語に翻訳する場合、プロジェクトのディレクトリ構造は次のようになります。

```treeview
my-project.test/
├── config/
├── ...
└── translations/
    └── de/
        └── site.php
```

次に、テキストエディタで `site.php` を開き、ソースメッセージを翻訳メッセージにマップする配列を返すようにします。

```php
<?php

return [
    'Contact us' => 'Kontaktiere uns',
];
```

これで、ドイツ語サイトのメッセージ翻訳を処理する際、「Contact us」が「Kontaktiere uns」に置換されます。

#### メッセージパラメータ

静的メッセージは[プレースホルダ値](https://www.yiiframework.com/doc/guide/2.0/en/tutorial-i18n#message-parameters)を持つことができます。例えば、

```php
<?php

return [
    'Welcome back, {name}' => 'Willkommen zurück {name}',
];
```

メッセージの翻訳時にプレースホルダ値を動的な値に置き換えるため、[translate](dev/filters.md#translate-or-t) フィルタや [Craft::t()](yii2:yii\BaseYii::t()) を呼び出す際、引数 `params` を渡します。

::: code
```twig
<a href="/contact">{{ 'Welcome back, {name}'|t(params = {
    name: currentUser.friendlyName,
}) }}</a>
```
```php
echo Craft::t('site', 'Welcome back, {name}', [
    'name' => Craft::$app->user->identity->friendlyName,
]);
```
:::
