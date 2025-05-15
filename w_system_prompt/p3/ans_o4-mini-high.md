# 結論 / TL;DR

KahnのアルゴリズムでDAGの隣接リストからトポロジカルソートを行うPython実装例です。

## 実装例

```python
from collections import deque

def topo_sort(adj):
    # 各頂点の入次数を計算
    indeg = {u: 0 for u in adj}
    for u in adj:
        for v in adj[u]:
            indeg[v] = indeg.get(v, 0) + 1

    # 入次数0の頂点をキューに追加
    dq = deque([u for u, d in indeg.items() if d == 0])
    res = []

    # キューから頂点を取り出し結果に追加、隣接頂点の入次数を減らす
    while dq:
        u = dq.popleft()
        res.append(u)
        for v in adj[u]:
            indeg[v] -= 1
            if indeg[v] == 0:
                dq.append(v)

    return res
```

> **時間計算量** O(V + E)

# Related Topics

* 深さ優先探索によるトポロジカルソート
* グラフのサイクル検出
* DAG上の最長パス問題
