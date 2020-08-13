
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
