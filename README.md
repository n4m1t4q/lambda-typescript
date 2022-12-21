- [`0. バージョン確認`](#0-バージョン確認)
- [`1. serverless framework インストール`](#1-serverless-framework-インストール)
- [`2. sls-offline インストール`](#2-sls-offline-インストール)
- [`3. ローカル実行`](#3-ローカル実行)
- [`4. AWS プロファイル設定`](#4-aws-プロファイル設定)
- [`5. デプロイ`](#5-デプロイ)
- [`7. アンデプロイ`](#7-アンデプロイ)

## `0. バージョン確認`

```sh
$ git --version
git version 2.28.0.windows.1

$ npm -v
8.1.2

$ node -v
v16.13.2

$ cd ~/git
```

## `1. serverless framework インストール`

```sh
$ npm install -g serverless

$ sls create --template aws-nodejs-typescript --path lambda-typescript

$ cd lambda-typescript

$ npm install
```

## `2. sls-offline インストール`

```sh
$ npm install serverless-offline --save-dev
```

## `3. ローカル実行`

```sh
$ sls offline
```

## `4. AWS プロファイル設定`

IAM ユーザー作成

```sh
$ code -r ~/.aws/credentials
```

`~/.aws/credentials`

```
[sls-deploy-user]
aws_access_key_id=
aws_secret_access_key=
```

## `5. デプロイ`

`serverless.ts`

```ts
const serverlessConfiguration: AWS = {
  service: "lambda-typescript",
  frameworkVersion: "3",
  plugins: ["serverless-esbuild", "serverless-offline"],
  provider: {
    name: "aws",
    runtime: "nodejs14.x",
    apiGateway: {
      minimumCompressionSize: 1024,
      shouldStartNameWithService: true,
    },
    environment: {
      AWS_NODEJS_CONNECTION_REUSE_ENABLED: "1",
      NODE_OPTIONS: "--enable-source-maps --stack-trace-limit=1000",
    },
    /** 追加 */
    region: "ap-northeast-1",
  },
  // and more.
};
```

```sh
$ sls deploy --aws-profile sls-deploy-user --verbose

# デプロイに成功するとコンソール上にエンドポイントが出力される

✔ Service deployed to stack lambda-typescript-dev (132s)

endpoint: POST - https://n5feqjwxkf.execute-api.ap-northeast-1.amazonaws.com/dev/hello
functions:
  hello: lambda-typescript-dev-hello (12 kB)

Stack Outputs:
  HelloLambdaFunctionQualifiedArn: arn:aws:lambda:ap-northeast-1:106096601035:function:lambda-typescript-dev-hello:2
  ServiceEndpoint: https://n5feqjwxkf.execute-api.ap-northeast-1.amazonaws.com/dev
  ServerlessDeploymentBucketName: lambda-typescript-dev-serverlessdeploymentbucket-nv6yes2m3g53

Need a better logging experience than CloudWatch? Try our Dev Mode in console: run "serverless --console"
```

## `7. アンデプロイ`

```sh
$ sls remove --aws-profile sls-deploy-user --verbose
```
