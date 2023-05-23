[Well-Architected Freamwork](https://aws.amazon.com/jp/architecture/well-architected/)

AWS Config リソースに対してあるべき姿を定義できる
リソースの設定を記録する
ルールを作ることができ、それに沿ったリソースとなっているかをチェックできる

stsのAssumeRole
AssumeRoleすることでプリンシパルがロールを切り替えれる

iamのPassRole
ロールの割り当てができる

明示的拒否が一番強い理由は、複数のポリシーをつけた際に予期せず、許可権限があったために本来拒否しようとしたものが有効になっていないものの可能性があるため

Systems Manager
リソースの中身の管理ができる

ABAC
リソースの属性値(タグ)でのアクセスポリシーの管理の仕方
RBAC
リソースのARNでのアクセスポリシーの管理の仕方

タグづけ戦略は重要
横断的なチームでちゃんと考える必要がある
一貫性のある方法でタグを使用する(強制させる)

ユーザデータ
インスタンス起動時に利用するスクリプト

EC2 Image Builder
AMI生成の自動化


Landing Zone
マルチアカウントを利用する上でのソリューション

ControlTower
マルチアカウントのランディングゾーンを作り、アカウント作成などを自動化できる
いろんなサービスを使って指示するサービスみたいなイメージ

OpsWorks
chef pappetでデプロイできるサービス
SystemsMangerでansibleが使える

CloudFormation
[Former2](https://former2.com/)という3rd Party製のツールでCloudFormationテンプレートを作るのが楽
DependsOn デプロイ順序の指定 先にデプロイするものを記述 getAttrとかは待ってくれないことがあるのでこれを指定する
他のテンプレートをで出力された値を参照することも可能
https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-importvalue.html
ドリフトを検出すると構成された現在の状態とテンプレートと何が違うのか検知できる
Service Catalog
これ経由で構成すると権限がなくてもデプロイできるので、開発担当でも可能になる

Serverless Application Model(SAM)
CloudFormationの拡張機能、サーバレスアプリの場合はこちらの方が楽にかける

OpsCenterはIncidentManagerに統合される予定
IncidentManagerはチケット化までできる

AppConfig
アプリから取得して、ロジックで使える
設定ファイルの代わり。フィーチャーフラグみたいなものに使うっぽい
Parameter Store
シークレット情報をアプリで取得し、ロジックで使える
ID、Passwordみたいなものに使う

2つの違いはアプリの制御に使うものなのか設定的なパラメータなのか。

メンテナンスウインドウ
パッチを適用するためのスケジュールを管理する
パッチマネージャー
何をやるか
Change Manager
承認ワークフロー

フリートマネージャー
管理してるサーバ全体に直接入らずにノード管理ができる
セッションマネージャーは1台について

ドキュメント
操作の集合体
Jsonで書かれたスクリプト集みたいなもの
自動実行の元

ホストに入って何かするのがRun Command、ホストに対して何かするのがオートメーション

ステートマネージャー
ドキュメントの実行結果見えたり、異常状態になっていないかが見える

起動テンプレート
起動設定
インスタンスを起動する時に必要な設定をテンプレート化しておく
起動設定はオートスケール専用

動的スケーリング CloudWatchのアラートベース
	簡易 シンプルな設定
	ステップ 段階的に設定できる
	ターゲット追跡 自動的に作ってくれる
予測スケーリング AIベース
予定されたアクション スケジュール
複数のスケーリングポリシーを組み合わせられる

インスタンスの更新を使うことでローリングアップデートができる

License Manager
ライセンスを登録する
AMIと関連づけて、幾つになったかが見える

CloudWatch
クロスリージョン対応になった
ディメンション グループのことEC2とかだとインスタンスだったりする
メトリクスは基本15ヶ月間保持
基本的には5分ごとのモニタリング、1分ごとにしたい場合課金して詳細モニタリングをする
1分未満でモニタリングさせたい場合は自前でAPI叩いて行う

CloudWatch Logs Insights
ログの分析をしてくれるサービス
ロググループごと

EventBridgeパイプ
リアルタイムデータを使ったイベント駆動が可能

Health Dashboard
AWS全体の稼働状態を確認できる

ResourceHealth
視覚的にEC2のホスト状態が確認できる

X-Ray
各サービスがどう連携していてどういう状態なのか確認できるサービス
リクエスト単位のトレース

CloudWatch AgentはSystemsManagerと違い絶対手でインストールする必要がある

IAM Access Analizer
アクセスレベルや外部公開してるかなどをチェックしてくれる
情報漏洩リスクの抑制ができる

Permission boundary
ポリシーに対してさらにバウンダリーポリシーを付与できる
例えばグループポリシーが適用されるが、やらせたくない操作があるみたいな時に使う

GuardDuty
ログを機械学習を通していつもと違う動きを検出

Inspector
インスタンスの中の脆弱性検出
使ってるライブラリに対してチェック
セキュリティ的に穴がないかとかもチェック

Transit Gateway
複数のネットワークを一元管理できる

VPCエンドポイント
	ゲートウェイエンドポイント
		VPCのルートテーブルで設定
		Dynamoはこれだけ
		無料
	インターフェイスエンドポイント
		ENIで設定
		有料

Route53 Resolver
プライベートなホストの名前解決ができる
普通な名前解決もできる

VPCフローログ
ネットワークのトラフィックに関するログを収集できる
通信についてだけ見れる

WAF
ウェブアプリケーションを保護する仕組み
SQLインジェクションとかHTTPフラッドを実行させないようにする

Shield
DDos保護サービス
	Standard
		ELBとか守れるものが限定的
		自動適用
		無料
	Advanced
		対策チームができる
		WAFと統合できる
		3000ドル

インスタンスストア
インスタンスに付属しているストレージ

EBS
最大のメリットはアタッチされたままサイズを変更できる
増やすことしかできない
RAID 0 or 1のみ利用可能
増分スナップショット
各スナップショットで復元するために最新のスナップショットで消されているものもS3にはまだ存在するので、コストは注意
容量減らしたい時はスナップショットでの復元時ならできる

AWS Backup
元々各サービスごとにあったバックアップ機能を一元管理できる

```
import json
import botocore
import boto3

backup = boto3.client('backup')

def lambda_handler(event, context):
    print ('Incoming Event:' + json.dumps(event))

    try:
        if event['Records'][0]['Sns']['Subject'] == 'Restore Test Status':
            print ('No action required, deletion of new resource confirmed.')
            return
    except Exception as e:
            print (str(e))
            return

    job_type = event['Records'][0]['Sns']['Message'].split('.')[-1].split(' ')[1]

    try:
        if 'failed' in event['Records'][0]['Sns']['Message']:
            print ('Something has failed. Please review the job in the AWS Backup console.')
            return 'Job ID:' + event['Records'][0]['Sns']['Message'].split('.')[-1].split(':')[1].strip()
        elif job_type == 'Backup':
            backup_job_id = event['Records'][0]['Sns']['Message'].split('.')[-1].split(':')[1].strip()
            backup_info = backup.describe_backup_job(
                            BackupJobId=backup_job_id
                        )
            #get backup job details
            recovery_point_arn = backup_info['RecoveryPointArn']
            iam_role_arn = backup_info['IamRoleArn']
            backup_vault_name = backup_info['BackupVaultName']
            resource_type = backup_info['ResourceType']

            metadata = backup.get_recovery_point_restore_metadata(
                BackupVaultName=backup_vault_name,
                RecoveryPointArn=recovery_point_arn
            )

            #determine resource type that was backed up and get corresponding metadata
            if resource_type == 'DynamoDB':
                metadata['RestoreMetadata']['targetTableName'] = metadata['RestoreMetadata']['originalTableName'] + '-restore-test'
            elif resource_type == 'EBS':
                ec2 = boto3.client('ec2')
                region = event['Records'][0]['Sns']['TopicArn'].split(':')[3]
                volumeid = event['Records'][0]['Sns']['Message'].split('.')[2].split('/')[1]

                metadata['RestoreMetadata']['availabilityZone'] = ec2.describe_volumes(
                VolumeIds=[
                    volumeid
                ]
                )['Volumes'][0]['AvailabilityZone']
            elif resource_type == 'RDS':
                metadata['RestoreMetadata']['DBInstanceIdentifier'] = event['Records'][0]['Sns']['Message'].split('.')[2].split(':')[7] + '-restore-test'
            elif resource_type == 'EFS':
                metadata['RestoreMetadata']['PerformanceMode'] = 'generalPurpose'
                metadata['RestoreMetadata']['newFileSystem'] = 'true'
                metadata['RestoreMetadata']['Encrypted'] = 'false'
                metadata['RestoreMetadata']['CreationToken'] = metadata['RestoreMetadata']['file-system-id'] + '-restore-test'

            #API call to start the restore job
            print ('Starting the restore job')
            restore_request = backup.start_restore_job(
                    RecoveryPointArn=recovery_point_arn,
                    IamRoleArn=iam_role_arn,
                    Metadata=metadata['RestoreMetadata']
            )

            print (json.dumps(restore_request))
            return

        elif job_type == 'Restore':
            restore_job_id = event['Records'][0]['Sns']['Message'].split('.')[-1].split(':')[1].strip()
            topic_arn = event['Records'][0]['Sns']['TopicArn']
            restore_info = backup.describe_restore_job(
                            RestoreJobId=restore_job_id
                        )
            resource_type = restore_info['CreatedResourceArn'].split(':')[2]

            print ('Restore from the backup was successful. Deleting the newly created resource.')

        #determine resource type that was restored and delete it to save cost
        if resource_type == 'dynamodb':
            dynamo = boto3.client('dynamodb')
            table_name = restore_info['CreatedResourceArn'].split(':')[5].split('/')[1]
            print ('Deleting: ' + table_name)
            delete_request = dynamo.delete_table(
                                TableName=table_name
                            )
        elif resource_type == 'ec2':
            ec2 = boto3.client('ec2')
            volume_id = restore_info['CreatedResourceArn'].split(':')[5].split('/')[1]
            print ('Deleting: ' + volume_id)
            delete_request = ec2.delete_volume(
                        VolumeId=volume_id
                    )
        elif resource_type == 'rds':
            rds = boto3.client('rds')
            database_identifier = restore_info['CreatedResourceArn'].split(':')[6]
            print ('Deleting: ' + database_identifier)
            delete_request = rds.delete_db_instance(
                        DBInstanceIdentifier=database_identifier,
                        SkipFinalSnapshot=True
                    )
        elif resource_type == 'elasticfilesystem':
            efs = boto3.client('efs')
            elastic_file_system = restore_info['CreatedResourceArn'].split(':')[5].split('/')[1]
            print ('Deleting: ' + elastic_file_system)
            delete_request = efs.delete_file_system(
                        FileSystemId=elastic_file_system
                    )

        sns = boto3.client('sns')

        print ('Sending deletion confirmation')
        #send a final notification confirming deletion of the newly restored resource
        notify = sns.publish(
            TopicArn=topic_arn,
            Message='Restore from ' + restore_info['RecoveryPointArn'] + ' was successful. The newly created resource ' + restore_info['CreatedResourceArn'] + ' has been cleaned up.' ,
            Subject='Restore Test Status'
        )

        print (json.dumps(notify))

        print (json.dumps(delete_request))
        return
    except Exception as e:
        print (str(e))
        return
```

Access Analizer for Amazon S3
外部に公開されているか
IAMと一緒

S3 オブジェクトロックモード
有効にすると、操作できない
途中から有効にすることはできないので作成時のみ設定可能
モードによって操作できる人もいる
リーガルホールドを適用すると無期限ロックになる

請求ダッシュボード
コストの確認
最大6時間の遅延がある

Cost Explorer
コストの分析
ダッシュボード

使用状況レポートはAWSで作ってくれて

Budgets
予算の計画を立てられる
予実管理

CloudWatch請求アラーム
バージニア北部だけで使える

サイズの適正化に関する推奨事項でコスト最適化が見える

Trusted Advisor
コスト、パフォーマンス、セキュリティについて機械学習でアドバイスしてくれる
コンサルみたいなもん
サポートプランをビジネス以上で使える

Compute Optimizer
EC2みたいなコンピュートサービスに対して機械学習でアドバイスしてくれる

Anomaly Detection
通常とは違うコストがかかった場合に教えてくれる

```
    {      "CidrIp": "0.0.0.0/0",      "FromPort": 22,      "IpProtocol": "tcp",      "ToPort": 22    }
```

