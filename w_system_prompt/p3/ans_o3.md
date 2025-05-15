# 結論 / TL;DR

キューを使う Kahn 法で、頂点と辺を一度ずつ処理して線形時間でソートできます。

## 俯瞰

| 何をするか           | なぜ重要か            | どうやるか                    | 計算量  |   |   |   |     |
| --------------- | ---------------- | ------------------------ | ---- | - | - | - | --- |
| DAG のトポロジカル順を得る | 依存順序の解決・ビルド順決定など | 入次数 0 の頂点から逐次取り出す Kahn 法 | \$O( | V | + | E | )\$ |

---

## 実装（最小コード）

```python
from collections import deque, defaultdict

def topo_sort(adj):
    """
    adj: dict[Any, list[Any]]
    return: list[Any] — トポロジカル順
    """
    indeg = defaultdict(int)
    for u, nbrs in adj.items():
        for v in nbrs:
            indeg[v] += 1
        indeg.setdefault(u, 0)           # 孤立頂点も初期化

    q = deque([u for u, d in indeg.items() if d == 0])
    order = []

    while q:
        u = q.popleft()
        order.append(u)
        for v in adj.get(u, []):
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    if len(order) != len(indeg):
        raise ValueError("DAG ではありません（閉路検出）")
    return order
```

時間計算量 \$O(|V|+|E|)\$ — 各頂点・辺を高々一度ずつ処理するのみ。
