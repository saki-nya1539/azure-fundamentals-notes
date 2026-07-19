# Azure基礎知識まとめ

## 1. AWSとの対応関係

AWSでの学習経験をベースに、Azureの主要概念を対応づけて整理する。

| 概念 | AWS | Azure |
|---|---|---|
| 課金・契約の単位 | アカウント | サブスクリプション |
| リソースの入れ物 | (リージョンやタグで管理) | リソースグループ |
| 仮想サーバー | EC2 | 仮想マシン (VM) |
| オブジェクトストレージ | S3 | Blob Storage |
| 仮想ネットワーク | VPC | Virtual Network (VNet) |
| ファイアウォール設定 | セキュリティグループ | ネットワークセキュリティグループ (NSG) |
| ID・権限管理 | IAM | Microsoft Entra ID (旧Azure AD) |
| マネージドDB | RDS | Azure SQL Database / Azure Database for MySQL |
| サーバーレス実行 | Lambda | Azure Functions |
| 監視・ログ | CloudWatch | Azure Monitor |

AWSにはない概念として、Azure特有の「リソースグループ」がある。これは同じプロジェクトに属するリソース(VM、ディスク、NSG、IPなど)をひとまとめにするフォルダのような仕組みで、削除するときもグループごと一括削除できる。今回のハンズオンでも、後片付けの際にリソースグループを丸ごと削除するだけで、VM・NSG・ディスク・パブリックIP・VNetがすべて一度に削除された。

## 2. アカウントと無料枠

学生向けの「Azure for Students」を利用した。

- クレジットカード登録が不要(大学の学籍確認メールアドレスで認証)
- $100(約16,200円)分のクレジットが付与され、有効期限は365日
- 無料枠として「B1s仮想マシンへの750時間のアクセス」などが案内される

ただし今回、学生サブスクリプションでは日本東(Japan East)を含む複数リージョンで、無料枠対象のBシリーズ(B1s等)のクォータ(利用可能量)が0になっており、実際には使用できないという制約に遭遇した。これは学生サブスクリプション特有の制限で、Dシリーズ(有料)は問題なく利用できた。今回は短時間の検証後すぐに削除する前提で、有料のStandard_D2s_v3(約0.129USD/時間、約21円/時間)を利用した。

## 3. 責任共有モデル・課金の考え方

AWSと同様、Azureも「クラウド事業者(Microsoft)が担う範囲」と「利用者が担う範囲」で責任を分担する責任共有モデルを採用している。VMを立てた場合、OSやミドルウェアの設定・アップデート、アクセス制御(NSGの設定)は利用者の責任範囲であり、これはAWSのEC2と全く同じ考え方である。

課金は基本的に稼働時間(または確保したリソース量)に応じた従量課金制で、AWSのオンデマンド方式に相当する。VMを「停止」ではなく完全に「削除」しない限り、ディスクなど一部のリソースには課金が発生し続ける点もAWSと同様である。

## 4. Microsoft Entra ID(旧Azure AD)とテナントの構造

Entra IDはAWSのIAMに相当する、ユーザー・アクセス権限の管理サービスである。今回、大学の組織アカウント(テナント)経由でAzureにサインインしたため、Entra IDの管理画面を開こうとしたところ「アクセス権がありません」というエラーが表示された。

これは、Azureの「サブスクリプション(課金・リソース管理の単位)」と「テナント(組織全体のID管理の単位)」が別物であり、テナント全体のディレクトリ設定は大学のIT管理者が一元管理しているためである。個人の学生アカウントはテナントのメンバーではあるが、Entra IDの管理者権限までは持っていない。AWSでいえば、個人のIAMユーザーが組織のRoot権限を持たないのと同じ構造であり、実務でよくある「組織のガバナンスと個人の作業権限の分離」を体感する良い機会になった。

## 5. 今日の実体験との対応

| 実施した作業 | 対応する概念 |
|---|---|
| リソースグループ`rg-azure-handson`の作成 | Azure独自のリソース管理単位 |
| VMサイズ選択でのクォータエラー対応 | サブスクリプションごとのリソース制限 |
| NSGでSSHのソースIPを自分のIPに制限 | 責任共有モデルにおける利用者側の責任 |
| Ubuntu VM + Apache/PHP/MySQLの構築 | AWSのEC2 + LAMP構築と同一の実務経験 |
| 独自HTMLページの公開 | Webサーバーとしての実運用に近い体験 |
| リソースグループの削除による一括後片付け | Azure特有のリソース管理・コスト管理の仕組み |
| Entra IDへのアクセス権エラー | テナントとサブスクリプションの権限分離構造 |

## 関連リポジトリ

- [aws-lamp-terraform](https://github.com/saki-nya1539/aws-lamp-terraform) — AWS環境のTerraformコード
- [aws-lamp-setup-guide](https://github.com/saki-nya1539/aws-lamp-setup-guide) — AWSでのLAMP環境構築手順書
- [iac-ai-code-review](https://github.com/saki-nya1539/iac-ai-code-review) — 生成AIによるIaCコードレビュー練習記録
- [aws-fundamentals-notes](https://github.com/saki-nya1539/aws-fundamentals-notes) — AWS基礎知識まとめ
- [azure-lamp-setup-guide](https://github.com/saki-nya1539/azure-lamp-setup-guide) — Azureでのハンズオン手順書
