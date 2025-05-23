# TL;DR

全モデルが正答 **9260** を出したが、**4-1 ≒ 4o › o3 › o4-mini-high › o4-mini** の順でフォーマット厳密度と説明の丁寧さに差が見えた。

---

## 俯瞰

* **指示遵守**
  *5 項目*（番号列挙・公式提示・段階説明・最終行書式・余計な要素の有無）を全モデルが概ね満たした。
* **スタイル差**

  * 4-1 は 7 手順で細かく進め、最終行を太字で強調。
  * 4o はヘッダを挟んで 3 手順に圧縮し可読性を確保。
  * o3 は 5 手順＋数式ブロックで堅実。終端行が太字になっておらず軽微な逸脱。
  * o4-mini-high／o4-mini は箇条書き型で最短距離。終端行も非太字。
* **計算過程の透明度**
  4-1 と 4o が「係数を掛ける → 加算する」を明示し、途中値を一行ずつ示して最も親切。

> **ポイント**
> P2 のプロンプトが単純なため、**内容差よりも“表現の粒度”がモデル性能を映す鏡**になった。

---

## 詳細

### 1. 指示追従チェック

| モデル          | 番号列挙 | 公式利用 | 段階説明     | 最終行書式 (**【答え = …】**) | 追加装飾  |
| ------------ | ---- | ---- | -------- | -------------------- | ----- |
| 4-1          | ✔    | ✔    | 7 段階     | **太字**               | ―     |
| 4o           | ✔    | ✔    | 3 段階＋見出し | **太字**               | 見出しあり |
| o3           | ✔    | ✔    | 5 段階     | 普通字                  | ―     |
| o4-mini-high | ✔    | ✔    | 6 段階     | 普通字                  | ―     |
| o4-mini      | ✔    | ✔    | 6 段階     | 普通字                  | ―     |

> **ポイント**
> プロンプトは「太字」も要求しているため、4-1 と 4o が満点、他 3 モデルは 1 点減。

### 2. 説明の丁寧さ・読みやすさ

| モデル          | 手順粒度 | 数式ブロック         | コメント         | 読みやすさ |
| ------------ | ---- | -------------- | ------------ | ----- |
| 4-1          | 細かい  | インライン + 箇条書き   | 係数掛け・加算を逐次表示 | 高     |
| 4o           | 凝縮   | インライン & ディスプレイ | セクション分割で視線誘導 | 高     |
| o3           | 中庸   | ディスプレイ $…$     | 最小解説         | 中     |
| o4-mini-high | 簡潔   | インライン          | コメント少        | 中     |
| o4-mini      | 簡潔   | インライン          | コメント最少       | 中     |

### 3. 内省メモ

1. **評価軸の妥当性**
   P2 は計算系タスクなので「正答かどうか」がまず第一。全モデルが同値なため、差はフォーマットだけで良いか再確認 → 妥当。
2. **“4o” vs “o4～” 区別**
   計算系でも 4o は回答粒度が GPT-4 シリーズと同等で、o4-mini 系より明らかに余裕が感じられる。
3. **採点露出**
   フォーマット逸脱を 1 点差程度で評価したが、利用目的が「厳密なスタイル遵守重視」なら減点幅を広げる必要があるかもしれない。

---

## 要点テーブル（総合主観スコア 5 段階）

| モデル          | 指示遵守  | 丁寧さ   | 読みやすさ | 総合     |
| ------------ | ----- | ----- | ----- | ------ |
| 4-1          | ★★★★★ | ★★★★★ | ★★★★☆ | **最良** |
| 4o           | ★★★★★ | ★★★★☆ | ★★★★☆ | 準最良    |
| o3           | ★★★★☆ | ★★★☆☆ | ★★★☆☆ | 中庸     |
| o4-mini-high | ★★★☆☆ | ★★☆☆☆ | ★★★☆☆ | 簡潔     |
| o4-mini      | ★★★☆☆ | ★★☆☆☆ | ★★★☆☆ | 簡潔     |

---

## Related Topics

* 算術タスクにおける LLM の計算信頼性
* フォーマット評価と内容評価を分離する採点手法
* 小型モデルで Markdown スタイルを厳守させるプロンプトテクニック
