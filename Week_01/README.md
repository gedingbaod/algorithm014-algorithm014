
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
# 6.1 从左向右排序O( n(n+1)/2 )
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
# 6.2 从右向左排序 O(n)
    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        m--;
        n--;
        int len = m + n + 1;
        while (m >= 0 && n >= 0) {
            nums1[len--] = nums1[m] > nums2[n] ? nums1[m--] : nums2[n--];
        }
        System.arraycopy(nums2, 0, nums1, 0, n + 1);
    }