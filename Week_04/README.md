# 0. 需要记忆的代码模板

# 0.1 DFS 递归写法
```Python
#Python
visited = set() 

def dfs(node, visited):
    if node in visited: # terminator
    	# already visited 
    	return 

	visited.add(node) 

	# process current node here. 
	...
	for next_node in node.children(): 
		if next_node not in visited: 
			dfs(next_node, visited)
```



```C++
//C/C++
//递归写法：
map<int, int> visited;

void dfs(Node* root) {
  // terminator
  if (!root) return ;

  if (visited.count(root->val)) {
    // already visited
    return ;
  }

  visited[root->val] = 1;

  // process current node here. 
  // ...
  for (int i = 0; i < root->children.size(); ++i) {
    dfs(root->children[i]);
  }
  return ;
}
```



```Java
//Java
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> allResults = new ArrayList<>();
        if(root==null){
            return allResults;
        }
        travel(root,0,allResults);
        return allResults;
    }


    private void travel(TreeNode root,int level,List<List<Integer>> results){
        if(results.size()==level){
            results.add(new ArrayList<>());
        }
        results.get(level).add(root.val);
        if(root.left!=null){
            travel(root.left,level+1,results);
        }
        if(root.right!=null){
            travel(root.right,level+1,results);
        }
    }
```



```JavaScript
//JavaScript
const visited = new Set()
const dfs = node => {
  if (visited.has(node)) return
  visited.add(node)
  dfs(node.left)
  dfs(node.right)
}
```

## 0.2 DSF 非递归写法
```Python
#Python
def DFS(self, tree): 

	if tree.root is None: 
		return [] 

	visited, stack = [], [tree.root]

	while stack: 
		node = stack.pop() 
		visited.add(node)

		process (node) 
		nodes = generate_related_nodes(node) 
		stack.push(nodes) 

	# other processing work 
	...
```



```C++
//C/C++
//非递归写法：
void dfs(Node* root) {
  map<int, int> visited;
  if(!root) return ;

  stack<Node*> stackNode;
  stackNode.push(root);

  while (!stackNode.empty()) {
    Node* node = stackNode.top();
    stackNode.pop();
    if (visited.count(node->val)) continue;
    visited[node->val] = 1;


    for (int i = node->children.size() - 1; i >= 0; --i) {
        stackNode.push(node->children[i]);
    }
  }

  return ;
}
```

## 0.3 BFS 代码模板
```Python
# Python
def BFS(graph, start, end):
    visited = set()
	queue = [] 
	queue.append([start]) 
	while queue: 
		node = queue.pop() 
		visited.add(node)
		process(node) 
		nodes = generate_related_nodes(node) 
		queue.push(nodes)
	# other processing work 
	...
```

```Java
//Java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
    }
}

public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> allResults = new ArrayList<>();
    if (root == null) {
        return allResults;
    }
    Queue<TreeNode> nodes = new LinkedList<>();
    nodes.add(root);
    while (!nodes.isEmpty()) {
        int size = nodes.size();
        List<Integer> results = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            TreeNode node = nodes.poll();
            results.add(node.val);
            if (node.left != null) {
                nodes.add(node.left);
            }
            if (node.right != null) {
                nodes.add(node.right);
            }
        }
        allResults.add(results);
    }
    return allResults;
}
```

```C++
// C/C++
void bfs(Node* root) {
  map<int, int> visited;
  if(!root) return ;

  queue<Node*> queueNode;
  queueNode.push(root);

  while (!queueNode.empty()) {
    Node* node = queueNode.top();
    queueNode.pop();
    if (visited.count(node->val)) continue;
    visited[node->val] = 1;

    for (int i = 0; i < node->children.size(); ++i) {
        queueNode.push(node->children[i]);
    }
  }

  return ;
}
```

```JavaScript
//JavaScript
const bfs = (root) => {
  let result = [], queue = [root]
  while (queue.length > 0) {
    let level = [], n = queue.length
    for (let i = 0; i < n; i++) {
      let node = queue.pop()
      level.push(node.val) 
      if (node.left) queue.unshift(node.left)
      if (node.right) queue.unshift(node.right)
    }
    result.push(level)
  }
  return result
};
```