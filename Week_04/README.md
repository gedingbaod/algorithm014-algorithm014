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
## 1.1 暴力求解
```java
    public boolean lemonadeChange02(int[] bills) {
        if (bills == null || bills.length == 0) {
            return false;
        }
        int size = bills.length;
        int[] inside = new int[3];//20元可以省略，但是真实记账一定是要记得
        for (int i = 0; i < size; i++) {
            int recieve = bills[i];
            if (recieve == 5) {
                inside[0]++;
            } else if (recieve == 10 && inside[0] > 0) {
                inside[1]++;
                inside[0]--;
            } else if (recieve == 20 && inside[0] > 0 && inside[1] > 0)  {
                inside[2]++;
                inside[1]--;
                inside[0]--;
            } else if (recieve == 20 && inside[0] >= 3) {
                inside[2]++;
                inside[0] -= 3;
            } else {
                return false;
            }
        }
        return true;
    }
```
## 1.2 贪心算法,1改变for循环，2改变存储方式，3改变判断方式，4先减为负数，再判断结束条件
```java
    public boolean lemonadeChange01(int[] bills) {
        int five = 0, ten = 0;
        for (int bill : bills) {
            switch (bill) {
                case 5: five++; break;
                case 10: five--; ten++; break;
                case 20: {
                    if (ten > 0) {
                        ten--;
                        five--;
                    } else {
                        five -= 3;
                    }
                    break;
                }
                default: break;
            }
            if (five < 0) {
                return false;
            }
        }
        return true;
    }
```
# 2. 买卖股票的最佳时机 II （亚马逊、字节跳动、微软在半年内面试中考过）
## 2.1 递归方法，算法超时
```java
    public int maxProfit02(int[] prices) {
        return calculate(prices, 0);
    }

    //双指针方法，利润=卖出指针-买入指针
    public int calculate(int[] prices, int s) {
        if(s >= prices.length) return 0;
        int maxSum = 0;//买入时间不同，取最大值
        for (int bit = s; bit < prices.length; bit++) {
            int maxProfit = 0;//卖出时机不同，取最大值
            for (int down = bit + 1; down < prices.length; down++) {
                if (prices[down] > prices[bit]) {
                    int profit = calculate(prices, down + 1) + prices[down] - prices[bit];
                    if(profit > maxProfit) maxProfit = profit;
                }
            }
            if(maxProfit > maxSum) maxSum = maxProfit;
        }
        return maxSum;
    }
```
## 2.2 波峰波谷，每个波谷到波峰的叠加，就是最大利润
```java
    public int maxProfit01(int[] prices) {
        int valley = prices[0];
        int peak = prices[0];
        int maxProfit = 0;
        int step = 0;
        int maxStep = prices.length - 1;
        while (step < maxStep) {
            while (step < maxStep && prices[step] >= prices[step + 1]) {
                step++;
            }
            valley = prices[step];
            while (step < maxStep && prices[step] <= prices[step + 1]) {
                step++;
            }
            peak = prices[step];
            maxProfit += peak - valley;
        }
        return maxProfit;
    }
```
## 2.3 贪心算法
```java
    public int maxProfit00(int[] prices) {
        int maxProfit = 0;
        for (int i = 1; i < prices.length; i++) {
            if (prices[i] > prices[i - 1]) {//只要当前一步有利润，就累加
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        return maxProfit;
    }
```
## 2.4 动态规划
```java
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len < 2) return 0;
        // cash：持有现金
        // hold：持有股票
        // 状态数组
        // 状态转移：cash → hold → cash → hold → cash → hold → cash
        int[] cash = new int[len];
        int[] hold = new int[len];

        cash[0] = 0;
        hold[0] = -prices[0];

        for (int i = 1; i < len; i++) {
            cash[i] = Math.max(cash[i - 1], hold[i - 1] + prices[i]);
            hold[i] = Math.max(hold[i - 1], cash[i - 1] - prices[i]);
        }
        return cash[len - 1];
    }
```
# 3. 分发饼干（亚马逊在半年内面试中考过）
## 3.1 贪心算法
```java
    public int findContentChildren(int[] grid, int[] size) {
        if (grid == null || size == null) return 0;
        Arrays.sort(grid);
        Arrays.sort(size);
        int gi = 0, si = 0;
        while (gi < grid.length && si < size.length) {
            if (grid[gi] <= size[si]) {
                gi++;
            }
            si++;
        }
        return gi;
    }
```
# 4. 模拟行走机器人
## 4.1 将障碍物的x和y坐标组合成一个字符串用set保存障碍物，查找的时候只要判断当前坐标组成的串是否在set里。
```java
public int robotSim02(int[] commands, int[][] obstacles) {
        int[][] dir = { { 0, 1 }, { 1, 0 }, { 0, -1 }, { -1, 0 } };
        int x = 0, y=0;
        int dir_index=0;
        int ans = 0;
        Set<String> blockSet = new HashSet<String>();
        for (int i=0;i<obstacles.length;i++) {
            blockSet.add(obstacles[i][0]+","+obstacles[i][1]);
        }

        for (int i=0;i<commands.length;i++) {
            if (commands[i]==-1) {
                dir_index=(dir_index+1)%4;
            }else if (commands[i]==-2) {
                dir_index=(dir_index+3)%4;
            }
            else if (commands[i]>0) {
                for (int j=1;j<=commands[i];j++) {
                    int next_x = x+ dir[dir_index][0];
                    int next_y = y+ dir[dir_index][1];
                    if (blockSet.contains(next_x+","+next_y)) {
                        break;
                    }else {
                        x = next_x;
                        y = next_y;
                        ans = Math.max(ans, x*x+y*y);
                    }
                }
            }
        }
        return ans;
    }
```
## 4.2 每移动一步前，判断该位置是否为障碍物，如果存在障碍物，结束当前路径的移动
```java
    public int robotSim(int[] commands, int[][] obstacles) {
        //direction表当前朝向，0123 表 北东南西
        int ans = 0,direction = 0,x = 0,y = 0;
        //每个朝向上的数据变化，比如朝北时取Direction[0]  ->   {0,1}
        //那么x轴的变化为x+0，y轴变化为y+1;
        int[][] Direction = {{0,1},{1,0},{0,-1},{-1,0}};

        HashSet<String> set = new HashSet<>();
        //将所有障碍物坐标组合成字符串存入set中方便查询
        for (int[] arr : obstacles) set.add(arr[0]+"，"+arr[1]);

        for (int com : commands){
            //定义下一步的坐标
            int next_x = 0,next_y = 0;
            //当命令为前进，开始移动
            if (com >= 0 ){
                for(int i = 0; i < com; i++){
                    //取得下一步的坐标
                    next_x = x + Direction[direction][0];
                    next_y = y + Direction[direction][1];
                    //若下一步有障碍物，结束当前命令，跳至下一命令
                    if(set.contains(next_x+"，"+next_y)) break;
                    //否则更新坐标与最远距离
                    x = next_x;
                    y = next_y;
                    ans = Math.max(ans, x*x + y*y);
                }
            }else{
                //改变朝向
                direction = com == -1 ? (direction + 1) % 4 : (direction + 3) % 4;
            }
        }
        return ans;
    }
```
# 5. 使用二分查找，寻找一个半有序数组 [4, 5, 6, 7, 0, 1, 2] 中间无序的地方
     说明：同学们可以将自己的思路、代码写在第 4 周的学习总结中
# 6. 单词接龙（亚马逊在半年内面试常考）
## 6.1 广度优先遍历
```java
//广度优先遍历
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 第 1 步：先将 wordList 放到哈希表里，便于判断某个单词是否在 wordList 里
        Set<String> wordSet = new HashSet<>(wordList);
        if (wordSet.size() == 0 || !wordSet.contains(endWord)) {
            return 0;
        }
        wordSet.remove(beginWord);

        // 第 2 步：图的广度优先遍历，必须使用队列和表示是否访问过的 visited 哈希表
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        Set<String> visited = new HashSet<>();
        visited.add(beginWord);

        // 第 3 步：开始广度优先遍历，包含起点，因此初始化的时候步数为 1
        int step = 1;
        while (!queue.isEmpty()) {
            int currentSize = queue.size();
            for (int i = 0; i < currentSize; i++) {
                // 依次遍历当前队列中的单词
                String currentWord = queue.poll();
                // 如果 currentWord 能够修改 1 个字符与 endWord 相同，则返回 step + 1
                if (changeWordEveryOneLetter(currentWord, endWord, queue, visited, wordSet)) {
                    return step + 1;
                }
            }
            step++;
        }
        return 0;
    }
    //尝试对 currentWord 修改每一个字符，看看是不是能与 endWord 匹配
    private boolean changeWordEveryOneLetter(String currentWord, String endWord,
                                             Queue<String> queue, Set<String> visited, Set<String> wordSet) {
        char[] charArray = currentWord.toCharArray();
        for (int i = 0; i < endWord.length(); i++) {
            // 先保存，然后恢复
            char originChar = charArray[i];
            for (char k = 'a'; k <= 'z'; k++) {
                if (k == originChar) {
                    continue;
                }
                charArray[i] = k;
                String nextWord = String.valueOf(charArray);
                if (wordSet.contains(nextWord)) {
                    if (nextWord.equals(endWord)) {
                        return true;
                    }
                    if (!visited.contains(nextWord)) {
                        queue.add(nextWord);
                        // 注意：添加到队列以后，必须马上标记为已经访问
                        visited.add(nextWord);
                    }
                }
            }
            // 恢复
            charArray[i] = originChar;
        }
        return false;
    }
```
## 6.2 双向广度优先遍历
```java
public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        // 第 1 步：先将 wordList 放到哈希表里，便于判断某个单词是否在 wordList 里
        Set<String> wordSet = new HashSet<>(wordList);
        if (wordSet.size() == 0 || !wordSet.contains(endWord)) {
            return 0;
        }

        // 第 2 步：已经访问过的 word 添加到 visited 哈希表里
        Set<String> visited = new HashSet<>();
        // 分别用左边和右边扩散的哈希表代替单向 BFS 里的队列，它们在双向 BFS 的过程中交替使用
        Set<String> beginVisited = new HashSet<>();
        beginVisited.add(beginWord);
        Set<String> endVisited = new HashSet<>();
        endVisited.add(endWord);

        // 第 3 步：执行双向 BFS，左右交替扩散的步数之和为所求
        int step = 1;
        while (!beginVisited.isEmpty() && !endVisited.isEmpty()) {
            // 优先选择小的哈希表进行扩散，考虑到的情况更少
            if (beginVisited.size() > endVisited.size()) {
                Set<String> temp = beginVisited;
                beginVisited = endVisited;
                endVisited = temp;
            }

            // 逻辑到这里，保证 beginVisited 是相对较小的集合，nextLevelVisited 在扩散完成以后，会成为新的 beginVisited
            Set<String> nextLevelVisited = new HashSet<>();
            for (String word : beginVisited) {
                if (changeWordEveryOneLetter(word, endVisited, visited, wordSet, nextLevelVisited)) {
                    return step + 1;
                }
            }

            // 原来的 beginVisited 废弃，从 nextLevelVisited 开始新的双向 BFS
            beginVisited = nextLevelVisited;
            step++;
        }
        return 0;
    }


    //尝试对 word 修改每一个字符，看看是不是能落在 endVisited 中，扩展得到的新的 word 添加到 nextLevelVisited 里
    private boolean changeWordEveryOneLetter(String word, Set<String> endVisited,
                                             Set<String> visited,
                                             Set<String> wordSet,
                                             Set<String> nextLevelVisited) {
        char[] charArray = word.toCharArray();
        for (int i = 0; i < word.length(); i++) {
            char originChar = charArray[i];
            for (char c = 'a'; c <= 'z'; c++) {
                if (originChar == c) {
                    continue;
                }
                charArray[i] = c;
                String nextWord = String.valueOf(charArray);
                if (wordSet.contains(nextWord)) {
                    if (endVisited.contains(nextWord)) {
                        return true;
                    }
                    if (!visited.contains(nextWord)) {
                        nextLevelVisited.add(nextWord);
                        visited.add(nextWord);
                    }
                }
            }
            // 恢复，下次再用
            charArray[i] = originChar;
        }
        return false;
    }
```
# 7. 岛屿数量（近半年内，亚马逊在面试中考查此题达到 350 次）
## 7.1 深度优先
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

# 8. 扫雷游戏（亚马逊、Facebook 在半年内面试中考过）
# 9. 跳跃游戏 （亚马逊、华为、Facebook 在半年内面试中考过）
## 9.1 正着跳
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
## 9.2 反着跳
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
# 10. 搜索旋转排序数组（Facebook、字节跳动、亚马逊在半年内面试常考）
# 11. 搜索二维矩阵（亚马逊、微软、Facebook 在半年内面试中考过）
# 12. 寻找旋转排序数组中的最小值（亚马逊、微软、字节跳动在半年内面试中考过）
# 13. 单词接龙 II （微软、亚马逊、Facebook 在半年内面试中考过）
# 14. 跳跃游戏 II （亚马逊、华为、字节跳动在半年内面试中考过）