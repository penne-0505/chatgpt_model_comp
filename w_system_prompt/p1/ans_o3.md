# TL;DR

オブジェクトストレージの整合性モデルは「書いた直後に最新データを必ず読めるか」を決める仕組みで、**強整合性ほど安全だがレイテンシやコストが増し、結果整合性ほどスケールと可用性を得られる**。

## 全体像

1. **背景**

   * クラウドのオブジェクトストレージは、複数リージョンへレプリケーションして耐障害性と無限スケールを実現。
   * CAP 定理により「一貫性 (Consistency)」「可用性 (Availability)」「分断耐性 (Partition-tolerance)」の全取りは不可能。
   * そこで各サービスは⾃社のワークロードに合わせ、整合性レベルをチューニング。

2. **整合性モデルの軸**

   * **強整合性**: 書き込み成功＝全リージョンへ反映完了。
   * **結果整合性**: 反映は非同期。ある時点で古いオブジェクトが読める可能性。
   * **ハイブリッド**: 新規作成は強、上書きは結果整合――など粒度を分けて提供。

> **ポイント**: 整合性は「アプリがリトライ・重複排除コードをどこまで持つか」を左右する。

## 詳細

### 1. 強整合性 (Strong Consistency)

* **何を**: 書き込みが成功した瞬間、世界中の `GET/LIST` が最新を返す。
* **なぜ**: 分散ロックやクォーラムで衝突を抑止し、アプリ側ロジックを簡素化するため。
* **どのように**:

  1. リーダー–フォロワ構成で全レプリカへコミット確認。
  2. 変更が全リージョンへ⾏き渡るまで応答をブロック。
* **代表例**

  * **Amazon S3**（2020 年以降の全バケット） ([Amazon Web Services, Inc.][1])
  * **Google Cloud Storage (GCS)** ([Google Cloud][2])
  * **Azure Blob Storage**（単一グローバル名前空間） ([Microsoft Learn][3])

> **ポイント**: レイテンシは RTT×レプリカ数が下限。大量小オブジェクト更新ではスループットに注意。

### 2. 結果整合性 (Eventual Consistency)

* **何を**: 時間経過により「いずれ」全レプリカが同一状態に収束。
* **なぜ**: レプリケーションを非同期にし、書き込みを即時応答してスループットと可用性を最優先。
* **どのように**:

  1. ローカルリージョンへ即コミットし ACK。
  2. バックグラウンドで他リージョンへ複製。
  3. 衝突はバージョニングまたは「最後に書いた方が勝ち」で解決。
* **代表例**: Ceph RGW、MinIO（デフォルト）、クロスリージョン S3 レプリケーション。

### 3. ハイブリッド／チューナブルモデル

* **Read-After-Write 強整合 + 上書きは結果整合**

  * 旧 AWS S3 や DigitalOcean Spaces が採用。
* **バウンデッド スタレネス／セッション整合**

  * Azure Cosmos DB が 5 段階整合性を提供 (Strong → Eventual) ([Microsoft Learn][4])

> **ポイント**: “秒以内に最新”といった SLA でアプリ側タイムアウトを簡単に設定できる利点。

## まとめ

* 強整合性はアプリ開発を楽にする代わりに「書き込み遅延」と「リージョン障害時の巻き添え」を招く。
* 結果整合性は高スループット・マルチリージョン可用性を実現する一方、アプリでリトライ・整合チェックが必須。
* ハイブリッド／チューナブルは速度と安全性をユースケースごとに最適化できる。

| 整合性モード                     | 整合速度 (目安)        | 事例 / 典型シナリオ              |
| -------------------------- | ---------------- | ------------------------ |
| 強整合性                       | ミリ秒級<br>(即時)     | AWS S3、GCS、Azure Blob    |
| 結果整合性                      | 秒〜数十秒            | Ceph RGW、MinIO、S3 CRR    |
| Read-After-Write 強 + 上書き遅延 | 新規は即時<br>上書きは秒単位 | 旧 S3、DigitalOcean Spaces |
| バウンデッド スタレネス               | SLA で N 秒以内      | Azure Cosmos DB (参考)     |
| セッション整合                    | 同一クライアントは即時      | Alibaba OSSなど            |

---

[1]: https://aws.amazon.com/about-aws/whats-new/2020/12/amazon-s3-now-delivers-strong-read-after-write-consistency-automatically-for-all-applications/?utm_source=chatgpt.com "Amazon S3 now delivers strong read-after-write consistency ... - AWS"
[2]: https://cloud.google.com/storage/docs/consistency "Cloud Storage consistency  |  Google Cloud"
[3]: https://learn.microsoft.com/en-us/azure/storage/blobs/concurrency-manage?utm_source=chatgpt.com "Manage concurrency in Blob Storage - Azure Storage | Microsoft Learn"
[4]: https://learn.microsoft.com/en-us/azure/cosmos-db/consistency-levels?utm_source=chatgpt.com "Consistency level choices - Azure Cosmos DB | Microsoft Learn"
