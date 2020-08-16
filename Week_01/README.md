# 0.数据结构基础
## 0.1 冒泡排序
    public static void popSort(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] > nums[j]) {
                    int tmp = nums[i];
                    nums[i] = nums[j];
                    nums[j] = tmp;
                }
            }
        }
    }
## 0.2 选择排序
    public static void selectSort(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            int val_min = nums[i];
            int pos_min = i;
            for (int j = i + 1; j < nums.length; j++) {
                if (val_min > nums[j]) {
                    val_min = nums[j];
                    pos_min = j;
                }
            }
            nums[pos_min] = nums[i];
            nums[i] = val_min;
        }
    }
# 1.详见# 10.设计循环双端队列（Facebook 在 1 年内面试中考过）
# 2.分析 Queue 和 Priority Queue 的源码
## 2.1 java.util.Queue接口的方法：

  add：    增加一个元素，成功返回true，容量不足抛出异常
  offer：  增加一个元素，成功返回true，容量不足返回false
  remove： 从头部删除一个元素并返回，队列为空抛出异常
  poll：   从头部删除一个元素并返回，队列为空返回null
  element：返回头部元素，队列为空抛出异常
  peek：   返回头部元素，队列为空返回null

  这些内容只是一个约定，最终还要根据实现的具体代码确定方法的行为表现。

## 2.2 java.util.PriorityQueue类
  继承关系：PriorityQueue <-- AbstractQueue <== Queue
  AbstractQueue 严格实现了Queue对功能的约定add，remove，element，peek。
  PriorityQueue 实现了全部功能，但是并没有按照接口约定，而是都抛出异常。
  PriorityQueue最大的特点是，他包含了一个Comparator对象，这个对象可以对元素进行比较。
  这个数据的存储使用了数组的方式，但实际上是存储了一个二叉树。
  当插入时，通过折半的方式查找元素应该插入的位置，同时移动其他元素。
  当删除时，直接删除首元素，同时调整二叉树。


# 3.旋转数组（微软、亚马逊、PayPal 在半年内面试中考过）
## 3.1 暴力方法, O(n * k)
    public static void rotate2(int[] nums, int k) {
        //执行k遍整体移动
        for (int i = 0; i < k; i++) {
            int tmp = nums[nums.length - 1];
            // 从后往前，所有数据向右移动一格
            for (int j = nums.length - 1; j > 0; j--) {
                nums[j] = nums[j - 1];
            }
            //把0位补上
            nums[0] = tmp;
        }
    }
## 3.2 整数取余法 O(1)
    public static void rotate0(int[] nums, int k) {
        //数组长度为0退出，避免溢出
        if(nums.length == 0) return;
        //如果k大于数组长度是，可以减少一遍重复置换
        k = k % nums.length;
        int count = 0;
        for (int pos_start = 0; count < nums.length; pos_start++) {
            int pos_cur = pos_start;
            int val_cur = nums[pos_cur];
            do {
                //根据当前节点配置下一节点
                int pos_next = (pos_cur + k) % nums.length;
                int val_next = nums[pos_next];
                //赋值当前节点
                nums[pos_next] = val_cur;
                //把下一节点配置为当前节点
                pos_cur = pos_next;
                val_cur = val_next;
                count++;
            } while (pos_start != pos_cur);
        }
    }
## 3.3 三次反转法, O(2n)
    public static void rotate(int[] nums, int k) {
        //数组长度为0退出，避免溢出
        if(nums.length == 0) return;
        //如果k大于数组长度是，可以减少一遍重复置换
        k = k % nums.length;
        //反转全部数组
        for (int i = 0; i < nums.length / 2; i++) {
            int tmp = nums[i];
            nums[i] = nums[nums.length - i - 1];
            nums[nums.length - i - 1] = tmp;
        }
        //反转前K个
        for (int j = 0; j < k / 2; j++) {
            int tmp = nums[j];
            nums[j] = nums[k - j - 1];
            nums[k - j - 1] = tmp;
        }
        //反转剩余部分
        int rest = (nums.length - k) / 2;
        for (int h = 0; h < rest; h++) {
            int tmp = nums[h + k];
            nums[h + k] = nums[nums.length - h - 1];
            nums[nums.length - h - 1] = tmp;
        }
    }

# 4.删除排序数组中的重复项（Facebook、字节跳动、微软在半年内面试中考过）

    public int removeDuplicates(int[] nums) {
        // 零的情况
        if (nums.length == 0) {
            return 0;
        }
        //用j做移动后的指针
        int j = 0;
        //用i做索引指针
        for (int i = 1; i < nums.length; i++) {
            //如果不同，则拷贝
            if (nums[j] != nums[i]) {
                nums[++j] = nums[i];
            }
        }
        return j+1;
    }

# 5.合并两个有序链表（亚马逊、字节跳动在半年内面试常考）

## 5.1 暴力求解(没读懂题目，理解成要创建一个新的链表)
    public static ListNode mergeTwoLists2(ListNode l1, ListNode l2) {
        //定义头指针
        ListNode point = new ListNode(0, null);
        ListNode head = point;
        int val_cur = 0;
        do {
            if (l1 != null) {
                if (l2 != null) {
                    if (l1.val < l2.val) {
                        val_cur = l1.val;
                        l1 = l1.next;
                    } else {
                        val_cur = l2.val;
                        l2 = l2.next;
                    }
                } else {
                    val_cur = l1.val;
                    l1 = l1.next;
                }
            } else {
                if (l2 != null) {
                    val_cur = l2.val;
                    l2 = l2.next;
                } else {
                    return null;
                }
            }
            //新增节点
            ListNode newNode = new ListNode(val_cur, null);
            point.next = newNode;
            point = point.next;
        } while (l1 != null || l2 != null);
        return head.next;
    }

## 5.2 递归方式
    public static ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else {
            if (l1.val < l2.val) {
                l1.next = mergeTwoLists(l1.next, l2);
                return l1;
            } else {
                l2.next = mergeTwoLists(l1, l2.next);
                return l2;
            }
        }
    }
# 6.合并两个有序数组（Facebook 在半年内面试常考）
## 6.1 从左向右排序O( n(n+1)/2 )
    public static void merge2(int[] nums1, int m, int[] nums2, int n) {
        if (n == 0) {
            return;
        }
        if (m == 0) {
            for (int i = 0; i < n; i++) {
                nums1[i] = nums2[i];
            }
            return;
        }
        int pos_1 = 0;
        int pos_2 = 0;
        do {
            //如果nums1的小
            if (nums1[pos_1] < nums2[pos_2] && pos_1 < (m + pos_2) ){
                pos_1++;
            } else { //如果nums2的小
                //nums1整体右移1位
                for (int i = m + pos_2; i > pos_1 ; i--) {
                    nums1[i] = nums1[i -1];
                }
                //插入nums2的值到nums1
                nums1[pos_1] = nums2[pos_2];
                pos_1++;
                pos_2++;
            }
        } while (pos_2 != n);
    }
## 6.2 从右向左排序 O(n)
    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        m--;
        n--;
        int len = m + n + 1;
        while (m >= 0 && n >= 0) {
            nums1[len--] = nums1[m] > nums2[n] ? nums1[m--] : nums2[n--];
        }
        System.arraycopy(nums2, 0, nums1, 0, n + 1);
    }
# 7. 两数之和（亚马逊、字节跳动、谷歌、Facebook、苹果、微软在半年内面试中高频常考）
## 7.1 选定一个值，找出另一个值
    public static int[] twoSum1(int[] nums, int target) {
        int find = 0;
        for (int i = 0; i < nums.length - 1; i++) {
            find = target - nums[i];
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == find) {
                    return new int[]{i, j};
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
## 7.2 哈希表
    public static int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target-nums[i]), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
# 8.移动零（Facebook、亚马逊、苹果在半年内面试中考过）
## 8.1 左右夹逼，数组左移
    public static void moveZeroes2(int[] nums) {
        int count = nums.length;
        int i = 0;
        while (count > i) {
            if (nums[i] == 0) {
                //左移一位
                for (int j = i; j < count - 1; j++) {
                    nums[j] = nums[j + 1];
                }
                //最后一位赋值为0
                nums[count - 1] = 0;
                count--;
            } else {
                i++;
            }
        }
    }
## 8.2 快慢指针
    public static void moveZeroes1(int[] nums) {
        int pos_find_start = 0;
        int pos_set = 0;
        while (nums.length > pos_find_start) {
            if (nums[pos_find_start] == 0) {
                for (int j = pos_find_start; j < nums.length; j++) {
                    if (nums[j] != 0) {
                        nums[pos_set] = nums[j];
                        nums[j] = 0;
                        pos_find_start = j;
                        break;
                    }
                }
            } else {
                if (pos_find_start > pos_set) {
                    nums[pos_set] = nums[pos_find_start];
                    nums[pos_find_start] = 0;
                }
            }
            pos_set++;
            pos_find_start++;
        }
    }
## 8.3 快慢指针优化
    public static void moveZeroes(int[] nums) {
        if (nums.length < 2) {
            return;
        }
        int pos_find_start = 0;
        int pos_set = 0;
        while (nums.length > pos_find_start) {
            if (nums[pos_find_start] != 0) {
                nums[pos_set++] = nums[pos_find_start];
            }
            pos_find_start++;
        }
        while (pos_set < nums.length) {
            nums[pos_set] = 0;
            pos_set++;
        }
    }
## 8.4 快慢指针，兼容数组小于2的情况
    public void moveZeroes(int[] nums) {
        int pos_des = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                if (i > pos_des) {//兼容数组前面都不为0的情况
                    nums[pos_des] = nums[i];
                    nums[i] = 0;
                }
                pos_des++;
            }
        }
    }
# 9. 加一（谷歌、字节跳动、Facebook 在半年内面试中考过）
## 9.1 后到前遍历，判断是否为9
    public static int[] plusOne1(int[] digits) {
        for (int i = digits.length-1; i >= 0 ; i--) {
            int num = digits[i];
            if (num != 9) {
                digits[i] = ++num;
                return digits;
            } else {
                digits[i] = 0;
            }
        }
        int[] ret = new int[digits.length + 1];
        Arrays.fill(ret, 0);
        ret[0] = 1;
        return ret;
    }
## 9.2 后到前遍历，加1后，判断是否进位
    public static int[] plusOne(int[] digits) {
        for (int i = digits.length-1; i >= 0 ; i--) {
            digits[i]++;
            digits[i] = digits[i] % 10;
            if (digits[i] != 0) {
                return digits;
            }
        }
        int[] ret = new int[digits.length + 1];
        ret[0] = 1;
        return ret;
    }
## 9.3 改为用9判断，会更直观
    public static int[] plusOne(int[] digits) {
        for (int i = digits.length-1; i >= 0 ; i--) {
            if (digits[i] != 9) {
                digits[i]++;
                return digits;
            }
        }
        int[] ret = new int[digits.length + 1];
        ret[0] = 1;
        return ret;
    }
# 10.设计循环双端队列（Facebook 在 1 年内面试中考过）
## 10.1 使用链表实现
```java
public class MyCircularDequeLink {
    public static class Node {
        int val;
        Node prev;
        Node next;

        Node() {
            this(0);
        }
        Node(int val) {
            this(val, null, null);
        }
        Node(int val, Node next, Node prev) {
            this.val = val;
            this.next = next;
            this.prev = prev;
        }
    }
    int max;
    int count;
    Node head;
    Node tail;

    /** Initialize your data structure here. Set the size of the deque to be k. */
    public MyCircularDequeLink(int k) {
        max = k;
        head = null;
        tail = null;
        count = 0;
    }

    /** Adds an item at the front of Deque. Return true if the operation is successful. */
    public boolean insertFront(int value) {
        if (isFull()) {
            return false;
        }
        Node node = new Node(value);
        if (head == null) {
            head = node;
            tail = node;
        } else {
            node.next = head;
            head.prev = node;
            head = node;
        }
        count++;
        return true;
    }

    /** Adds an item at the rear of Deque. Return true if the operation is successful. */
    public boolean insertLast(int value) {
        if (isFull()) {
            return false;
        }
        Node node = new Node(value);
        if (tail == null) {
            head = node;
            tail = node;
        } else {
            node.prev = tail;
            tail.next = node;
            tail = node;
        }
        count++;
        return true;
    }

    /** Deletes an item from the front of Deque. Return true if the operation is successful. */
    public boolean deleteFront() {
        if (isEmpty()) {
            return false;
        }
        head = head.next;
        if (head != null) {
            head.prev = null;
        } else {
            tail = null;
        }
        count--;
        return true;
    }

    /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
    public boolean deleteLast() {
        if (isEmpty()) {
            return false;
        }
        tail = tail.prev;
        if (tail != null) {
            tail.next = null;
        } else {
            head = null;
        }
        count--;
        return true;
    }

    /** Get the front item from the deque. */
    public int getFront() {
        if (isEmpty()) {
            return -1;
        }
        return head.val;
    }

    /** Get the last item from the deque. */
    public int getRear() {
        if (isEmpty()) {
            return -1;
        }
        return tail.val;
    }

    /** Checks whether the circular deque is empty or not. */
    public boolean isEmpty() {
        return count == 0;
    }

    /** Checks whether the circular deque is full or not. */
    public boolean isFull() {
        return count >= max;
    }
}
```
## 10.2 用数组实现，取模运算
```java
class MyCircularDequeArray1 {
    int[] array;
    int head; //始终指向队列的首位置
    int tail; //始终指向队列的下一个位置
    int max;
    /** Initialize your data structure here. Set the size of the deque to be k. */
    public MyCircularDequeArray1(int k) {
        if (k < 0) {
            throw new IllegalArgumentException("Deque size is less than 0!");
        }
        max = k + 1;
        array = new int[max];
        head = 0;
        tail = 0;
    }

    /** Adds an item at the front of Deque. Return true if the operation is successful. */
    public boolean insertFront(int value) {
        if (isFull()) {
            return false;
        }
        head = (head + max - 1) % max;
        array[head] = value;
        return true;
    }

    /** Adds an item at the rear of Deque. Return true if the operation is successful. */
    public boolean insertLast(int value) {
        if (isFull()) {
            return false;
        }
        array[tail] = value;
        tail = (tail + 1) % max;
        return true;
    }

    /** Deletes an item from the front of Deque. Return true if the operation is successful. */
    public boolean deleteFront() {
        if (isEmpty()) {
            return false;
        }
        head = (head + 1) % max;
        return true;
    }

    /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
    public boolean deleteLast() {
        if (isEmpty()) {
            return false;
        }
        tail = (tail + max - 1) % max;
        return true;
    }

    /** Get the front item from the deque. */
    public int getFront() {
        if (isEmpty()) {
            return -1;
        }
        return array[head];
    }

    /** Get the last item from the deque. */
    public int getRear() {
        if (isEmpty()) {
            return -1;
        }
        return array[(tail + max - 1) % max];
    }

    /** Checks whether the circular deque is empty or not. */
    public boolean isEmpty() {
        return head == tail;
    }

    /** Checks whether the circular deque is full or not. */
    public boolean isFull() {
        return (tail + 1) % max == head;
    }
}
```
## 10.3 用数组实现，长度通过计数器计算
```java
class MyCircularDeque {
    int[] array;
    int head; //始终指向队列的首位置
    int tail; //始终指向队列的下一个位置
    int count;
    /** Initialize your data structure here. Set the size of the deque to be k. */
    public MyCircularDeque(int k) {
        if (k < 0) {
            throw new IllegalArgumentException("Deque size is less than 0!");
        }
        array = new int[k];
        head = 0;
        tail = 0;
        count = 0;
    }

    /** Adds an item at the front of Deque. Return true if the operation is successful. */
    public boolean insertFront(int value) {
        if (isFull()) {
            return false;
        }
        if (head == 0) {
            head = array.length - 1;
            array[head] = value;

        } else {
            array[--head] = value;
        }
        count++;
        return true;
    }

    /** Adds an item at the rear of Deque. Return true if the operation is successful. */
    public boolean insertLast(int value) {
        if (isFull()) {
            return false;
        }
        if (tail == array.length - 1) {
            array[tail] = value;
            tail = 0;
        } else {
            array[tail++] = value;
        }
        count++;
        return true;
    }

    /** Deletes an item from the front of Deque. Return true if the operation is successful. */
    public boolean deleteFront() {
        if (isEmpty()) {
            return false;
        }
        head = (head == array.length - 1) ? 0 : head + 1;
        count--;
        return true;
    }

    /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
    public boolean deleteLast() {
        if (isEmpty()) {
            return false;
        }
        if (tail == 0) {
            tail = array.length - 1;
        } else {
            tail--;
        }
        count--;
        return true;
    }

    /** Get the front item from the deque. */
    public int getFront() {
        if (isEmpty()) {
            return -1;
        }
        return array[head];
    }

    /** Get the last item from the deque. */
    public int getRear() {
        if (isEmpty()) {
            return -1;
        }
        if (tail == 0) {
            return array[array.length - 1];
        } else {
            return array[tail - 1];
        }
    }

    /** Checks whether the circular deque is empty or not. */
    public boolean isEmpty() {
        return count == 0;
    }

    /** Checks whether the circular deque is full or not. */
    public boolean isFull() {
        return count == array.length;
    }
}
``` 