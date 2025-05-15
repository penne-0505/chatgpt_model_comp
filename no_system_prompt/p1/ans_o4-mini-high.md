**TL;DR:**
オブジェクトストレージの整合性モデルには、強整合性・リード・アフター・ライト整合性・最終的整合性の３種類があり、性能・可用性・レイテンシーのトレードオフで使い分けられます。

## 全体像

* オブジェクトストレージは大量のデータを分散配置しつつ高い可用性を実現するため、書込み後の「いつ最新データを読めるか」という整合性モデルを選択します。
* CAP定理により、一貫性（Consistency）、可用性（Availability）、分断耐性（Partition Tolerance）のうち同時に満たせるのは２つまでで、整合性モデルはこのトレードオフを調整するものです ([SysTutorials][1])。

## 詳細

### 強整合性 (Strong Consistency)

* **定義：** あらゆる読取操作が常に最新の書込みを反映することを保証するモデル ([SysTutorials][1])。
* **メリット：** 更新直後でも必ず最新データを取得できるため、金融取引や分析パイプライン等での誤動作リスクを低減。
* **デメリット：** 分散ノード間の同期コストによりレイテンシや一部障害時の可用性が制約される場合がある。
* **主な事例：**

  * AWS S3（全GET/PUT/LIST操作が強整合） ([Amazon Web Services, Inc.][2], [Amazon Web Services, Inc.][3])
  * Google Cloud Storage（グローバル強整合） ([Stack Overflow][4])
  * Azure Blob Storage（LRS/ZRS） ([Microsoft Learn][5])

### リード・アフター・ライト整合性 (Read-After-Write Consistency)

* **定義：** クライアントが書込み後に行う全ての読取操作が、その書込みを必ず反映するモデル ([SysTutorials][1])。
* **メリット：** ユーザー操作直後のデータ反映を保証し、インタラクティブなWebアプリケーションに最適。
* **デメリット：** 他クライアントからの読取には一貫性を保証しないため、マルチクライアント環境では不整合が生じる可能性あり。
* **主な事例：**

  * AWS S3（旧モデルで新規オブジェクトPUT時に即時読取保証） ([Medium][6])

### 最終的整合性 (Eventual Consistency)

* **定義：** 更新が止まれば最終的に全ノードが同一データに収束することを保証するモデル ([SysTutorials][1])。
* **メリット：** レイテンシと可用性が高く、大規模分散やリージョン間レプリケーションに向く。
* **デメリット：** 更新直後は古いデータが返る可能性があり、一時的な不整合をアプリ側で許容・解消する仕組みが必要。
* **主な事例：**

  * AWS S3 クロスリージョンレプリケーション（CRR）は非同期で宛先に遅延あり） ([Stack Overflow][7])
  * Azure RA-GRS（読み取り可能なセカンダリが非同期更新で最終的整合） ([Microsoft Learn][8])

## まとめ

* **強整合性**：厳密な最新データ取得が必須のシステムに。
* **リード・アフター・ライト整合性**：自クライアントの即時反映が重要なインタラクティブ用途に。
* **最終的整合性**：高可用性・低レイテンシを優先し、一時的不整合を許容できるユースケースに。

| 整合性モード          | 整合速度      | 事例                                                                                                                                                               |
| --------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 強整合性            | 即時（全操作）   | AWS S3 ([Amazon Web Services, Inc.][2], [Amazon Web Services, Inc.][3]), Google Cloud Storage ([Stack Overflow][4]), Azure Blob (LRS/ZRS) ([Microsoft Learn][5]) |
| リード・アフター・ライト整合性 | 即時（新規作成時） | AWS S3旧モデル（新規オブジェクトPUT） ([Medium][6])                                                                                                                            |
| 最終的整合性          | 非同期（遅延あり） | AWS S3 CRR ([Stack Overflow][7]), Azure RA-GRS ([Microsoft Learn][8])                                                                                            |

[1]: https://www.systutorials.com/understanding-cloud-storage-consistency-models/ "Understanding Cloud Storage Consistency Models - SysTutorials"
[2]: https://aws.amazon.com/about-aws/whats-new/2020/12/amazon-s3-now-delivers-strong-read-after-write-consistency-automatically-for-all-applications/?utm_source=chatgpt.com "Amazon S3 now delivers strong read-after-write consistency ... - AWS"
[3]: https://aws.amazon.com/s3/consistency/?utm_source=chatgpt.com "Amazon S3 Strong Consistency"
[4]: https://stackoverflow.com/questions/33377643/what-does-eventual-or-strong-mean-in-the-context-of-google-cloud-storage-consist?utm_source=chatgpt.com "What does eventual or strong mean in the context of Google Cloud ..."
[5]: https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction?utm_source=chatgpt.com "Introduction to Azure Blob Storage - Learn Microsoft"
[6]: https://programming.am/understanding-s3-data-consistency-model-abe90e0681b8?utm_source=chatgpt.com "Understanding S3 Data Consistency Model | by Souren Stepanyan"
[7]: https://stackoverflow.com/questions/49186381/aws-s3-eventual-consistency-for-puts-and-deletes?utm_source=chatgpt.com "AWS S3 - Eventual Consistency for PUTS and DELETES"
[8]: https://learn.microsoft.com/en-us/azure/storage/common/geo-redundant-design?utm_source=chatgpt.com "Use geo-redundancy to design highly available applications - Azure ..."
