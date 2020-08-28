# 1.二叉树的最近公共祖先（Facebook 在半年内面试常考）
## 1.1 递归解法
```java
public TreeNode lowestCommonAncestor1(TreeNode root, TreeNode p, TreeNode q) {
        if (root == p || root == q) {
            return root;
        }
        if (root == null) {
            return null;
        }
        if (root.left == null && root.right == null) {
            return null;
        }
        TreeNode lnode = lowestCommonAncestor(root.left, p, q);
        TreeNode rnode = lowestCommonAncestor(root.right, p, q);
        if (lnode != null && rnode != null) {
            return root;
        } else if (lnode == null) {
            return rnode;
        } else if (rnode == null) {
            return lnode;
        }
        return null;
    }
```
## 1.2 简化解法 
```java
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) return root;
        TreeNode lnode = lowestCommonAncestor(root.left, p, q);
        TreeNode rnode = lowestCommonAncestor(root.right, q, q);
        if(lnode == null) return rnode;
        if(rnode == null) return lnode;
        return root;
    }
```
# 2. 从前序与中序遍历序列构造二叉树（字节跳动、亚马逊、微软在半年内面试中考过）
## 2.1 复制数组，性能较低
```java
    public TreeNode buildTree2(int[] preorder, int[] inorder) {
        if (preorder.length == 0) {
            return null;
        }
        //根据前序遍历，找到根节点
        TreeNode root = new TreeNode(preorder[0]);
        //根据中序遍历找到根节点
        int i = 0;
        for (; i < inorder.length; i++) {
            if (inorder[i] == preorder[0]) {
                break;
            }
        }
        //构建左子树
        int[] lpre = new int[i];
        int[] lino = new int[i];
        System.arraycopy(preorder, 1, lpre, 0, i);
        System.arraycopy(inorder, 0, lino, 0, i);
        root.left = buildTree(lpre, lino);
        //构建右子树
        int rsize = inorder.length - i - 1;
        int[] rpre = new int[rsize];
        int[] rino = new int[rsize];
        System.arraycopy(preorder, i + 1, rpre, 0, rsize);
        System.arraycopy(inorder, i + 1, rino, 0, rsize);
        root.right = buildTree(rpre, rino);

        return root;
    }
```
## 2.2 不拷贝版本，使用双指针
```java
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(preorder.length == 0) return null;
        return buildTree(preorder, 0, preorder.length, inorder, 0, inorder.length);
    }
    public TreeNode buildTree(int[] preorder, int p_start, int p_end, int[] inorder, int i_start, int i_end) {
        if (p_start == p_end) {
            return null;
        }
        //根据前序遍历，根节点为preorder的首个节点
        TreeNode root = new TreeNode(preorder[p_start]);

        //根据中序遍历找到根节点
        int i_root = i_start;
        for (; i_root < i_end; i_root++) {
            if (inorder[i_root] == preorder[p_start]) {
                break;
            }
        }
        int leftNum = i_root - i_start;
        //构建左子树
        root.left = buildTree(preorder, p_start + 1, p_start + leftNum + 1, inorder, i_start, i_root);
        //构建右子树
        root.right = buildTree(preorder, p_start + leftNum + 1, p_end, inorder, i_root + 1, i_end);
        return root;
    }
```
# 3. 组合（微软、亚马逊、谷歌在半年内面试中考过）
```java
List<List<Integer>> ret = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        if (n <= 0 || k <= 0 || n < k) {
            return ret;
        }
        findCombination(n, k, 1, new Stack<Integer>());
        return ret;
    }

    // p 可以声明成一个栈
    // i 的极限值满足： n - i + 1 = (k - pre.size())。
    // 【关键】n - i + 1 是闭区间 [i,n] 的长度。
    // k - pre.size() 是剩下还要寻找的数的个数。
    public void findCombination(int n, int k, int start, Stack<Integer> pre) {
        if (pre.size() == k) {
            ret.add(new ArrayList<>(pre));
            return;
        }
        for (int i = start; i < n - (k - pre.size()) + 1; i++) {
            pre.push(i);
            findCombination(n, k, i + 1, pre);
            pre.pop();
        }
    }
```
# 4. 全排列（字节跳动在半年内面试常考）
## 4.1 回溯法
```java
    public List<List<Integer>> permute1(int[] nums) {
        if (nums.length < 1) {
            return res;
        }
        findPermute(nums, new LinkedList<Integer>());
        return res;
    }

    public void findPermute(int[] nums, LinkedList<Integer> track) {
        if (nums.length == track.size()) {
            res.add(new ArrayList<>(track));
            return;
        }
        int size = nums.length;
        for (int i = 0; i < size; i++) {
            if (track.contains(nums[i])) {
                continue;
            }
            track.add(nums[i]);
            findPermute(nums, track);
            track.removeLast();
        }
    }
```
## 4.2 加强判断表达式
```java
    List<List<Integer>> res = new ArrayList<>();

    public List<List<Integer>> permute(int[] nums) {
        int[] visited = new int[nums.length];
        backTrace(nums, new LinkedList<Integer>(), visited);
        return res;
    }

    public void backTrace(int[] nums, LinkedList<Integer> track, int[] visited) {
        int size = nums.length;
        if (size == track.size()) {
            res.add(new ArrayList<>(track));
            return;
        }
        for (int i = 0; i < size; i++) {
            if (visited[i] == 1) {
                continue;
            }
            visited[i] = 1;
            track.add(nums[i]);
            backTrace(nums, track, visited);
            visited[i] = 0;
            track.removeLast();
        }
    }
```
# 5. 全排列 II （亚马逊、字节跳动、Facebook 在半年内面试中考过）
## 5.1 使用哈希去重
```java
    List<List<Integer>> res = new ArrayList<>();
    Map<String,Integer> map = new HashMap();
    public List<List<Integer>> permuteUnique(int[] nums) {
        int[] visited = new int[nums.length];
        backTrace(nums, new LinkedList<Integer>(), visited);
        return res;
    }

    public void backTrace(int[] nums, LinkedList<Integer> track, int[] visited) {
        int size = nums.length;
        if (size == track.size()) {
            res.add(new ArrayList<>(track));
            return;
        }
        for (int i = 0; i < size; i++) {
            if (visited[i] == 1) {
                continue;
            }

            //去重
            StringBuilder sb = new StringBuilder();
            for (Integer in : track) {
                sb.append(in).append("#");
            }
            sb.append(nums[i]);
            String str = sb.toString();
            if (map.containsKey(str)) {
                continue;
            }
            map.put(str,null);

            //回溯
            visited[i] = 1;
            track.add(nums[i]);
            backTrace(nums, track, visited);
            visited[i] = 0;
            track.removeLast();
        }
    }
```
## 5.2 剪枝的方式
```java
    public List<List<Integer>> permuteUnique(int[] nums) {
        if(nums.length < 1) return result;
        Arrays.sort(nums);
        trackBack(nums, new boolean[nums.length], new LinkedList<Integer>());
        return result;
    }

    public void trackBack(int[] nums, boolean[] visited, LinkedList<Integer> track) {
        if (track.size() == nums.length) {
            result.add(new LinkedList<>(track));
            return;
        }
        int size = nums.length;
        for (int i = 0; i < size; i++) {
            if(visited[i]) continue;
            //如果当前节点与他的前一个节点一样，并且他的前一个节点已经被遍历过了，那我们也就不需要了。
//            if(i > 0 && nums[i] == nums[i-1] && visited[i-1]) break;
            //如果当前节点与他的前一个节点一样，并且他的前一个节点未被遍历过，那我们就跳过。
            if(i > 0 && nums[i] == nums[i-1] && !visited[i-1]) continue;

            visited[i] = true;
            track.add(nums[i]);
            trackBack(nums, visited, track);
            visited[i] = false;
            track.removeLast();
        }
    }
```