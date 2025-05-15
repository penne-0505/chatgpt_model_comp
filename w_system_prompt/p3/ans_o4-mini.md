# 結論 / TL;DR

Kahnのアルゴリズムを用いて、隣接リストからDAGのトポロジカルソートを効率的に実装できます。

## 実装例

```python
from collections import deque

def topological_sort(adj):
    # 1. 各頂点の入次数を初期化
    indegree = {u: 0 for u in adj}
    for u in adj:
        for v in adj[u]:
            indegree[v] = indegree.get(v, 0) + 1

    # 2. 入次数0の頂点をキューに追加
    queue = deque(u for u, d in indegree.items() if d == 0)
    result = []

    # 3. キューから取り出しつつ隣接頂点の入次数を更新
    while queue:
        u = queue.popleft()
        result.append(u)
        for v in adj.get(u, []):
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)

    return result
```

> **ポイント**: 入次数を管理し、入次数0の頂点から順に処理することで、DAG全体を一度だけ走査できます。

## 時間計算量

O(V + E)

# Related Topics

* DFSを用いたトポロジカルソート
* DAGのサイクル検出と処理
* トポロジカルソートの応用（依存関係解決など）
