```python
from collections import deque

def topological_sort(adj_list):
    # 各ノードの入次数を計算
    in_degree = {u: 0 for u in adj_list}
    for u in adj_list:
        for v in adj_list[u]:
            in_degree[v] = in_degree.get(v, 0) + 1

    # 入次数0のノードをキューに追加
    queue = deque([u for u in in_degree if in_degree[u] == 0])
    topo_order = []

    # Kahnのアルゴリズム
    while queue:
        u = queue.popleft()
        topo_order.append(u)
        for v in adj_list.get(u, []):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # サイクル検出
    if len(topo_order) != len(in_degree):
        raise ValueError("グラフにサイクルが含まれています")

    return topo_order
```

時間計算量は O(V + E) です。
