1. **実装（DFSベース）**

```python
def topological_sort(graph: dict[str, list[str]]) -> list[str]:
    visited = set()
    stack = []
    
    def dfs(node):
        if node in visited:
            return
        visited.add(node)
        for neighbor in graph.get(node, []):
            dfs(neighbor)
        stack.append(node)

    for node in graph:
        if node not in visited:
            dfs(node)

    return stack[::-1]  # 逆順にして返す
```

2. **時間計算量: `O(V + E)`**
   （V: ノード数、E: エッジ数）— 各ノード・エッジを一度ずつ探索するため。
