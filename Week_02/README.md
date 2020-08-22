# 1. 写一个关于 HashMap 的小总结
## 1.1 继承关系
       extends AbstractMap<K,V>
       implements Map<K,V>, Cloneable, Serializable
## 1.2 数据结构：数组+链表
       整体使用数组(Node<K,V>[] table)存储数据，当出现哈希碰撞时，碰撞到的值，存入节点的链表中。
       节点定义的Node<K,V>，实现了Map.Entry<K,V>接口，同时包含四个属性，hash,key,value,next
       Node的hash值计算：Objects.hashCode(key) ^ Objects.hashCode(value);
       还有一种是TreeNode，是一颗红黑树，这里不做说明。
## 1.3 重点函数put
```java
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //检查数组是否为空，如果为空则初始化
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //计算哈希值，得到位置p
        if ((p = tab[i = (n - 1) & hash]) == null)
            //如果对应的哈希位置为空，则直接存入数据。
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            //如果哈希和key一致，用新的value替换掉旧的value
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //如果p是TreeNode类型，放入红黑树中
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            //如果不是TreeNode就是链表，
            else {
                for (int binCount = 0; ; ++binCount) {
                    //数据加到链表尾部。
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //链表中已存在插入值
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //如果是已存在的Key，更新value
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        //修改计数器加1
        ++modCount;
        //节点计数器加1
        if (++size > threshold)
            resize();
        //回调函数
        afterNodeInsertion(evict);
        return null;
    }
```
## 1.4 重点函数get，不存在时返回空
```java
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        //如果map为空，而且哈希大于数组，则返回null
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            //找到哈希相同，key相同的值，并返回节点
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            //查找哈希碰撞的情况
            if ((e = first.next) != null) {
                //如果是红黑树的情况
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                //在链表中逐个查找key相同的节点
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```
## 1.5 小结
        仔细研究的话，HashMap还是有很多复杂的地方，如果resize就很复杂
        但是从总体思路来说，HashMap是维护了一个数组，
        通过计算hash(key)得到一个哈希值，使得数据分散的存储在数组中。
        如果恰好两个key的哈希值一样，则使用链表存储对象。


# 2. 有效的字母异位词（亚马逊、Facebook、谷歌在半年内面试中考过）

## 2.1  O(nlog n) 3ms 使用自带工具排序，比较排序后的结果
    public static boolean isAnagram2(String s, String t) {
        char[] charA = s.toCharArray();
        char[] charB = t.toCharArray();
        //O(nlog n)
        Arrays.sort(charA);
        Arrays.sort(charB);
        s = String.valueOf(charA);
        t = String.valueOf(charB);
        return s.equals(t);
    }
## 2.2 O(m+n) 15mn 使用内置哈希表，但是效果不理想，原因是HashMap操作耗时
    public static boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        Map<Character, Integer> mapS = new HashMap<>();
        Map<Character, Integer> mapT = new HashMap<>();
        for (char c : s.toCharArray()) {
            mapS.put(c, mapS.getOrDefault(c, 0) + 1);
        }
        for (char c : t.toCharArray()) {
            mapT.put(c, mapT.getOrDefault(c, 0) + 1);
        }
        if (mapS.size() != mapT.size()) {
            return false;
        }
        for (Character c : mapS.keySet()) {
            if (!mapS.get(c).equals(mapT.getOrDefault(c, 0))) {
                return false;
            }
        }
        return true;
    }
## 2.3 O(m+n) 4mn 因为字符集只有26个，根据哈希的思想，做一个简易的哈希表。
    public static boolean isAnagram100(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        int[] counter = new int[26]; //哈希表
        for (int i = 0; i < s.length(); i++) {
            counter[s.charAt(i) - 'a']++;
        }
        for (int i = 0; i < s.length(); i++) {
            int index = t.charAt(i) - 'a';
            counter[index]--;
            if (counter[index] < 0) {
                return false;
            }
        }
        return true;
    }
## 2.4 O(m+n) 2ms 在对for循环进行优化
    public static boolean isAnagram0(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        int[] counter = new int[26]; //哈希表
        for (char c : s.toCharArray()) {
            counter[c - 97]++;
        }
        for (char c : t.toCharArray()) {
            counter[c - 97]--;
            if (counter[c - 97] < 0) {
                return false;
            }
        }
        return true;
    }
# 3. 两数之和（近半年内，亚马逊考查此题达到 216 次、字节跳动 147 次、谷歌 104 次，Facebook、苹果、微软、腾讯也在近半年内面试常考）
## 3.1 通过哈希得到与当前值对应的值是否存在
    public static int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("not find");
    }
# 4. 589. N叉树的前序遍历（亚马逊在半年内面试中考过）
## 4.1 使用递归方式 3mn
```java
    public static List<Integer> preorder2(Node root) {
        List<Integer> ret = new ArrayList<>();
        if (root == null) {
            return ret;
        }
        ret.add(root.val);
        if (root.children != null) {
            for (Node leaf : root.children) {
                ret.addAll(preorder(leaf));
            }
        }
        return ret;
    }

```
## 4.2 把数组放到外面
```java
    List<Integer> res = new ArrayList<Integer>();
    public List<Integer> preorder(Node root) {
        inOrder(root);
        return res;
    }
    public void inOrder(Node root) {
        if(root == null) {
            return;
        }
        res.add(root.val);
        int s = root.children.size();
        for(int i = 0; i < s; i++) {
            inOrder(root.children.get(i));
        }
    }
```
# 5. HeapSort ：自学 https://www.geeksforgeeks.org/heap-sort/

# 6. 49. 字母异位词分组 （亚马逊在半年内面试中常考）
## 6.1  通过字符串指纹确定哈希
```java
    public List<List<String>> groupAnagrams1(String[] strs) {
        Map<String, List<String>> fpMap = new HashMap();
        int[] counter = new int[26];
        for (String str : strs) {
            Arrays.fill(counter, 0);
            for (char c : str.toCharArray()) {
                counter[c - 97]++;
            }
            StringBuilder sb = new StringBuilder("");
            for (int i = 0; i < 26; i++) {
                sb.append('#');
                sb.append(counter[i]);
            }
            String key = sb.toString();

            if (!fpMap.containsKey(key)) {
                fpMap.put(key,new ArrayList<String>());
            }
            fpMap.get(key).add(str);
        }
        return new ArrayList(fpMap.values());
}
```
## 6.2 通多使用质数的方式，减少Key的计算
```java
    public List<List<String>> groupAnagrams(String[] strs) {

        // 考察了哈希函数的基本知识，只要 26 个即可
        // （小写字母ACSII 码 - 97 ）以后和质数的对应规则，这个数组的元素顺序无所谓
        // key 是下标，value 就是数值
        int[] primes = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29,
                31, 37, 41, 43, 47, 53, 59, 61, 67, 71,
                73, 79, 83, 89, 97, 101};
        // key 是字符串自定义规则下的哈希值
        Map<Integer, List<String>> hashMap = new HashMap<>();
        for (String s : strs) {
            int hashValue = 1;

            char[] charArray = s.toCharArray();
            for (char c : charArray) {
                hashValue *= primes[c - 'a'];
            }

            // 把单词添加到哈希值相同的分组
            if (hashMap.containsKey(hashValue)) {
                List<String> curList = hashMap.get(hashValue);
                curList.add(s);
            } else {
                List<String> newList = new ArrayList<>();
                newList.add(s);

                hashMap.put(hashValue, newList);
            }

            //下面这段看起来会比较简洁，但是新加入元素是，HashMap会进行两次操作，会增加时间
//            if (!hashMap.containsKey(hashValue)) {
//                hashMap.put(hashValue, new ArrayList<>());
//            }
//            hashMap.get(hashValue).add(s);
        }
        return new ArrayList<>(hashMap.values());
    }
```
# 6. 94. 二叉树的中序遍历（亚马逊、字节跳动、微软在半年内面试中考过）
## 6.1 使用递归的方式
```java
    static List<Integer> res = new ArrayList<>();
    public List<Integer> inorderTraversal(TreeNode root) {
        res.clear();
        inTrav(root);
        return res;
    }

    public void inTrav(TreeNode root) {
        if (root == null) {
            return;
        }
        inTrav(root.left);
        res.add(root.val);
        inTrav(root.right);
    }
```

# 7. 144. 二叉树的前序遍历（字节跳动、谷歌、腾讯在半年内面试中考过）
## 7.1 递归实现  List放在函数外面会提高性能
```java
    static List<Integer> res = new ArrayList<>();
    public static List<Integer> inorderTraversal(TreeNode root) {
        res.clear();
        inTrav(root);
        return res;
    }

    public static void inTrav(TreeNode root) {
        if (root == null) {
            return;
        }
        inTrav(root.left);
        res.add(root.val);
        inTrav(root.right);
    }
```
# 8. 429. N叉树的层序遍历（亚马逊在半年内面试中考过）
## 8.1 逐层遍历 2mn
    List<List<Integer>> ret429 = new ArrayList<>();
    public List<List<Integer>> levelOrder2(Node root) {
        if (root == null) {
            return ret429;
        }
        //处理第一层
        List<Integer> chList = new ArrayList<>();
        chList.add(root.val);
        ret429.add(chList);

        //循环遍历下一层
        List<Node> nodeList = root.children;
        if (nodeList == null || nodeList.size() == 0) {
            return ret429;
        }
        while (nodeList.size() != 0) {
            nodeList = layerTrav(nodeList);
        }
        return ret429;
    }

    public List<Node> layerTrav(List<Node> list) {
        List<Integer> chList = new ArrayList<>();
        List<Node> nodeList = new ArrayList<>();
        for (Node node : list) {
            chList.add(node.val);
            if (node.children != null && node.children.size() != 0) {
                nodeList.addAll(node.children);
            }
        }
        ret429.add(chList);
        return nodeList;
    }
## 8.2 放到一个函数里，会慢 3mn
    public List<List<Integer>> levelOrder1(Node root) {
        List<List<Integer>> ret429 = new ArrayList<>();
        if (root == null) {
            return ret429;
        }
        //处理第一层
        List<Integer> rootList = new ArrayList<>();
        rootList.add(root.val);
        ret429.add(rootList);

        //循环遍历下一层
        List<Node> nodeList = root.children;
        if (nodeList == null || nodeList.size() == 0) {
            return ret429;
        }
        while (nodeList.size() != 0) {
            List<Integer> chList = new ArrayList<>();
            List<Node> tempList = new ArrayList<>();
            for (Node node : nodeList) {
                chList.add(node.val);
                if (node.children != null && node.children.size() != 0) {
                    tempList.addAll(node.children);
                }
            }
            ret429.add(chList);
            nodeList.clear();
            nodeList = tempList;
        }
        return ret429;
    }
## 8.3 简化的广度优先搜索
    public List<List<Integer>> levelOrder(Node root) {
        List<List<Integer>> result = new ArrayList<>();
        if(root == null) return result;
        List<Node> layer = Arrays.asList(root);
        while (!layer.isEmpty()) {
            List<Integer> values = new ArrayList<>();
            List<Node> tempList = new ArrayList<>();
            for (Node node : layer) {
                values.add(node.val);
                if (node.children != null && !node.children.isEmpty()) {
                    tempList.addAll(node.children);
                }
            }
            result.add(values);
            layer = tempList;
        }
        return result;
    }
## 8.4 通过队列的方式遍历整个树 3mn
    public List<List<Integer>> levelOrder0(Node root) {
        List<List<Integer>> result = new ArrayList<>();
        if(root == null) return result;
        Queue<Node> layer = new LinkedList<>();
        layer.add(root);
        while (!layer.isEmpty()) {
            List<Integer> values = new ArrayList<>();
            int size = layer.size();
            for (int i = 0; i < size; i++) {
                Node node = layer.poll();
                values.add(node.val);
                if (node.children != null && !node.children.isEmpty()) {
                    layer.addAll(node.children);
                }
            }
            result.add(values);
        }
        return result;
    }
# 9. 丑数（字节跳动在半年内面试中考过）
## 9.1 暴力
```java
    public int nthUglyNumber2(int n) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(1, 1);
        map.put(2, 2);
        map.put(3, 3);
        map.put(5, 5);

        int counter = 0;
        int num = 0;
        while (counter != n) {
            num++;
            for (Integer exist : map.keySet()) {
                if (map.containsKey(num / exist) && num % exist == 0) {
                    map.put(num, counter);
                    counter++;
                    break;
                }
            }
        }
        return num;
    }
```
## 9.2 只考虑235被整除的情况
```java
    public int nthUglyNumber000(int n) {
        if (n <= 5) {
            return map.get(n);
        }
        int counter = 0;
        int num = 0;
        for (Integer start : map.keySet()) {
            if (start > num) {
                num = start;
                counter = map.get(start);
            }
        }
        while (counter != n) {
            num++;
            if (map.containsKey(num / 2) && num % 2 == 0) {
                counter++;
                map.put(num, counter);
                continue;
            }
            if (map.containsKey(num / 3) && num % 3 == 0) {
                counter++;
                map.put(num, counter);
                continue;
            }
            if (map.containsKey(num / 5) && num % 5 == 0) {
                counter++;
                map.put(num, counter);
                continue;
            }
        }
        return num;
    }
    static Map<Integer, Integer> map = new HashMap<>();
    static {
        map.put(1, 1);
        map.put(2, 2);
        map.put(3, 3);
        map.put(4, 4);
        map.put(5, 5);
    }
```
## 9.3 动态规划
```java
    public int nthUglyNumber(int n) {
        int a = 0, b = 0, c = 0;
        int[] dp = new int[n];
        dp[0] = 1;
        for (int i = 1; i < n; i++) {
            int n2 = dp[a] * 2, n3 = dp[b] * 3, n5 = dp[c] * 5;
            dp[i] = Math.min(n2, Math.min(n3, n5));
            if(dp[i] == n2) a++;
            if(dp[i] == n3) b++;
            if(dp[i] == n5) c++;
        }
        return dp[n - 1];
    }
```
# 10. 347. 前 K 个高频元素（亚马逊在半年内面试中常考）
## 10.1 使用小顶堆排序
```java
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        int[] ret = new int[k];
        int size = nums.length;
        for (int i = 0; i < size; i++) {
            map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
        }
        List<Integer> list = new ArrayList<>();
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return map.get(o1) - map.get(o2);
            }
        });
        for (Integer key : map.keySet()) {
            if (queue.size() < k) {
                queue.add(key);
            } else if (map.get(key) > map.get(queue.peek())) {
                queue.remove();
                queue.add(key);
            }
        }
        for (int i = 0; i < k; i++) {
            ret[i] = queue.remove();
        }
        return ret;
```