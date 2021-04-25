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



### 参考
- [Amplifyチュートリアル](https://docs.amplify.aws/start/getting-started/installation/q/integration/android)


## npmとは
Node Package Managerである。Node.jsのパッケージ管理ツールである。

パッケージとは、Javascriptのライブラリやフレームワークのことを指す。

npmを使えば、各種ライブラリの依存関係を管理・解消してくれ、パッケージの更新・インストール/アンインストールを行うことが可能である。

### ハイフンGとは
グローバルを指している。

### パッケージ管理
npm list


### 参考
- [npm の基本的な使い方](https://www.webdesignleaves.com/pr/jquery/npm_basic.html)
