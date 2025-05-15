# 結論 / TL;DR

DAG（有向非巡回グラフ）に対するトポロジカルソートは、**入次数管理によるKahnのアルゴリズム**で実装できる。

---

## 実装

```python
from collections import deque, defaultdict

def topological_sort(graph):
    in_degree = defaultdict(int)
    for u in graph:
        for v in graph[u]:
            in_degree[v] += 1

    # 入次数が0のノードをキューに入れる
    queue = deque([node for node in graph if in_degree[node] == 0])
    result = []

    while queue:
        u = queue.popleft()
        result.append(u)
        for v in graph[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    if len(result) != len(graph):
        raise ValueError("入力グラフはDAGではありません")

    return result
```

> **ポイント**: `graph` は `{node: [隣接ノードのリスト]}` の形式。孤立ノードも忘れず `[]` で定義すること。

---

## 時間計算量

**O(V + E)**
（各ノードと辺を一度ずつ処理する）

---

## Related Topics

* DFSベースのトポロジカルソート（スタック活用）
* DAG検出（サイクル検出アルゴリズム）
* 依存関係解決（ビルド順序、課題スケジューリング）
