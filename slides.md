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
    <div class="text-black font-bold text-[60px]">AWSのサービス間連携について<br/>紹介します</div>
  </div>

  <div class="font-bold text-black absolute bottom-[80px] text-[24px]">
    <div>2024.7.10</div> 
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
  - お仕事少し落ち着いた
  - FF14 黄金のレガシー
    - メインクエ終わったからギャザクラとレベリングで忙しい

---

# イベント駆動 (1)

<div class="flex" style="justify-content: center; align-items: center; height: 100%; margin-top: -3rem">
  <img src="/event_driven_01.png" class="h-[200px]" alt="">
</div>

---

# イベント駆動 (2)

<div class="flex" style="justify-content: center; align-items: center; height: 100%; margin-top: -3.5rem">
  <img src="/event_driven_02.png" class="h-[200px]" alt="">
</div>

---

# イベント駆動 (3)

- Actionにコンピュートリソースを使う場合
  - コードの運用が必須になる
    - LambdaのRuntime更新など
    - 外部ライブラリを使っていれば、それの更新もある

---

# 今回お話しすること

- コンピュートリソースを使わずにサービス間のデータ連係ができるサービスの紹介
- 自分が使ったことがあるサービスについて話します
  - 他にもできるサービスがあるかもしれないということです

---

# SNS Topic

- Pub/Subのサービス
- ひとつのTopicに対して複数のSubscriptionを設定でき、<br/>TopicにデータがPublishされると全てのSubscriptionにデータが飛ぶ
- Subscriptionのとしては次のプロトコルを使用できる (一部抜粋)
  - Eメール, Lambda Function, <br/>SQS Queue, Kinesis Firehose

---

# S3 Notification (1)

- S3のオブジェクトに対するイベントを通知することができる機能
- 対象のオブジェクトはPrefixとSuffixの両方で制限できる
- 通知対象のイベントはオブジェクトの作成や削除の他に、<br/>ライフサイクルの移行イベントや有効期限イベント、<br/>タグの付け外しなども対象

---

# S3 Notification (2)

- 通知先は、Lambda Function, SQS Queue, SNS Topicの三択

<div class="flex gap-[8px] mt-[20px]" style="justify-content: center">
  <img src="/s3_notification.png" class="h-[200px]" alt="">
</div>

---

# S3 Notification (3)

- 注意点
  - 同一のPrefix, Suffixの組のNotificationは一つしか作れない
    - 複数のLambda FunctionやSQS Queueに通知を飛ばしたい場合は、SNS Topicに通知してそこから投げてもらう

---

# Amazon Data Firehose (1)

- データをバッファリングしながら、別の場所に書き込むサービス
- Kinesis Data StreamやAmazon SMKからデータを取得できる
  - Firehoseに直接データを投げることもできる
- データを書き込む先は、S3, OpenSearch, Redshiftがある
  - AWS以外のSaaSにデータを書き込むこともできる
    - Datadog, Elastic, Splunk, Snowflake, etc...

---

# Amazon Data Firehose (2)

- 注意点
  - Redshiftへの書き込みを行う際には、Redshiftのクラスターが外部のインターネットから見える必要がある
    - すでにPrivate SubnetにあるRedshiftクラスターには書き込みできない
    - FirehoseのIPアドレスの範囲で制限をかけることは可能

---

# CloudWatch Logs (1)

- ログを保存するサービス
- SubscriptionFilterという機能を使って、FilterPatternに指定したログが保存された際に別のサービスにデータを飛ばす
- 飛ばすことのできるサービスは次の4つ
  - Lambda, OpenSearch, <br/>Kinesis Data Stream, Amazon Data Firehose

---

# CloudWatch Logs (2)

- Tips
  - FilterPatternとして正規表現を使用することができます
  - JSONで構造化されたログの場合、FilterPatternで特定のキーに特定の値があるときという風にかくことができる
    - ```{ $.level = "ERROR" }"```

---

# IoT Core Rule (1)

- AWSのIoTのための基盤となるサービス
- MQTTというプロトコルでの通信をサポートする
  - 一応HTTPSでもPublishはできる
- デバイスの管理やMQTTで通信を行うための証明書の管理、<br/>
  証明書に紐付ける権限の管理をする
- それだけだとMQTTのバックエンドでしかないので、<br/>
  Ruleという機能でデータ連係ができる

---

# IoT Core Rule (2)

- IoT Core Rule
  - トピックにPublishされたデータをAWSのサービスなどに流すことができる
  - SQL形式で条件や形式を指定する
  - 一つのルールで送信先を複数もつことができる
    - 失敗したときの送信先を一つ設定できます
    - 通常の送信先も失敗したときの送信先も、<br/>送信先の選択肢は同じです

---

# IoT Core Rule (3)

- データの送信先は色々ある (一部抜粋)
  - IoT Events, IoT SiteWise,<br/>
    Firehose, Kinesis Data Stream,<br/>
    DynamoDB, S3, SNS, SQS,<br/>
    Lambda, StepFunctions, Timestream

---

# IoT Core Rule (3)

- Tips
  - IoTと銘打っているが、IoT以外にも使うことができる
    - 昔Web上のチャットかなにかを<br/>IoT Coreを使って作ったってのを見かけたことがある

---

# DynamoDB (1)

- DynamoDBにはItemの操作を出力するDynamoDB Streamがある
- あるが、実はDynamoDB Streamを使わずに、<br/>Kinesis Data Streamにデータを渡すこともできる
- Kinesis Data Streamに渡されるのはDynamoDB Streamの`NEW_AND_OLD_IMAGES`になる

---

# DynamoDB (2)

- これで何がうれしいの? (個人の意見です)
  - DynamoDBの変更を全てS3に保存できる
    - DDB -> Kinesis Stream -> Firehose -> S3
  - DynamoDB Streamのシャード数は可変で固定できない
    - Kinesis Streamによってシャード数を固定することができる

---

# EventBridge Rule (1)

- AWS汎用のEvent Sourceみたいなサービス
  - 何かが発火したEventをキャッチして別のターゲットに投げる
- Scheduleを使って一定時間おきにLambdaを動かすなどしている人は多いと思う
- 実はそれ以外にも色々なSourceやTargetにも対応している


---

# EventBridge Rule (2)

- 使用できるEvent Sourceが豊富
  - AWSの各種サービスが発火するEvent
    - 昔使ったのはStepFunctionsの失敗したのを知ろうとした
  - CloudTrailが吐き出しているAWSへの操作ログ
  - 一部SaaSのイベントもEvent Sourceとして使える
    - Datadog, Auth0, New Relic, etc...

---

# EventBridge Rule (3)

- Lambdaの他にも色々動かすことができる
  - EC2 RebootInstances API Call, EC2 StopInstances API Call,<br/>
    EC2 TerminateInstance API Call, ECS Task, Glue Workflow,<br/>
    EventBridge EventBus, Firehose, Kinesis Stream,<br/>
    SNS Topic, SQS Queue, StepFunctions StateMachine, <br/>
    Systems Manager Run Command, Systems Manager Automation,<br/>
    etc...

---

# StepFunctions (1)

<div class="flex gap-[8px] mt-[20px]" style="justify-content: space-between">
  <div>
    <ul>
      <li>今回特に紹介したい3つのサービスの一つ</li>
      <li>StateMachineという名前のワークフローを組んで処理を行う</li>
    </ul>
  </div>
  <img src="/sample_statemachine.png" class="h-[400px]" alt="">
</div>

---

# StepFunctions (2)

- StateMachineには2つの種類がある
  - 最大1年実行できる標準ワークフロー 
  - 最大5分実行できるExpressワークフロー (短時間高速に動かす)
- 料金体系も異なる

---

# StepFunctions (3)

- ワークフローの各ステップを状態(State)と呼ぶ (以下一部State)
  - Task: 様々な処理を行う
  - Choice: 条件分岐
  - Wait: 指定した秒数待つか、指定した日時まで待つ
  - Parallel: 内部出来に複数の処理を並列実行する
  - Map: 渡されたEventなどに含まれる配列を並列に処理する

---

<div class="flex gap-[8px] mt-[20px]" style="justify-content: center; align-items: center; height: 100%">
  <img src="/horizon_statemachine.png" class="w-[1000px]" alt="a">
</div>

---

# StepFunctions (4)

- Task Stateで実行できる物 (一部)
  - Lambda: Lambdaを動かす
  - AWS SDK サービス統合: AWSのAPIをCallする
  - 最適化された統合: AWSのAPI CallをStepFunctions用に最適化
    - StepFunctions, Athena, Bedrock, DynamoDB, Glue,<br/>
      ECS/Fargate, SageMaker, SNS, SQS, etc...

---

# StepFunctions (5)

- 扱いにくいところ
  1. StateMachineの定義をJSONで書かないといけない
  2. 各Stateで必要になるデータ処理が複雑

---

# StepFunctions (6)

- StateMachineの定義をJSONで書かないといけない
  - AWSがビジュアルエディタを用意してくれているので<br/>ポチポチしながら作れる
  - JSONで出力することができるので<br/>IaCなどにも持って行くことができる

---

<div class="flex" style="height: 100%; justify-content: center; align-items: center">
  <img src="/visual_editor_sfn.png" alt="" />
</div>

---

# StepFunctions (7)

- 各Stateで必要になるデータ処理が複雑
  - 抽出したり加工したりしないといけない
  - AWSがデータフローシミュレーターを作ってくれているので、<br/>
    ポチポチして試すことができる

---

<div class="flex" style="height: 100%; justify-content: center; align-items: center">
  <img src="/data_flow_simulator.png" class="h-[95%]" alt="" />
</div>

---

# StepFunctions まとめ

- StateMachineという名のワークフローを定義して実行するサービス
- 短時間高速に動かすExpressワークフローと標準のものがある
- Lambdaを動かすほかにAWSのAPIをCallすることができる
- JSONで記述する必要があるがビジュアルエディタがある
- データフローシミュレーターでデータ加工などを試すことができる

---

# EventBridge Pipes (1)

- 今回特に紹介したい3つのサービスの二つ目
- ソースにデータが入力されると、<br/>別のサービス(ターゲット)にデータを投入するためのサービス
- ターゲットにデータを入れる前にデータを変換/加工することもできる
- 個人的にはこれで痒いところに手が届くようになったと思っている

---

# EventBridge Pipes (2)

- 対応しているソース
  - DynamoDB Stream, Kinesis Data Stream, SQS Queue,<br/>
    Amazon MQ, Amazon MSK, Apache Kafka (一部SaaS)

---

# EventBridge Pipes (3)

- 対応しているターゲット
  - SQS Queue, SNS Topic, Kinesis Data Stream, Firehose,<br/>
    ECS Task, Lambda, StepFunctions StateMachine, <br/>
    CloudWatch Logs, EventBridge EventBus,, Redshift Data API,<br/>
    etc...

---

# EventBridge Pipes (4)

- フィルタリング
  - 指定しなければソースに入った全てのデータをターゲットに送る
  - 指定していれば、対象のもののみターゲットに送る
    - 指定した対象以外はソースからも削除されてしまうので、<br/>
      かなり注意して指定しないといけない

---

# EventBridge Pipes まとめ

- ソースにデータが入力されるとターゲットに渡すサービス
- ターゲットに入れる前にデータを変換/加工できる
- ターゲットに渡すデータをフィルタリングできる
  - できるが注意して使う必要がある

---

# API Gateway

- 今回特に紹介したい3つのサービスの三つ目
- REST APIとHTTP APIでAWSのAPIをコールすることができる
- REST APIだとほぼすべてのAWSのAPIをコールできる
- HTTP APIだと限られた範囲ではあるが、AWSのAPIをコールできる
  - EventBridge PutEvents, SQS SendMessage, Kinesis PutRecord,<br/>
    StepFunctions StartExecution, etc...

---

# まとめ

- 自分が知っている使ったことのある、AWSのサービス間データ連係について紹介しました
- AWSのアーキテクチャを設計する際に思い出してもらえると幸いです

---
layout: image
image: /end_card.png
class: text-center
---


