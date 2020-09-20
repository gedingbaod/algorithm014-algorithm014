# 1.最小路径和（亚马逊、高盛集团、谷歌在半年内面试中考过）
```java
    private int M;
    private int N;
    private int[][] memo;
    //dfs + 记忆
    public int minPathSum(int[][] grid) {
        M = grid.length;
        N = grid[0].length;
        memo = new int[M][N];
        for (int i = 0; i < M; i++) {
            Arrays.fill(memo[i], -1);
        }
        return dfs(grid, 0, 0);
    }

    private int dfs(int[][] grid, int r, int c) {
        //若越界，则认为不可达，距离为无穷大
        if (r < 0 || r >= M || c < 0 || c >= N) return Integer.MAX_VALUE;
        if (memo[r][c] > -1) return memo[r][c];
        //若到达终点，终点的贡献值是其本身
        if (r == M - 1 && c== N - 1) return grid[M - 1][N - 1];
        //右边的点到终点最短的路径
        int right = dfs(grid, r, c + 1);
        //下面的点到终点的最短路径
        int down = dfs(grid, r + 1, c);
        //取两者的较小值，计算出当前点的最小路径值
        int ans = Math.min(right, down) + grid[r][c];
        memo[r][c] = ans;
        return ans;
    }
```
# 2.解码方法（亚马逊、Facebook、字节跳动在半年内面试中考过）
```java
    public int numDecodings(String s) {
        int len = s.length();
        if (len == 0) {
            return 0;
        }

        // dp[i] 以 s[i] 结尾的前缀子串有多少种解码方法
        // dp[i] = dp[i - 1] * 1 if s[i] != '0'
        // dp[i] += dp[i - 2] * 1 if  10 <= int(s[i - 1..i]) <= 26
        int[] dp = new int[len];

        char[] charArray = s.toCharArray();
        if (charArray[0] == '0') {
            return 0;
        }
        dp[0] = 1;

        for (int i = 1; i < len; i++) {
            if (charArray[i] != '0') {
                dp[i] = dp[i - 1];
            }

            int num = 10 * (charArray[i - 1] - '0') + (charArray[i] - '0');
            if (num >= 10 && num <= 26) {
                if (i == 1) {
                    dp[i]++;
                } else {
                    dp[i] += dp[i - 2];
                }
            }
        }
        return dp[len - 1];
    }
```
# 3.最大正方形（华为、谷歌、字节跳动在半年内面试中考过）
```java
public int maximalSquare(char[][] matrix) {
        int maxSide = 0;
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return maxSide;
        }
        int rows = matrix.length, columns = matrix[0].length;
        int[][] dp = new int[rows][columns];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = Math.max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
```
# 4.任务调度器（Facebook 在半年内面试中常考）
```java
    public int leastInterval(char[] tasks, int n) {
        int[] counts = new int[26];
        for (char c : tasks) {
            counts[c - 'A'] += 1;
        }
        int max = 0;
        for (int count : counts) {
            max = Math.max(max, count);
        }
        int maxCount = 0;
        for (int count : counts) {
            if (count == max) {
                maxCount++;
            }
        }
        return Math.max((n + 1) * (max - 1) + maxCount, tasks.length);
    }
```
# 5.回文子串（Facebook、苹果、字节跳动在半年内面试中考过）
```java
    public int countSubstrings(String s) {
        int n = s.length(), ans = 0;
        for (int i = 0; i < 2 * n - 1; ++i) {
            int l = i / 2, r = i / 2 + i % 2;
            while (l >= 0 && r < n && s.charAt(l) == s.charAt(r)) {
                --l;
                ++r;
                ++ans;
            }
        }
        return ans;
    }
```