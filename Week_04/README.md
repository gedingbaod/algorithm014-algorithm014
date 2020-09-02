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

```java
//DFS的迭代实现版本（Stack）
public void DFSWithStack(TreeNode root) {
     if (root == null)
         return;
     Stack<TreeNode> stack = new Stack<>();
     stack.push(root);

     while (!stack.isEmpty()) {
         TreeNode treeNode = stack.pop();

         //在这里处理遍历到的TreeNode

         if (treeNode.right != null)
             stack.push(treeNode.right);

         if (treeNode.left != null)
             stack.push(treeNode.left);
     }
}
```


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

```java
//使用Queue实现BFS
public void BFSWithQueue(Node root) {
    Queue<Node> queue = new LinkedList<>();
    if (root != null)
        queue.add(root);
    Set<Node> visited = new HashSet<>();
    
    while (!queue.isEmpty()) {
        Node node = queue.poll();
        visited.add(node);
 
        //在这里处理遍历到的Node节点
 
        if (node.children != null) {
            for (Node child : node.children) {
                if (child != null && !visited.contains(child){
                    queue.add(child);
                }
            }
        }
    }
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

# 1. 模拟行走机ac亚马逊在半年内面试中考过）
# 2. 模拟行走机ab佳时机 II （亚马逊、字节跳动、微软在半年内面试中考过）
# 3. 模拟行走机aa逊在半年内面试中考过）
# 4. 模拟行走机器人
# 5. 使用二分查找，寻找一个半有序数组 [4, 5, 6, 7, 0, 1, 2] 中间无序的地方
# 6. 说明：同学们可以将自己的思路、代码写在第 4 周的学习总结中
# 7. 单词接龙（亚马逊在半年内面试常考）
# 8. 岛屿数量（近半年内，亚马逊在面试中考查此题达到 350 次）
## 8.1 深度优先
```java
    public int numIslands(char[][] grid) {
        if(grid == null || grid.length == 0) return 0;

        int gr = grid.length;
        int gc = grid[0].length;
        int counter = 0;

        for (int i = 0; i < gr; i++) {
            for (int j = 0; j < gc; j++) {
                if (grid[i][j] == '1') {
                    DFSMarking(grid, i, j);
                    counter++;
                }
            }
        }
        return counter;
    }
    public void DFSMarking(char[][] grid, int i, int j) {
        int gr = grid.length;
        int gc = grid[0].length;
        if(i < 0 || j < 0 || i >= gr || j >= gc || grid[i][j] == '0') return;

        grid[i][j] = '0';
        DFSMarking(grid, i - 1, j);
        DFSMarking(grid, i + 1, j);
        DFSMarking(grid, i, j - 1);
        DFSMarking(grid, i, j + 1);
    }
```

# 9. 扫雷游戏（亚马逊、Facebook 在半年内面试中考过）
# 10. 跳跃游戏 （亚马逊、华为、Facebook 在半年内面试中考过）
## 10.1 正着跳
```java
public boolean canJump2(int[] nums) {
        int n = nums.length;
        int rightmost = 0;
        for (int i = 0; i < n; i++) {
            if (i <= rightmost) {
                rightmost = Math.max(rightmost, i + nums[i]);
                if (rightmost >= n - 1) {
                    return true;
                }
            }
        }
        return false;
    }
```
## 10.2 反着跳
```java
    public boolean canJump(int[] nums) {
        if(nums == null) return false;
        int endReachable = nums.length - 1;
        //因为最后一个不用判断，可以直接从倒数第二个开始
        for (int i = endReachable - 1; i >= 0; i--) {
            if (nums[i] + i >= endReachable) {
                endReachable = i;
            }
        }
        return endReachable == 0;
    }
```
# 11. 搜索旋转排序数组（Facebook、字节跳动、亚马逊在半年内面试常考）
# 12. 搜索二维矩阵（亚马逊、微软、Facebook 在半年内面试中考过）
# 13. 寻找旋转排序数组中的最小值（亚马逊、微软、字节跳动在半年内面试中考过）
# 14. 单词接龙 II （微软、亚马逊、Facebook 在半年内面试中考过）
# 15. 跳跃游戏 II （亚马逊、华为、字节跳动在半年内面试中考过）