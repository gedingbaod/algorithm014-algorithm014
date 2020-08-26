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
# 4. 全排列（字节跳动在半年内面试常考）
# 5. 全排列 II （亚马逊、字节跳动、Facebook 在半年内面试中考过）
