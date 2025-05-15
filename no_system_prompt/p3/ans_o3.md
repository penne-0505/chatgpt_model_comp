```python
from collections import defaultdict, deque
from typing import Dict, List, Hashable

def topological_sort(adj: Dict[Hashable, List[Hashable]]) -> List[Hashable]:
    """
    Kahn’s algorithm for topological sorting.
    Parameters
    ----------
    adj : dict
        Adjacency list representing a directed acyclic graph (DAG).
        Keys are nodes; values are iterable collections of successor nodes.
    Returns
    -------
    list
        A list of nodes in topologically sorted order.
    Raises
    ------
    ValueError
        If the graph contains a cycle (i.e., is not a DAG).
    """
    # 1.  indegree 集計
    indeg = defaultdict(int)
    for u, nbrs in adj.items():
        indeg.setdefault(u, 0)
        for v in nbrs:
            indeg[v] += 1

    # 2.  indegree 0 の頂点をキューへ
    q = deque([v for v, d in indeg.items() if d == 0])
    order = []

    # 3.  幅優先で除去しながらトポロジカル順序を構築
    while q:
        u = q.popleft()
        order.append(u)
        for v in adj.get(u, []):
            indeg[v] -= 1
            if indeg[v] == 0:
                q.append(v)

    # 4.  全頂点が処理済みか確認（= DAG であることを保証）
    if len(order) != len(indeg):
        raise ValueError("Graph is not a DAG (cycle detected).")

    return order
```

時間計算量 O(V + E) — 各頂点と各辺を高々一度ずつ処理するだけで完結するため。
