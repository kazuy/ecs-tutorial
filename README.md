# ECS Tutorial

目的

- ECSの理解を深める
- [AWS開発者ガイド](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/ECS_CLI.html)を試す

## ecs-cli インストール

Homebrewで一発

```
$ brew install amazon-ecs-cli
```

## 設定

### ECS CLIの設定

名前付きAmazon ECSプロファイルで、~/.ecs/credentialsファイルに保存されているAWS認証情報を設定する

```
$ ecs-cli configure profile --profile-name <profile_name> --access-key <AWS_ACCESS_KEY_ID> --secret-key <AWS_SECRET_ACCESS_KEY>
```

- profile_name: 任意の値(defaultなど  ~/.ecs/credentialsに追加される)
- AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY: IAMユーザーのアクセスキー

### クラスター情報の設定

使用するAWSリージョン、リソース作成プレフィックス、Amazon ECS CLIで使用するAmazon ECSクラスター名を設定する

```
$ ecs-cli configure --cluster <cluster_name> --default-launch-type <launch_type> --region <region_name> --config-name <configuration_name>

ex)
$ ecs-cli configure --cluster ecs-tutorial-cluster --region ap-northeast-1 --default-launch-type EC2 --config-name ecs-tutorial
```

- cluster_name: 任意の値(ECSクラスター一覧に表示される)
- launch_type: EC2 or FARGATE
- region_name: ap-northeast-1
- configuration_name: 任意の値(ecs-tutorialなど  ~/.ecs/configに追加される)

### クラスター作成

クラスターのセットアップに必要なAmazon ECS クラスター(まだない場合)とAWSリソースを作成する

```
$ ecs-cli up --keypair <id_rsa> --capability-iam --size 2 --instance-type <instance_type> --cluster-config <configuration_name>

ex)
$ ecs-cli up --keypair <id_rsa> --capability-iam --size 2 --instance-type t2.micro --cluster-config ecs-tutorial
```

- id_rsa: EC2に設定するキーペア名(事前に作成しておく)
- instance_type: t2.microなど
- configuration_name: クラスター情報の設定名

### クラスターへデプロイ

ComposeファイルからECS タスク定義を作成し(まだない場合)、そのタスクの1つのインスタンスをクラスターで実行する(createとstartの組み合わせ)

```
$ ecs-cli compose up --create-log-groups --cluster-config <configuration_name>

ex)
$ ecs-cli compose up --create-log-groups --cluster-config ecs-tutorial
```

- configuration_name: クラスター情報の設定名

### クラスター実行中コンテナの確認

Amazon ECSクラスターで実行中のすべてのコンテナを一覧表示する

```
$ ecs-cli ps
```

### クラスタータスクのスケーリング(任意)

実行中のタスクの数を、指定した数にスケーリングする

```
$ ecs-cli compose scale 2 --cluster-config <configuration_name>
```

- configuration_name: クラスター情報の設定名

### コンテナの停止

Composeプロジェクトによって作成されたすべての実行中のタスクを停止する

```
$ ecs-cli compose down --cluster-config <configuration_name>
```

- configuration_name: クラスター情報の設定名

### サービス起動

構成ファイルからAmazon ECSサービスを作成し(まだない場合)、そのタスクの1つのインスタンスをクラスターで実行する(createとstartの組み合わせ)

```
$ ecs-cli compose service up --cluster-config <configuration_name>
```

- configuration_name: クラスター情報の設定名

### リソースのクリーンアップ

サービスの必要数を0に更新してから、サービスを削除する

構成プロジェクトで作成されたサービスに属する実行中のタスクを停止したい場合は`ecs-cli compose service stop`を使用する

```
$ ecs-cli compose service rm --cluster-config <configuration_name>
```

- configuration_name: クラスター情報の設定名

### クラスターの削除

`ecs-cli up`によって作成された AWS CloudFormationスタックおよび関連するリソースを削除する


```
$ ecs-cli down --force --cluster-config <configuration_name>
```

- configuration_name: クラスター情報の設定名
- force: VPC、サブネット、セキュリティグループ等削除される

## コマンドラインリファレンス

[こちら](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/ECS_CLI_reference.html)を参照する

