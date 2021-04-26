## 経緯
- Androidアプリから、DBを操作したい！
  - [MobileSDKってのがあるみたい](https://media.amazonwebservices.com/jp/summit2015/docs/Dev-08m-Tokyo-Summit-2015.pdf)
  - [ドキュメント](https://docs.aws.amazon.com/mobile-sdk/index.html)見つけた
  - どうやら2021年１０月30日からAWSMobileHub が AWSAmplify に置き換わるっぽい。Amplify だと MobileHub の機能全て使えるみたい。[サイト](https://docs.aws.amazon.com/aws-mobile/latest/developerguide/what-is-aws-mobile.html)
- MobileHubって何ぞ
  - モバイル向けサービスのハブ機能を担う、やつらしい。サインイン、ストレージ、DB、分析、PUSHなどなど。そのMobileHubがAmplifyに置き換わるんやと。
- API Gateway使えば、RESTAPI作成できるっぽい。作成したAPIをLambdaと連携できるんけ。Cognitoは公開認証機能らしい。SNS認証か。DeviceFarmはSMS認証とかできそう。SNSはPUSH通知ね。業務で掠ったね。
  - [ドキュメント](https://www.capa.co.jp/archives/7066#:~:text=AWS%20Mobile%20Hub%E3%81%AFAWS,%E3%83%86%E3%82%B9%E3%83%88%E3%80%8D%E3%81%AA%E3%81%A9%E3%81%8C%E5%8F%AF%E8%83%BD%E3%81%A7%E3%81%99%E3%80%82)
- 一旦API Gatewayは置いといて、[Amplify](https://docs.amplify.aws/)って
  - Amazonが作ったフレームワークっぽい。Webにもアプリにも使えちゃうみたいな[公式サイト](https://aws.amazon.com/jp/amplify/?nc=sn&loc=1)
  - Amplifyフレームワーク自体にはかからないけど、使用するサービスにはかかるみたい。普通に使えるっぽい。読んでみてプラットフォーム環境が提供されているイメージ持つから料金高そうに感じる。いろいろ簡素化して出来ちゃうから割高的な。[このサイト](https://www.fenet.jp/aws/column/technique/139/#:~:text=AWS%20Amplify%E3%81%AE%E6%A7%8B%E6%88%90%E8%A6%81%E7%B4%A0,%E3%81%AE%E8%A8%AD%E8%A8%88%E3%81%8C%E3%81%A7%E3%81%8D%E3%81%BE%E3%81%99%E3%80%82)

てなわけで、とりあえずAmplify使ってみたほうが無難っぽそうなので、導入してみる。

## Amplifyのチュートリアルで学習を進める

### 以下のコマンドでインストール

`npm install -g @aws-amplify/cli`

こんな感じで出力されればOK

`added 1213 packages, and audited 1231 packages in 43s`

### 以下のコマンドでAmplifyCLI（コマンドラインインタフェース）をセットアップする

`amplify configure`

#### ログイン
コンソールにログインを求められるので、ログインする。そしてコマンドラインでEnterを押す。

#### リージョン選択
恐らく、Amplifyをセットアップするリージョンを選択するんだと思う。

```
Specify the AWS Region
? region:
  ap-south-1
  ca-central-1
  us-east-1
❯ us-east-2
  us-west-2
  eu-west-1
  eu-west-2
(Move up and down to reveal more choices)
```

[ap-northeast-1（東京である）](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions)を選択する。

#### IAMの作成
`Specify the username of the new IAM user:`

どうやらAmplify用の新しいIAMユーザの名前を入れるみたい。

「aws-amplify-only」でやってみた。するとマネジメントコンソールを開き、IAMの設定画面に遷移する。

1. 「プログラムによるアクセス」にチェックを入れた状態で、次へ。
2. 「AdministratorAccess」を選択して次へ
3. 最後に「ユーザーの作成」を押下する

アクセスキーIDとシークレットキーを受け取る（メモっておくこと）

→コマンドラインの方でEnter押すの忘れてた。

```
Press Enter to continue

Enter the access key of the newly created user:
? accessKeyId:  ********************
? secretAccessKey:  ****************************************
This would update/create the AWS Profile in your local machine
? Profile Name:  aws-amplify-only

Successfully set up the new user.
```

### アプリのセットアップ

今回テスト用プロジェクトを作成した。

- ProjectレベルのGradle

`classpath 'com.amplifyframework:amplify-tools-gradle-plugin:1.0.2'`
`(最後の行に)apply plugin: 'com.amplifyframework.amplifytools'`

- ModuleレベルのGradle

```
implementation 'com.amplifyframework:aws-api:1.17.1'
implementation 'com.amplifyframework:aws-datastore:1.17.1'
```

Syncを実行する

### プロジェクトのセットアップ

プロジェクトのルートまで移動し、以下を実行する

`amplify init`

以降、以下のようになる。（日本語訳はコメントにて記載）

```
# このコマンドは、アプリディレクトリのルートから実行することをお勧めします
Note: It is recommended to run this command from the root of your app directory
# プロジェクトの名前を入力します
? Enter a name for the project <APP_NAME>
# 次の構成が適用されます
The following configuration will be applied:

Project information
| Name: <APP_NAME>
| Environment: dev
| Default editor: Visual Studio Code
| App type: android
| Res directory: app/src/main/res

# 上記の構成でプロジェクトを初期化しますか？
? Initialize the project with the above configuration? Yes

# デフォルトのプロバイダーawscloudformationを使用する
Using default provider  awscloudformation

# 使用する認証方法を選択します。（今回はアクセスキーを選択
? Select the authentication method you want to use: AWS access keys
? accessKeyId:  ****
? secretAccessKey:  ****
? region:  ap-northeast-1

# バックエンド環境開発者をAWSAmplifyコンソールアプリに追加する
Adding backend environment dev to AWS Amplify Console app: <random>
⠦ Initializing project in the cloud...

# 中略

# 「amplifyaddapi」を試してバックエンドAPIを作成してから、「amplifypublish」を試してすべてをデプロイしてください
Pro tip:
Try "amplify add api" to create a backend API and then "amplify publish" to deploy everything
```

マネジメントコンソールからAmplifyを開き、アプリケーション一覧を開くと追加されている！やったー🙌

### モデルを作成する
DBのスキーマを設定する。

チュートリアル通りに行こう。

- ファイルビューを「Project」に切り替える
- amplifyディレクトリがプロジェクト内に作成されていることを確認する
  - `amplify/backend/api/amplifyDatasource/schema.graphql` を選択する
  - チュートリアル通りに書き換えてみよう。

```graphql
enum Priority {
  LOW
  NORMAL
  HIGH
}

type Todo @model {
  id: ID! // IDは自動生成される識別子。!はNonNull（非オプショナル型）
  name: String! // !はNonNull（非オプショナル型）である
  priority: Priority // 列挙型である
  description: String // Nullable（オプショナル型）である
}
```

書き終わったら、BuildConfiguration（AndroidStudioのビルドするところ）から、 **「modelgen」** を選択する。

Runを実行する（control + R)

```
The following types do not have '@auth' enabled. Consider using @auth with @model
	 - Todo
Learn more about @auth here: https://docs.amplify.aws/cli/graphql-transformer/auth


GraphQL schema compiled successfully.
```
AndroidStudioのRunエリアに上記のように表示されていればOK!

Todoクラスなどが生成される。

### 実際にちょこっと使ってみる。

実装例は以下の通り。コメントで補足している。めっちゃゴリ押し。

```kotlin
        try {
            Amplify.addPlugin(AWSDataStorePlugin())
            Amplify.configure(applicationContext)
            Log.d("Tutorial", "Initialized Amplify")
        } catch (e: AmplifyException) {
            Log.d("Tutorial", "Could not Initialized Amplify", e)
        }

//        var item : Todo = Todo.builder().name("Build Android application")
//            .description("using Amplify")
//            .build()

        var item : Todo = Todo.builder().name("Finish quarterly texas")
            .priority(Priority.HIGH)
            .description("texas are due for the quarter next week")
            .build()

        // ローカルデータ（DataStore）上にどんどん保存していく。実行するたびに保存データが増えていく。
        // SharedPreferencesみたい。
        Amplify.DataStore.save(item,
            {Log.d("Tutorial", "Saved item: ${item.name}")},
            {Log.d("Tutorial", "Could not item", it)})

        // 全て呼び出している。条件をつけてSELECTすることも可能
        Amplify.DataStore.query(Todo::class.java,
            {
                todos ->
                while (todos.hasNext()) {
                    val todo: Todo = todos.next()
                    Log.d("Tutorial", "========TODO=======")
                    Log.d("Tutorial", "Name : ${todo.name}")
                    Log.d("Tutorial", "Priority : ${todo.priority}")
                    Log.d("Tutorial", "Description : ${todo.description}")
                    Log.d("Tutorial", "")
                }
            },
            {
                Log.d("Tutorial", "Could not query DataStore", it)
            })
```

## AmplifyのGradleタスク

Amplifyを導入すると、Gradleタスクが追加される。

![スクリーンショット 2021-04-27 7 41 39](https://user-images.githubusercontent.com/23667931/116159972-25d44500-a72c-11eb-94d4-03d28beff547.png)

タスク自体はOther配下に作成されるみたい。

![スクリーンショット 2021-04-27 7 42 01](https://user-images.githubusercontent.com/23667931/116160007-34baf780-a72c-11eb-8f78-011ff7f22fd2.png)

- modelgen
  - ローカル上でモデルクラスを作成する
- amplifyPush
  - Amplifyのクラウド上にプッシュする

## (補足)npmとは
Node Package Managerである。Node.jsのパッケージ管理ツールである。

パッケージとは、Javascriptのライブラリやフレームワークのことを指す。

npmを使えば、各種ライブラリの依存関係を管理・解消してくれ、パッケージの更新・インストール/アンインストールを行うことが可能である。

#### ハイフンGとは
グローバルを指している。

#### パッケージ管理
npm list


### 参考

- [Amplifyチュートリアル](https://docs.amplify.aws/start/getting-started/installation/q/integration/android)
- [npm の基本的な使い方](https://www.webdesignleaves.com/pr/jquery/npm_basic.html)
