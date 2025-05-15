# 結論 / TL;DR

トポロジカルソートは「入次数を管理しながら幅優先（BFS, Kahn’s algorithm）」でO(V+E)で実装できる。

---

```python
from collections import deque, defaultdict

def topological_sort(adj):
    in_deg = defaultdict(int)
    for u in adj:
        for v in adj[u]:
            in_deg[v] += 1
        if u not in in_deg:
            in_deg[u] = 0
    q = deque([u for u in in_deg if in_deg[u] == 0])
    res = []
    while q:
        u = q.popleft()
        res.append(u)
        for v in adj.get(u, []):
            in_deg[v] -= 1
            if in_deg[v] == 0:
                q.append(v)
    if len(res) != len(in_deg):
        raise ValueError("グラフはDAGでない（閉路あり）")
    return res
```

> **ポイント**
>
> * 入次数0のノードから順に処理。
> * 入力 `adj` は {ノード: \[隣接ノード, ...]} のdict。
> * 閉路がある場合は例外を出す実装。
> * ノードの集合をadjキー＋値全てから抽出。

---

# 計算量

* 時間計算量 O(V+E)（V:ノード数, E:辺数）

  * 各ノード・各辺を高々1回ずつ処理。

---

# Related Topics

* DAG判定
* DFSによるトポロジカルソート
* 有向グラフのサイクル検出
* 順序付けの応用（依存関係解決など）
