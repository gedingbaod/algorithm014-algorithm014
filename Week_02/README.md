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
## 4.2 把数组放到外面
    static List<Integer> ret = new ArrayList<>();
    public static List<Integer> preorder(Node root) {
        inOrder(root);
        return ret;
    }

    public static void inOrder(Node root) {
        if (root == null) {
            return;
        }
        ret.add(root.val);
        if (root.children != null) {
            for (int i = 0; i < root.children.size(); i++) {
                inOrder(root.children.get(i));
            }
        }
    }
# 5. 144. 二叉树的前序遍历（字节跳动、谷歌、腾讯在半年内面试中考过）