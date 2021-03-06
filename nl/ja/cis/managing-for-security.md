---
copyright:
  years: 2018
lastupdated: "2018-03-13"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# セキュリティー最適化のための IBM CIS の管理

IBM Cloud Internet Services (CIS) のセキュリティー設定には、トラフィックでの誤検出や検出漏れの影響を回避するように設計された、安全なデフォルト値が含まれています。ただし、これらのデフォルト設定が、あらゆるお客様にとって最善のセキュリティー態勢になるとは限りません。以下の手順を実行して、CIS アカウントが安全でセキュアな方法で構成されるようにしてください。

**推奨事項とベスト・プラクティス**

* プロキシングおよび不明瞭さの拡大により発信元 IP アドレスをセキュアにする
* セキュリティー・レベルを選択的に構成する
* Web アプリケーション・ファイアウォール (WAF) を安全にアクティブ化する

## ベスト・プラクティス 1: 発信元 IP アドレスをセキュアにする

IBM CIS を使用してサブドメインをプロキシー接続すると、応答は IBM CIS に固有の IP アドレスを使用してアクティブに行われるので、すべてのトラフィックが保護されます (例えば、すべてのクライアントはまず CIS プロキシーに接続されるので、発信元 IP アドレスが覆い隠されます)。

### 発信元からの HTTP(S) トラフィックの DNS レコードすべてに対して IBM CIS プロキシーを使用する

発信元 IP アドレスのセキュリティーを改善するには、すべての HTTP(S) トラフィックをプロキシー接続する必要があります。

**違いを確認してください - 非プロキシー・レコードとプロキシー・レコードの照会:**

```
$ dig greycloud.theburritobot.com +short
1.2.3.4 (The origin IP address)

$ dig orangecloud.theburritobot.com +short
104.16.22.6 , 104.16.23.6 (CIS IP addresses)
```

### 非標準の名前を使用して非プロキシーの発信元レコードを覆い隠す
FTP など、IBM CIS でプロキシー接続することができず、依然として発信元 IP を使用しているレコードは、追加の不明瞭さを作成して保護することができます。特に、IBM CIS でプロキシー接続できない発信元のレコードが必要な場合は、非標準の名前を使用してください。例えば、`ftp.example.com` ではなく `[random word or-random characters].example.com` を使用します。この不明瞭さにより、DNS レコードの辞書スキャンが行われても、発信元 IP アドレスが露出する可能性は小さくなります。

### 可能であれば HTTP トラフィックと非 HTTP トラフィックとで異なる IP 範囲を使用する
一部のお客様は、HTTP トラフィックと非 HTTP トラフィックとで異なる IP 範囲を使用して、その HTTP IP 範囲を指し示すすべてのレコードをプロクシー接続できるように、そしてすべての非 HTTP トラフィックを異なる IP サブネットによって覆い隠せるようにしています。

## ベスト・プラクティス 2: セキュリティー・レベルを選択的に構成する
**セキュリティー・レベル**により、**IP レピュテーション・データベース**の機密性が設定されます。IBM CIS は毎月、700 万を超える数の Web サイトから 10 億を超える数の固有の IP アドレスを確認しているので、システムで悪意のあるアクターを識別して、それらのアクターがお客様の Web 資産にアクセスするのを防ぐことができます。悪い相互作用や誤検出を防止するためにも、ドメインごとに**セキュリティー・レベル**を構成して、それぞれの必要に応じてセキュリティーの程度を適切に調節してください。

### 機密性の高い領域のセキュリティー・レベルを高めて「高」にする
総当たり攻撃の試行を減らすために、以下の方法で管理ページやログイン・ページに**「ページ・ルール (Page Rule)」**を追加して、この設定値を高くすることができます。

1. API の URL パターン (`www.example.com/wp-login` など) を使用して**「ページ・ルール (Page Rule)」**を作成します。 
2. **「セキュリティー・レベル (Security Level)」**設定を識別します。
3. 設定に**「高」**のマークを付けます。
4. **「リソースのプロビジョン (Provision Resource)」**を選択します。

### 誤検出を削減するために機密ではないパスや API のセキュリティー・レベルを低くする
以下の方法で、一般ページや API トラフィックに対しては、この設定値を低く設定することができます。 

1. API の URL パターン (`www.example.com/api/*` など) を使用して**「ページ・ルール (Page Rule)」**を作成します。
2. **「セキュリティー・レベル (Security Level)」**設定を識別します。
3. セキュリティー・レベルを**「低 (Low)」**または**「実質的にオフ (Essentially off)」**に切り替えます。
4. **「リソースのプロビジョン (Provision Resource)」**を選択します。

### 「セキュリティー・レベル (Security Level)」設定とは
「セキュリティー・レベル (Security Level)」設定は、特定の IP アドレスがネットワーク上での悪意のある動作により取得した脅威スコアを使用して調整されます。10 より大きい脅威スコアは「高」と認識されます。

* **高**: 0 より大きな脅威スコアが検査対象となります。
* **中**: 14 より大きな脅威スコアが検査対象となります。
* **低 (LOW)**: 24 より大きな脅威スコアが検査対象となります。
* **実質的にオフ (ESSENTIALLY OFF)**: 49 より大きな脅威スコアが検査対象となります。

セキュリティー・レベルの設定値は、定期的に確認することをお勧めします。その手順については、[CIS セットアップのベスト・プラクティスの資料](best-practices.html)を参照してください。

## ベスト・プラクティス 3: Web アプリケーション・ファイアウォール (WAF) を安全にアクティブ化する
WAF は、**セキュリティー**のセクションで設定できます。ドメイン全体に対してオンにする前に、WAF が可能な限り安全に設定されるようにするため、これらの設定値について逆の順序で説明します。Traffic Application に WAF イベントを設定して詳細にチューニングすることにより、これらの初期設定によって誤検出を減らすことができます。新しい脆弱性が識別されると、WAF はそれに対応できるように自動的に更新されます。

WAF は、以下のタイプの攻撃からの保護を提供します。
* SQL インジェクション攻撃
* クロスサイト・スクリプティング
* クロスサイト偽造

WAF には、最も一般的な攻撃を止めるためのルールが含まれた、デフォルトのルール・セットが含まれています。現時点では、WAF は有効にすることも無効にすることも可能です。デフォルトのルール・セットと各ルールの動作について詳しくは、[WAF のデフォルト・ルール・セット](waf-rule-set.html)の資料を参照してください。

WAF について詳しくは、[WAF 概念の資料](waf-concept.html)を参照してください。

## ベスト・プラクティス 4: TLS 設定の構成
IBM CIS には、トラフィックを暗号化するためのいくつかのオプションが備わっています。リバース・プロキシーとして、データ・センターで TLS 接続を閉じてから、発信元サーバーへの新しい TLS 接続を開きます。

TLS には、操作のための 4 つのモードが備わっています。
* **オフ**: このモードでは TLS が無効になります。これは推奨されていません。
* **クライアントとエッジ間 (Client-to-edge)**: CIS からクライアントへのトラフィックは TLS で暗号化されますが、CIS から発信元サーバーへのトラフィックは TLS で暗号化されません。
* **終端間でフレキシブル (End-to-end flexible)**: すべてのトラフィックが TLS で暗号化されます。ただし、CIS と発信元サーバーの間のトラフィックは、自己署名証明書を使用して保護することができます。
* **終端間で CA 署名付き (End-to-end CA signed)**: すべてのトラフィックが TLS で暗号化されます。CA 署名付きの証明書を使用する必要があります。

TLS のオプションについて詳しくは、[この資料](ssl-options.html)を参照してください。

IBM CIS では、カスタムの証明書を使用することができます。あるいは、CIS によってユーザー用にプロビジョンされたワイルドカードを使用することもできます。

### カスタム証明書をアップロードする
**「証明書の追加 (Add Certificate)」**ボタンをクリックして、証明書、秘密鍵、およびバンドル方式を入力することにより、カスタムの証明書をアップロードできます。お客様独自の証明書をアップロードした場合は、暗号化トラフィックとの互換性が直ちに得られ、証明書 (例えば Extended Validation (EV) 証明書) に対する管理を維持することになります。カスタム証明書をアップロードした場合は証明書の管理が必要になるということに注意してください。例えば、IBM CIS は証明書の有効期限を追跡しません。 

![カスタムの証明書](images/upload-custom-certificate.png)

### プロビジョンされた証明書を利用する
IBM は、お客様にドメインのワイルドカード証明書を提供する複数の認証局 (CA) とパートナー関係を結んでいます。これらの証明書をセットアップするには、手動での検証が必要となることがあります。これらの追加ステップを実行する際には、サポート・チームの支援を受けることができます。
