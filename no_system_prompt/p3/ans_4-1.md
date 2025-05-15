1.

```python
def topological_sort(adj):
    from collections import deque

    # 入次数をカウント
    in_degree = {u: 0 for u in adj}
    for u in adj:
        for v in adj[u]:
            in_degree[v] = in_degree.get(v, 0) + 1

    # 入次数0のノードをキューに入れる
    queue = deque([u for u in in_degree if in_degree[u] == 0])
    order = []

    while queue:
        u = queue.popleft()
        order.append(u)
        for v in adj.get(u, []):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # ノード数と順序リストの長さが一致しない場合、グラフにサイクルあり
    if len(order) != len(in_degree):
        raise ValueError("Graph is not a DAG")
    return order
```

2.

**時間計算量 O(V + E)**（V: ノード数, E: エッジ数）

* 各ノード・各エッジを1回ずつ処理するため。
