```python
from collections import deque

def topological_sort(adj_list):
    """
    Performs a topological sort on a DAG.
    :param adj_list: dict where keys are nodes and values are lists of successor nodes
    :return: list of nodes in topologically sorted order
    """
    # Compute in-degrees
    in_degree = {u: 0 for u in adj_list}
    for u in adj_list:
        for v in adj_list[u]:
            in_degree[v] = in_degree.get(v, 0) + 1

    # Initialize queue with nodes of in-degree 0
    queue = deque([u for u in in_degree if in_degree[u] == 0])
    result = []

    while queue:
        u = queue.popleft()
        result.append(u)
        for v in adj_list.get(u, []):
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    # If result size != number of nodes, there was a cycle
    if len(result) != len(in_degree):
        raise ValueError("Graph is not a DAG (contains a cycle)")

    return result

# 使用例
if __name__ == "__main__":
    graph = {
        'A': ['C'],
        'B': ['C', 'D'],
        'C': ['E'],
        'D': ['F'],
        'E': ['H', 'F'],
        'F': ['G'],
        'G': [],
        'H': []
    }
    order = topological_sort(graph)
    print(order)  # 例: ['A', 'B', 'C', 'D', 'E', 'H', 'F', 'G']
```

時間計算量 O(V + E)  (V は頂点数、E は辺数)
