**TL;DR:**
オブジェクトストレージの整合性モデルは、更新後にどの程度早く正しいデータを読み出せるかを規定し、整合性保証とパフォーマンスのトレードオフになる。

## 全体像

オブジェクトストレージでは、データを多数のノードに分散して保存・複製するため、**いつ・どこから読んでも同じ最新データが返る保証**（整合性）をどこまで強く担保するかがサービスごとに異なります。大きくは以下の３つのモデルに分類できます。

1. **Strong consistency（線形整合性）**
2. **Read-after-write consistency（書き込み後即読み出し整合性）**
3. **Eventual consistency（最終的整合性）**

各モデルは、ノード間での同期方式やレプリケーションタイミングが異なり、**整合性の強さ**と**読み出しレイテンシ**がトレードオフになります。

---

## 詳細

### 1. Strong consistency（線形整合性）

* **定義:** 書き込みが完了した瞬間から、全ての読み出しが必ず最新の値を返す。
* **仕組み:** グローバルな合意（リーダー・フォロワー方式やPaxos/Raft等）を用い、全レプリカが更新後に同意してから応答。
* **利点:** データの一貫性が厳密に保たれるため、金融取引や在庫管理など高い正確性が求められる業務に最適。
* **欠点:** レプリケーション完了まで待つため書き込み／読み出し双方でレイテンシが高くなりがち。

### 2. Read-after-write consistency（書き込み後即読み出し整合性）

* **定義:** 同じクライアント（または同じセッション）から書き込んだオブジェクトは、その直後の読み出しで必ず最新値が返る。
* **仕組み:** クライアント単位でセッション情報を保持し、新規オブジェクトへのアクセスだけ強制的に最新を返す一方、更新（オーバーライト）は若干の遅延を許容。
* **利点:** 新規オブジェクト作成時の一貫性を保証しつつ、更新負荷を抑えられるため一般的なWebコンテンツ配信に適する。
* **欠点:** 更新（overwrite）後の読み出しは最終整合性となる場合があるので、更新頻度が高い用途にはやや不安が残る。

### 3. Eventual consistency（最終的整合性）

* **定義:** 最終的には全レプリカが同じデータになるが、即時ではなく一定時間のずれを許す。
* **仕組み:** 非同期レプリケーションを基本とし、更新後バックグラウンドで各ノードに伝播。
* **利点:** レプリケーションのオーバーヘッドが小さく、高スループット・低レイテンシでスケールアウトに強い。
* **欠点:** 更新直後に古い値が返る可能性があり、リアルタイム性や厳密な一貫性が求められる場面には不向き。

---

## まとめ

* **用途に応じてモデルを選定**：リアルタイム性・正確性重視ならStrong、一貫性と性能のバランスならRead-after-write、スケーラビリティと低レイテンシ重視ならEventual。
* **CAP定理との関係**：分散環境ではConsistencyとAvailability/Partition toleranceのトレードオフが存在。
* **サービス選択時のポイント**：SLAやSLOにおける整合性保証、許容レイテンシ、コストを総合的に勘案する。

| 整合性モード                                 | 整合速度（レイテンシ） | 事例                                                        |
| -------------------------------------- | ----------- | --------------------------------------------------------- |
| Strong consistency（線形整合性）              | 低速（高レイテンシ）  | AWS S3（2020年以降）, Google Cloud Storage, Azure Blob Storage |
| Read-after-write consistency（書込後即読整合性） | 中速          | AWS S3（新規オブジェクト）, 一部のマネージドS3互換ストレージ                       |
| Eventual consistency（最終的整合性）           | 高速（低レイテンシ）  | OpenStack Swift, Ceph RGW, 一部のオンプレOSSストレージ                |
