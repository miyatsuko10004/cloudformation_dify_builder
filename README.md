# cloudformation_dify_builder

## 概要
このプロジェクトは、CloudFront、ALB、EC2を使用したDify環境を構築するためのCloudFormationテンプレートです。以下の三層構成を提供します。
- CloudFront → ALB → EC2

## 機能
- CloudFront、ALB、EC2の三層構成
- ACM証明書、VPC、サブネット、セキュリティグループ、IAMロールをパラメーターで指定
- 自動的にDockerとDocker Composeをインストールし、Difyをデプロイ

## パラメーター
- `OnFailure`: スタック作成失敗時の動作。デフォルトは `DELETE`。許容値は `DELETE`, `ROLLBACK`, `DO_NOTHING`。
- `KeyName`: 既存のEC2キーペアの名前。
- `InstanceType`: EC2インスタンスタイプ。デフォルトは `t3.small`。許容値は `t3.nano`, `t3.micro`, `t3.small`, `t3.medium`, `t3.large`, `m8g.medium`, `m8g.large`, `m8g.xlarge`, `m8g.2xlarge`。
- `CloudFrontCertARN`: CloudFront用のACM証明書のARN（us-east-1で発行されたもの）。
- `ALBCertARN`: ALB用のACM証明書のARN（VPCと同じリージョンで発行されたもの）。
- `EC2VpcId`: EC2を配置するVPCのID。
- `EC2SubnetId`: EC2インスタンスを配置するパブリックサブネットのID。
- `ECSecurityGroupId`: EC2用に利用するセキュリティグループのID。
- `EC2IAMRole`: EC2インスタンスにアタッチするIAMロール（インスタンスプロファイル名）。
- `ALBVpcId`: ALBを配置するVPCのID。
- `ALBSubnetIds`: ALBを配置するサブネットIDのリスト（AZから最低2つ）。
- `CloudFrontDomainName`: CloudFrontのAlternate domain name。
- `DomainName`: アプリケーションのドメイン名（例: `example.com`）。

## リソース
- **ALBSecurityGroup**: ALB用のセキュリティグループ。
- **DifyInstance**: DifyをホストするEC2インスタンス。
  - DockerとDocker Composeをインストールし、Difyをデプロイ。
- **DifyALB**: インターネット向けのALB。
- **DifyTargetGroup**: EC2インスタンスをターゲットとするターゲットグループ。
- **DifyALBListener**: 443番 (HTTPS) リスナー。
- **DifyALBListenerHTTP**: 80番 (HTTP) リスナー。
- **DifyCloudFrontDistribution**: CloudFrontディストリビューション。

## 出力
- `CloudFrontURL`: CloudFront経由でDifyにアクセスするためのURL。
- `ALBDNSName`: アプリケーションロードバランサーのDNS名。
- `EC2InstanceId`: Difyを実行しているEC2インスタンスのID。

## インストール手順
1. リポジトリをクローンします。
    ```sh
    git clone https://github.com/miyatsuko10004/cloudformation_dify_builder.git
    ```
2. AWS CLIを使用してCloudFormationスタックをデプロイします。
    ```sh
    aws cloudformation create-stack --stack-name my-dify-stack --template-body file://builder.yaml --parameters ParameterKey=KeyName,ParameterValue=my-key-pair ParameterKey=InstanceType,ParameterValue=t3.small ParameterKey=CloudFrontCertARN,ParameterValue=arn:aws:acm:us-east-1:333333333333:certificate/123456789123456789 ParameterKey=ALBCertARN,ParameterValue=arn:aws:acm:ap-northeast-1:333333333333:certificate/987654321987654321 ParameterKey=EC2VpcId,ParameterValue=vpc-123456789 ParameterKey=EC2SubnetId,ParameterValue=subnet-0123456789 ParameterKey=ECSecurityGroupId,ParameterValue=sg-0123456789 ParameterKey=EC2IAMRole,ParameterValue=hoge ParameterKey=ALBVpcId,ParameterValue=vpc-123456789 ParameterKey=ALBSubnetIds,ParameterValue=\"subnet-0123456789,subnet-123456789\" ParameterKey=CloudFrontDomainName,ParameterValue=my.cloudfront.domain ParameterKey=DomainName,ParameterValue=example.com
    ```

3. スタックが作成されるのを待ちます。

## 貢献方法
貢献したい場合の手順を説明します。
1. フォークします。
2. 新しいブランチを作成します。
    ```sh
    git checkout -b feature/新機能
    ```
3. 変更をコミットします。
    ```sh
    git commit -m 'Add 新機能'
    ```
4. プッシュします。
    ```sh
    git push origin feature/新機能
    ```
5. プルリクエストを作成します。

## ライセンス
このプロジェクトはMITライセンスの下で公開されています。

## コンタクト
プロジェクトに関する質問や提案がある場合は、[Issues](https://github.com/miyatsuko10004/cloudformation_dify_builder/issues)からお問い合わせください。
