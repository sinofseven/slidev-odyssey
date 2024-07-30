---
theme: default
layout: image
image: /first.png
class: text-center
transition: "slide-left"
colorSchema: light
defaults:
  fonts:
    sans: Noto Sans JP
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">re:Invnet 2023に関連する<br/>DevIOの記事の索引を作ったので<br/>紹介します</div>
  </div>

  <div class="font-bold text-black absolute bottom-[80px] text-[24px]">
    <div>2024.7.29</div> 
    <div>製造ビジネステクノロジー部 夏目祐樹</div>
  </div>
</div>

---

# 自己紹介

- 夏目 祐樹 (ナツメ ユウタ)
- 製造ビジネステクノロジー部 自称サーバーレスエンジニア
- 好きなAWSサービス
  - Lambda, SQS, DynamoDB
- 近況
  - FF14 黄金のレガシー
    - 明日のメンテナンス後から高難易度コンテンツ開始
    - どのジョブで行くかもまだ決めきれてない

---

# こんなサイトを作りました (1)

- https://reinvent2023.index.devio.luciferous.app/
- https://bit.ly/4cYwSCp

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -7rem">
  <img src="/qr_code.png" class="h-[350px]" alt="">
</div>

---

# こんなサイトを作りました (2)

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/sample_01.png" class="h-[400px]" alt="">
</div>

---

# こんなサイトを作りました (3)

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/sample_02.png" class="h-[400px]" alt="">
</div>

---

# こんなサイトを作りました (4)

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/sample_03.png" class="h-[400px]" alt="">
</div>

---

# 今回はなすこと

- 作成までの経緯
- 設計
- フロントエンドの実装
- バックエンドの実装
- フロントエンドのビルド&デプロイとその他
- re:Invent 2024の索引を作るとすれば

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">作成までの経緯</div>
  </div>
</div>

---

# 最初の野望 (1)

- 最初に考え始めたのは12月下旬あたり (たぶん)
- re:Invent 2023の記事を集めた冊子を作ろうと思った
  - 冊子にまとめてオフィスに置いたら面白いかなと思った
- 冊子を安く印刷してくれるサイトを見つけていた
  - 製本直送.com

---

# 最初の野望 (2)

- DevIOの記事をそれぞれChromeでPDF化
- PDF化した記事をまとめて一つのPDFに
- ここまでは過去にやったことがある

---

# 最初の野望 (3)

- まとめたPDFの頭に目次をつけて、全ページにページ番号を付与
- 末尾にサービス毎の索引をつける
  - 実はこのときから索引を作ることを考えてた

---

# 潰える野望

- re:Invent 2022の最新記事がいつか調べてみた

<div class="flex" style="justify-content: center; align-items: center; height: 100%; margin-top: -6rem">
  <img src="/devio-reinvent-2022.png" class="h-[370px]" alt="">
</div>

---

# 他に困ったこと

- re:Invent 2023の記事から特定のサービスの記事を探しにくかった
- スマホから特集カテゴリを見た際、一番下までスクロールすると `Read More`というボタンが表示される仕様だった
  - 何故かボタンを押下すると`読み込みに失敗しました`と表示された
- 上記理由から、Webサイトで索引を作ることに決めた

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">新旧DevIOについて</div>
  </div>
</div>

---

# 新DevIO (1)

- 2024年07月から新DevIOにリニューアル

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -6rem">
  <img src="/devio-renewal.png" class="h-[370px]" alt="">
  <a href="https://dev.classmethod.jp/articles/developersio-202407-renewal/">https://dev.classmethod.jp/articles/developersio-202407-renewal/</a>
</div>

---

# 新DevIO (2)

- 公式にはアナウンスしてないけど、ツールを使うとContentfulを使ってることがわかる

<div class="flex" style="justify-content: center; align-items: center; height: 100%; margin-top: -6rem">
  <img src="/devio-wappalyzer.png" class="h-[370px]" alt="">
</div>

---

# 旧DevIO

- それ以前はWordpressを使用していた

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -6rem">
  <img src="/devio-old-archtecture.png" class="h-[213px]" alt="">
  <a href="https://dev.classmethod.jp/articles/developersio-cdn-cloudfront/">https://dev.classmethod.jp/articles/developersio-cdn-cloudfront/</a>
</div>

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">設計</div>
  </div>
</div>

---

# 設計 (1)

- 今回作るものは小規模なものなので、簡単な方針だけ決めた
- 方針
  - 記事情報はLambdaで取得する
  - 取得した記事に対して自分でカテゴリとタグを付与する
  - 静的なWebサイトとして構築する

---

# 設計 (2)

- 取得した記事に対して自分でカテゴリとタグを付与する
  - DevIOでは記事にタグがついている
    - 過不足があることがあるので自分で付与することにした
  - カテゴリはDevIOではついていないが、サムネイルで区別して付与する

---

# 設計 (3)

- 静的なWebサイトとして構築する
  - 記事の一覧を都度取得する必要はない
  - Webサイトのビルド時に取得し、静的に組み込むことでより高速に動くと考えた

---

# 設計 (4)

- 一つの記事につき集める情報は9つ
  - 下記ものに加えて、記事URLと著者URL, カテゴリ, タグ

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -7rem">
  <img src="/article-card.png" class="h-[300px]" alt="">
</div>


---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">フロントエンドの実装</div>
  </div>
</div>

---

# フロントエンドの実装方針

- Reactで作成する
- CSS Frameworkを使用して作成する
- スマホでの表示も考慮する

---

# CSS FrameworkとUI Framework

- 独断と偏見に満ちた自分の解釈
- CSS Framework
  - 原則CSSでUIを調整する
  - 代表的なのはtailwind.css
- UI Framework
  - VueやReactのコンポーネントを提供しているフレームワーク
  - Vuetifyやmui, Chakra UIなど

---

# Bulma (1)

- 自分がよく使うCSS Frameworkは Bulma

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -6rem">
  <img src="/bulma-io.png" class="h-[350px]" alt="">
  <a href="https://bulma.io">https://bulma.io</a>
</div>

---

# Bulma (2)

- Pure CSSなCSS Framework
- 色や見た目などを調整してくれる
  - Grid Layoutの仕組みを提供してくれたり
- 実装当時は0.x系だったが、3月下旬に1.x系がリリースされた
  - 1.x系からダークモードも提供された

---

# Bulma (3)

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/bulma-button.png" class="h-[450px]" alt="">
</div>

---

# Bulma (4)

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/bulma-navbar.png" class="h-[400px]" alt="">
</div>

---

# Bulma (5)


<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/bulma-modal.png" class="h-[400px]" alt="">
</div>

---

# スマホ対応のためにつかったもの

- ngrok
- SafariのWebインスペクタ
- CSSのmedia query

---

# ngrok

- ローカル開発中のWebページを外部からアクセスできるようにするのに使うサービス
- 昔からあったけど、気づいたら色々とできることが増えていた

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -10rem">
  <img src="/ngrok.png" class="h-[200px]" alt="">
</div>

---

# SafariのWebインスペクタ

- Macに有線でiPhoneをつないで設定することで、iPhoneのSafariで表示しているサイトの開発者コンソールをMacで開くことができる
- 設定をきちんとすれば、同一ネットワークにあれば有線でつなぐ必要はないかも
- iPhone見ているサイトの開発者コンソールが見えるようになったので、Styleの調整とか色々できるようになった

---

# media query

- CSSファイルにおいて、ディスプレイサイズによって当てるスタイルを変えることができる
- スマホ表示とそれ以外という形で今回は構築した
- ```css
  @media screen and (max-width: 1023px) {
    .Card {
      width: 362px;
    }

    .CardImage {
      height: 190px !important;
    }

    .CardAvatar {
      width: 24px !important;
      height: 24px !important;
    }
  }
  ```

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">バックエンドの実装</div>
  </div>
</div>

---

# データベースをどうするのか (1)

- 当初の予定ではDynamoDBを使用する予定だった
- しかし、タグ付けなどを行うための管理画面を別途作成する必要があった
  - タグエディターが必要になりUIフレームワークなどが必要になる
    - 簡単には作れない

---

# 話は変わるが、メモとして使っているもの

- Markdownで書けるサイト
  - https://mimemo.io
- Notion
  - Classmethodで全社的に使ってるし、個人アカウントを作成して使ったり
  - 子ページだったり、メモ用のデータベースを用意しておいたり

---

# Notionのデータベースにはタグエディターがあった

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/notion-tag-editor.png" class="h-[400px]" alt="">
</div>

---

# Notionのデータベースは実は便利に使えるものだった

- データベースのPropertyに必要な値を設定すれば都合良く使えそうだった
  - 問題だったタグエディターもはじめから持ってた
- API経由でデータの読み書きを行うことができる
  - ワークスペースレベルでの管理者権限が必要ではある
  - 1秒あたり3リクエストの制限はある

---

# 使ったデータベース

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/notion-database-sample.png" class="h-[400px]" alt="">
</div>

---

# バックエンドのアーキテクチャ

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/architecture.png" class="h-[400px]" alt="">
</div>

---

# 話は変わるが、私は咎人です

- クラスメソッドにおいて私は前科があります
- 私はDevelopersIOを落としました
  - それもre:Inventの最中に
- なので今回はDevIOに負荷をかけないように注意して作りました

---

# Wordpressへのリクエストを制限

- Pythonのクロージャを使って、HTTPリクエストの間隔を制御した
- ```python
  def create_http_client(
    sec: Union[int, float]
  ) -> Callable[[Union[str, Request]], HTTPResponse]:
    dt_prev = datetime.now()

    @logging_function(logger)
    def process(req: Union[str, Request]) -> HTTPResponse:
        nonlocal dt_prev
        delta = datetime.now() - dt_prev
        interval = sec - delta.total_seconds()
        if interval > 0:
            sleep(interval)
        try:
            return urlopen(req)
        finally:
            dt_prev = datetime.now()

    return process
  ```

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">フロントエンドのビルド&デプロイ<br/>とその他</div>
  </div>
</div>

---

# フロントエンドのデプロイ先

- CloudFlare Pages
  - GithubにPushするだけでデプロイができる
  - package.jsonに仕込めばビルド前に処理を追加することができる
  - デプロイフックを使って外部からデプロイを実行することができる

---

# ビルド前の記事情報取得

- package.jsonの"script.build"でbuildする前にTypeScriptを動かして記事情報を取得する
- 今回は、TypeScriptを使ってtsファイルを生成するという暴挙に出る
  - import順をformatしてくれるprettierのプラグインが当時はjson importに対応していなかったため
- NotionにアクセスするためのAPI KeyなどはCloudFlare Pagesで環境変数にセットする
  - 暗号化して保存もできる

---

# アーキテクチャを少し変更

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/architecture_v2.png" class="h-[400px]" alt="">
</div>

---

# 最初のお仕事

- 無事デプロイまで動くようになった後の最初のお仕事は200件以上ある記事へのタグ付け
- 純粋に時間がかかった
  - ここが終わったら、追加されたものを少しずつタグ付けしていくだけでいい

---
layout: image
image: /section.png
---

<div class="flex items-center justify-center h-full">
  <div>
    <div class="text-black font-bold text-[60px]">re:Invent 2024の索引を作るとすれば</div>
  </div>
</div>

---

# 今年のre:Inventの索引を作るとすれば

- フロントエンドの実装はほぼそのまま使い回すことができる
  - HTMLのタイトルとかNotionのDBのIDとか最小限度の編集で可能
  - PCで見たとき、記事のカードの縦幅がガタガタしてるのを直してもいいかもしれない
- バックエンドは全面改修した方が楽かもしれない
  - 今の仕組みはWordPressを前提としたもの
  - そこだけ書き直すこともできるかもしれないが取得できるデータも違うし、一度捨てて書き直した方がいいかも

---

# 何よりも

- 今年はre:Invent開催期間中に着手する
- もう一度に200件以上のタグ付けとかしたくない
  - 早くやればやるほど、一度にタグ付けする記事の量は減るはず

---

# まとめ

- re:Invent 2023のDevIOの記事の索引を作りました
  - https://reinvent2023.index.devio.luciferous.app/
- 自分が何を考え、どう作ったのか紹介しました
- 今後何かのお役に立てたら幸いです

---

# DevelopersIO 2023 re:Invent 索引

- https://reinvent2023.index.devio.luciferous.app/
- https://bit.ly/4cYwSCp

<div class="flex" style="justify-content: center; flex-direction: column; align-items: center; height: 100%; margin-top: -7rem">
  <img src="/qr_code.png" class="h-[350px]" alt="">
</div>
