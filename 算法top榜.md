[215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

```java
public int findKthLargest(int[] nums, int k) {
    int target=nums.length-k;
    int left=0,right= nums.length-1;
    int now=quickselect(nums,left,right);
    while(now!=target)
    {
        if(now<target) left=now+1;
        else right=now-1;
        now=quickselect(nums,left,right);
    }
    return nums[target];
}

int quickselect(int []nums,int left,int right)
{
    int randomIndex = left + (int) (Math.random() * (right - left + 1));
    swap(nums, left, randomIndex); // 将随机基准值移到left位置，不改变原有逻辑
    int l=left+1,r=right;
    while(l<=r)
    {
        while(l<=r&&nums[l]<nums[left]) ++l;//这里比较不要有等号
        while(l<=r&&nums[r]>nums[left]) --r;
        if (l <= r) {
            swap(nums, l, r);
            l++;
            r--;
        }
    }
    swap(nums,left,r);
    return r;
}

void swap(int nums[],int i,int j)
{
    int t=nums[i];
    nums[i]=nums[j];
    nums[j]=t;
}
```

[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

注意判断的时候有一个等号

```java
public int search(int[] nums, int target) {
        return find(nums,target,0,nums.length-1);
    }
    int find(int[] nums,int target,int left,int right)
    {
        if(left>right) return -1;
        int mid=(left+right)/2;
        if(nums[mid]==target) return mid;
        if(nums[left]<=nums[mid])//这里和底下有一个等号
        {
            if(target<nums[mid]&&target>=nums[left])
                return find(nums,target,left,mid-1);
            else return find(nums,target,mid+1,right);
        }else{
            if(target>nums[mid]&&target<=nums[right])
                return find(nums,target,mid+1,right);
            else return find(nums,target,left,mid-1);
        }
    }
```

[103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

正常的层次遍历，但是在path中选择是从最后添加还是第一个添加

```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        boolean flag=true;
        List<List<Integer>> res=new ArrayList<>();
        if(root==null) return res;
        LinkedList<TreeNode> queue=new LinkedList<>(); 
        queue.add(root);
        while(!queue.isEmpty())
        {
            int size=queue.size();
            LinkedList<Integer> list=new LinkedList<>();
            for(int i=0;i<size;i++)
            {
                TreeNode node=queue.removeFirst();
                if(flag) list.addLast(node.val);
                else list.addFirst(node.val);
                if(node.left!=null) queue.add(node.left);
                if(node.right!=null) queue.add(node.right);
            }
            res.add(list);
            flag=!flag;  
        }  
        return res;
    }
```

[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

后序遍历

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root==null||root==p||root==q)
            return root;
        TreeNode le=lowestCommonAncestor(root.left,p,q);
        TreeNode ri=lowestCommonAncestor(root.right,p,q);
        if(le!=null&&ri!=null) return root;
        if(le==null) return ri;
        if(ri==null) return le;
        return null;
    }
```

[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

每向后移动一个位置都要回去找比当前指针小的元素，然后加一比较取最大值

```java
public int lengthOfLIS(int[] nums) {
        int res=1;
        int []dp=new int[nums.length];
        Arrays.fill(dp,1);
        for(int i=1;i<nums.length;i++)
        {
            for(int j=0;j<i;j++)
                if(nums[i]>nums[j])
                    dp[i]=Integer.max(dp[i],dp[j]+1);
            res=Integer.max(res,dp[i]);
        }
        return res;
    }
```

[143. 重排链表](https://leetcode.cn/problems/reorder-list/)

找到链表中点，分成两个链表，前一个链表next置空，然后交替进行穿插，最后别忘了第二个链表可能多一个元素

```java
public void reorderList(ListNode head) {
        if (head == null || head.next == null) {
            return;
        }
        ListNode dummy=new ListNode();
        dummy.next=head;
        ListNode slow=dummy,fast=dummy;
        while(fast.next!=null&&fast.next.next!=null)
        {
            slow=slow.next;
            fast=fast.next.next;
        }
        ListNode cur=slow.next;
        while(cur.next!=null)
        {
            ListNode now=cur.next;
            cur.next=now.next;
            now.next=slow.next;
            slow.next=now;
        }
        ListNode l1=dummy.next;
        ListNode l2=slow.next;
        slow.next=null;
        ListNode now=dummy;
        while(l1!=null&&l2!=null)
        {
            now.next=l1;
            l1=l1.next;
            now=now.next;
            now.next=l2;
            l2=l2.next;
            now=now.next;
        }
        if(l2!=null) now.next=l2;
    }
```

[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

挺麻烦的，如果栈中元素大于等于2，在循环里面bottom是pop的而left是peek的，如果是left和bottom相同那就会加上零相当于一个pop。

```java
public int trap(int[] height)
    {
        Deque<Integer> dq=new LinkedList<>();
        dq.push(0);
        int sum=0;
        for(int i=1;i<height.length;i++)
        {
            while(!dq.isEmpty()&&height[dq.peek()]<height[i])
            {
                if(dq.size()>1)
                {
                    int bottom=dq.pop();
                    int left=dq.peek();
                    sum+=(Math.min(height[left],height[i])-height[bottom])*(i-left-1);
                }else dq.pop();
            }
            dq.push(i);
        }
        return sum;
    }
```

动态规划，左边遍历当前节点左边最大的高度和逆序遍历右边最大的高度，这个点贡献的雨水就是两个高度最小值减去当前高度

```java
class Solution {
    public int trap(int[] height) {
        int n=height.length;
        int lefthigh[]=new int[n],righthigh[]=new int[n];
        lefthigh[0]=height[0];righthigh[n-1]=height[n-1];
        for(int i=1;i<n;++i)
            lefthigh[i]=Math.max(height[i],lefthigh[i-1]);
        for(int i=n-2;i>=1;--i)
            righthigh[i]=Math.max(height[i],righthigh[i+1]);
        int res=0;
        for(int i=1;i<n-1;++i)
            res+=Math.min(lefthigh[i],righthigh[i])-height[i];
        return res;
    }
}
```

[72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

初始化注意一下，空串和非空串的距离等于非空串的长度

```java
public int minDistance(String word1, String word2) {
        int dp[][]=new int[word1.length()+1][word2.length()+1];
        for(int i=1;i<=word1.length();i++)
            dp[i][0]=i;
        for(int i=1;i<=word2.length();i++)
            dp[0][i]=i;
        for(int i=1;i<=word1.length();i++)
            for(int j=1;j<=word2.length();j++)
                if(word1.charAt(i-1)==word2.charAt(j-1))
                    dp[i][j]=dp[i-1][j-1];
                else dp[i][j]=Math.min(Math.min(dp[i-1][j-1],dp[i-1][j]),dp[i][j-1])+1;
        return dp[word1.length()][word2.length()];
    }
```

[124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

当前节点是顶节点不会在往上找了需要更新max ，如果不是顶节点那就加上自己的左孩子或者右孩子。

```java
int max=Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        travel(root);
        return max;
    }

    int travel(TreeNode root)
    {
        if(root==null) return 0;
        int left=travel(root.left);
        int right=travel(root.right);
        max=Math.max(left+ root.val+right,max);
        return Math.max(Math.max(left,right)+root.val,0);
    }
```

[93. 复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)

挺麻烦的，记录点的数目作为终止条件，然后有前导零的时候单独递归，要记得回溯。

```java
ArrayList<String> res = new ArrayList<>();
    StringBuilder sb = new StringBuilder();
    int dot = 0;
    
    public List<String> restoreIpAddresses(String s) {
        if (s.length() < 4 || s.length() > 12) return res; // 提前检查长度
        travel(0, s);
        return res;
    }
    
    void travel(int nowidx, String s) {
        if (dot == 3) {
            if (nowidx >= s.length()) return; // 检查 nowidx 是否越界
            String temp = s.substring(nowidx);
            if (temp.length() > 3) return; // 剩余部分不能超过3位
            if (temp.length() > 1 && temp.charAt(0) == '0') return; // 不能有前导零
            int num = Integer.parseInt(temp);
            if (num >= 0 && num <= 255) {
                sb.append(temp);
                res.add(sb.toString());
                sb.setLength(sb.length() - temp.length()); // 回溯
            }
            return;
        }
        
        if (nowidx >= s.length()) return; // 防止越界
        
        if (s.charAt(nowidx) == '0') {
            sb.append("0.");
            dot++;
            travel(nowidx + 1, s);
            dot--;
            sb.setLength(sb.length() - 2); // 回溯，移除 "0."
        } else {
            for (int i = 1; i <= 3 && nowidx + i <= s.length(); i++) {
                String temp = s.substring(nowidx, nowidx + i);
                int num = Integer.parseInt(temp);
                if (num >= 0 && num <= 255) {
                    int prevLen = sb.length();
                    sb.append(temp).append(".");
                    dot++;
                    travel(nowidx + i, s);
                    dot--;
                    sb.setLength(prevLen); // 回溯到之前的状态
                }
            }
        }
    }

```

[82. 删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

循环先判定是不是为空，内部分支再循环是否和下一个元素相等，相等则后移到第一个不是这个的元素，否则加入目标链表。

```java
public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy=new ListNode();
        dummy.next=head;
        ListNode cur=head;
        ListNode t=dummy;
        while(cur!=null)
        {
            int nowval=cur.val;
            if(cur.next!=null&&cur.next.val==nowval)
            {
                while(cur!=null&&cur.val==nowval)
                    cur=cur.next;
            }else{
                t.next=cur;
                t=t.next; 
                cur=cur.next;
            }
        }
        t.next=null;
        return dummy.next;
    }

```

[**4. 寻找两个正序数组的中位数**](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

方法一：设置两个栈，先全部放入最大栈中，然后算出长度再把一半都放入栈里面，然后取两个栈顶取平均值或者最大栈栈顶。

方法二，将两个数组合并成一个新的int[m+n]数组，因为有序，双指针合并，然后直接取中间的值或者中间和下一个元素的平均值。

前两个都不好使，要使用二分法

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int n1=nums1.length,n2=nums2.length;
        int k1=(n1+n2+1)/2,k2=(n1+n2+2)/2;//统一计算要么是一样的要么相差1
        return k1==k2?getkth(nums1,nums2,0,n1-1,0,n2-1,k1)
                :(getkth(nums1,nums2,0,n1-1,0,n2-1,k1)+
                getkth(nums1,nums2,0,n1-1,0,n2-1,k2))/2;
    }

    double getkth(int[] nums1, int[] nums2,int l1,int r1,int l2,int r2,int k)
    {
        int len1=r1-l1+1,len2=r2-l2+1;
        if(len1>len2) return getkth(nums2,nums1,l2,r2,l1,r1,k);//保持左边元素更少
        if(len1==0) return nums2[l2+k-1];//边界一如果左边没有元素直接返回右边就行
        if(k==1) return Math.min(nums1[l1],nums2[l2]);//边界二找第一小的元素直接返回两个数组第一个元素的较小值
        int k1=l1+Math.min(len1,k/2)-1,k2=l2+Math.min(len2,k/2)-1;//每次减少k/2或者较小值的元素，快速达到边界条件
        if(nums1[k1]>nums2[k2]) //左边比较大，减去右边的长度
            return getkth(nums1,nums2,l1,r1,k2+1,r2,k-(k2-l2+1));
        else return getkth(nums1,nums2,k1+1,r1,l2,r2,k-(k1-l1+1));//反之减去左边多余长度
    }
```

[148. 排序链表](https://leetcode.cn/problems/sort-list/)

插入排序，太慢了会超时

```java
public ListNode sortList(ListNode head) {
        if(head==null||head.next==null) return head;
        ListNode dummy=new ListNode();
        dummy.next=head;
        ListNode last=head;
        ListNode cur=head.next;
        while(cur!=null)
        {
            int nowval=cur.val;
            ListNode l=dummy;
            while(l.next!=null&&nowval>l.next.val)
                l=l.next;
            if(l==last)
                last=last.next;
            else{
                last.next=cur.next;
                cur.next=l.next;
                l.next=cur;
            }
            cur=last.next;
        }
        return dummy.next;
    }

```

最优解是链表的归并排序，注意合并的时候是后序合并，自底向上合并

```java
public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        // 使用快慢指针找到链表的中间节点
        ListNode slow = head;
        ListNode fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        // 断开链表，分成两部分
        ListNode mid = slow.next;
        slow.next = null;

        // 递归排序左右两部分
        ListNode left = sortList(head);
        ListNode right = sortList(mid);

        // 合并两个有序链表
        return merge(left, right);
    }

    // 合并两个有序链表
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode();
        ListNode tail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        // 处理剩余节点
        if (l1 != null) {
            tail.next = l1;
        } else {
            tail.next = l2;
        }
        return dummy.next;
    }

```

[22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

只有一个要注意的就是路径的生成使用stringbuilder就好

[**165. 比较版本号**](https://leetcode.cn/problems/compare-version-numbers/)

只有一个地方就是 . 需要转义

![1747800386297](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1747800386297.png)



[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

最大栈更新最大值，如果i-k位置是最大值删掉，别忘记循环条件中的maxstack不为空。

```java
public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> maxstack=new LinkedList<>();
        int res[]=new int[nums.length-k+1];
        maxstack.push(nums[0]);
        for(int i=1;i<k;i++)
        {
            while(!maxstack.isEmpty()&&maxstack.getLast()<nums[i])
                maxstack.removeLast();
            maxstack.add(nums[i]);
        }
        res[0]=maxstack.getFirst();
        int idx=1;
        for(int i=k;i< nums.length;i++)
        {
            if(nums[i-k]==maxstack.getFirst())
                maxstack.removeFirst();
            while(!maxstack.isEmpty()&&maxstack.getLast()<nums[i])
                maxstack.removeLast();
            maxstack.add(nums[i]);
            res[idx++]=maxstack.getFirst();
        }
        return res;
    }

```

[8. 字符串转换整数 (atoi)](https://leetcode.cn/problems/string-to-integer-atoi/)

当它是数字的时候的处理相对比较复杂，边界情况

```java
public int myAtoi(String s) {
        int res=0;
        char str[]=s.toCharArray();
        int len=s.length();
        int nowidx=0;
        int sign=1;
        while(nowidx<len&&str[nowidx]==' ')nowidx++;
        if(nowidx<len)
        {
            if(str[nowidx]=='-')
            {
                sign=-1;
                nowidx++;
            }else if(str[nowidx]=='+')
                nowidx++;
        }
         while (nowidx < len && Character.isDigit(str[nowidx])) {
            int digit = str[nowidx] - '0';

            // 检查是否溢出，这边比较复杂
            if (res > Integer.MAX_VALUE / 10 || (res == Integer.MAX_VALUE / 10 && digit > Integer.MAX_VALUE % 10)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }

            res = res * 10 + digit;
            nowidx++;
        }
        return res * sign;
    }

```

[2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

有两个注意点，sum>9 add置为true否则置为false，最后如果add是true还要new ListNode

[**32. 最长有效括号**](https://leetcode.cn/problems/longest-valid-parentheses/)

只有在指针到右括号才有可能dp有变化，所以只有是右括号的时候才好使，主要是注意if中的转移方程，情况一，如果前一个是左括号，那就变成前一个的左括号的前一个有效长度加二，注意临界情况，情况二如果当前指针的右括号前一个还是右括号，那就返回到它的有效长度的前面去看看是不是左括号，i-dp[i-1]-1就是跳到前一个右括号有效长度的前面一个位置。

```java
public int longestValidParentheses(String s) {
        int maxLength = 0;
        int[] dp = new int[s.length()];

        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                maxLength = Math.max(maxLength, dp[i]);
            }
        }

        return maxLength;
    }

```

[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

时间复杂度比较大，就是每次全部重新比较是不是符合规则的滑动窗口的方法

```java
boolean isok(HashMap<Character,Integer> hms,HashMap<Character,Integer> hmt)
    {
        for(char c:hmt.keySet())
        {
            if(hms.getOrDefault(c,0)<hmt.get(c))
                return false;
        }
        return true;
    }
    public String minWindow(String s, String t) {
        if(s.length()<t.length()) {
            return "";
        }
        String res="";
        int minlen=Integer.MAX_VALUE;
        HashMap<Character,Integer> hms=new HashMap<>();
        HashMap<Character,Integer> hmt=new HashMap<>();
        for(char c:t.toCharArray())
            hmt.put(c,hmt.getOrDefault(c,0)+1);
        int left=0,right=0;
        while(right<s.length())
        {
            char r=s.charAt(right);
            hms.put(r,hms.getOrDefault(r,0)+1);
            while(isok(hms,hmt))
            {
                if(right-left+1<minlen)
                {
                    minlen=right-left+1;
                    res=s.substring(left,right+1);
                }
                char temp=s.charAt(left);
                hms.put(temp,hms.get(temp)-1);
                left++;
            }
            right++;
        }
        return res;
    }
```

小优化还是很慢，valid 变量用于记录当前窗口中满足目标字符串字符数量要求的字符种类数。当 valid 等于 need.size() 时，说明当前窗口已经包含了目标字符串的所有字符，此时可以尝试收缩窗口。通过这种方式，避免了每次都遍历 need 哈希表来检查窗口是否满足条件，从而提高了效率。

```java
    /**
     * 找出字符串 s 中包含字符串 t 所有字符的最小覆盖子串
     * @param s 源字符串
     * @param t 目标字符串
     * @return 最小覆盖子串，如果不存在则返回空字符串
     */
    public String minWindow(String s, String t) {
        // 如果 s 的长度小于 t 的长度，不可能存在覆盖子串，直接返回空字符串
        if (s.length() < t.length()) {
            return "";
        }
        // 存储最终结果的最小覆盖子串
        String res = "";
        // 记录最小覆盖子串的长度，初始化为整数最大值
        int minlen = Integer.MAX_VALUE;
        // 用于记录当前滑动窗口中字符及其出现的次数
        HashMap<Character, Integer> hms = new HashMap<>();
        // 用于记录目标字符串 t 中字符及其出现的次数
        HashMap<Character, Integer> hmt = new HashMap<>();

        // 统计目标字符串 t 中每个字符的出现次数
        for (char c : t.toCharArray()) {
            hmt.put(c, hmt.getOrDefault(c, 0) + 1);
        }
        // 记录 t 中不同字符的数量，当窗口中满足该数量的字符种类都达到要求时，说明窗口包含了 t 的所有字符
        int required = hmt.size();
        // 记录当前窗口中已经满足 t 中字符数量要求的字符种类数
        int formed = 0;
        // 滑动窗口的左边界
        int left = 0;
        // 滑动窗口的右边界
        int right = 0;

        while (right < s.length()) {
            // 获取右边界当前指向的字符
            char r = s.charAt(right);
            // 右边界右移，扩大窗口
            right++;

            // 如果当前字符在目标字符串 t 中
            if (hmt.containsKey(r)) {
                // 更新当前窗口中该字符的出现次数
                hms.put(r, hms.getOrDefault(r, 0) + 1);
                // 如果窗口中该字符的数量达到了 t 中该字符的数量，说明该字符的数量要求已满足，formed 加 1
                if (hms.get(r).equals(hmt.get(r))) {
                    formed++;
                }
            }
            // 当窗口中已经满足 t 中所有字符的数量要求时，尝试缩小窗口
            while (left <= right && formed == required) {
                // 如果当前窗口的长度小于之前记录的最小长度，更新最小长度和结果子串的起始位置
                if (right - left < minlen) {
                    minlen = right - left;
                    res = s.substring(left, right);
                }
                // 获取左边界当前指向的字符
                char l = s.charAt(left);
                // 左边界右移，缩小窗口
                left++;

                // 如果左边界移除的字符在目标字符串 t 中
                if (hmt.containsKey(l)) {
                    // 如果移除该字符前，窗口中该字符的数量刚好满足 t 中该字符的数量，移除后不再满足，formed 减 1
                    if (hms.get(l).equals(hmt.get(l))) {
                        formed--;
                    }
                    // 更新当前窗口中该字符的出现次数
                    hms.put(l, hms.get(l) - 1);
                }
            }
        }
        return res;
    }
```

**[41. 缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)**

思路：具体步骤
1、过滤无效元素：将数组中所有小于等于 0 或者大于数组长度 n 的数置为 n + 1，因为这些数不会影响最终结果。
2、标记出现过的正整数：遍历数组，对于每个绝对值在 [1, n] 范围内的数 num，将数组中索引为 num - 1 的元素取相反数，表示 num 已经出现过。
3、查找第一个未出现的正整数：再次遍历数组，找到第一个正数所在的索引 i，那么 i + 1 就是缺失的最小正整数。如果数组中所有元素都是负数，说明 1 到 n 的正整数都出现过，那么缺失的最小正整数就是 n + 1。

```java
public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        // 步骤 1: 过滤无效元素
        for (int i = 0; i < n; i++) {
            if (nums[i] <= 0 || nums[i] > n) {
                nums[i] = n + 1;
            }
        }
        // 步骤 2: 标记出现过的正整数
        for (int i = 0; i < n; i++) {
            int num = Math.abs(nums[i]);
            if (num <= n) {
                nums[num - 1] = -Math.abs(nums[num - 1]);
            }
        }
        // 步骤 3: 查找第一个未出现的正整数
        for (int i = 0; i < n; i++) {
            if (nums[i] > 0) {
                return i + 1;
            }
        }
        return n + 1;
    }
```

[43. 字符串相乘](https://leetcode.cn/problems/multiply-strings/)

写得非常快哈，模拟正常的乘法，从最后一位到第一位×然后再每一位进位

只有一点就是新数组的长度是两个字符串长度相加而不是相乘

```java
public String multiply(String num1, String num2) {
        if(num1.equals("0")||num2.equals("0"))
            return "0";
        int res[]=new int[num1.length()+num2.length()];
        for(int i=num1.length()-1;i>=0;i--)
        {
            int temp1=num1.charAt(i)-'0';
            for(int j=num2.length()-1;j>=0;j--)
            {
                int temp2=num2.charAt(j)-'0';
                int mult=temp1*temp2;
                res[i+j+1]+=mult;
            }
        }
        for(int i=res.length-1;i>=0;i--)
        {
            if(res[i]>9)
            {
                res[i-1]+=res[i]/10;
                res[i]%=10;
            }
        }
        StringBuilder sb=new StringBuilder();
        if(res[0]!=0)
            sb.append(res[0]);
        for(int i=1;i<res.length;i++)
            sb.append(res[i]);
        return sb.toString();
    }
```

[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

思路就是后序生成树，递归生成左右孩子先，思路就是找到当前根节点的序号然后左右递归生成左右孩子，返回当前节点，一开始是边界情况。

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
        return build(preorder,inorder,0,preorder.length-1,0,inorder.length-1);
    }

    TreeNode build(int[] preorder, int[] inorder,int preleft,int preright,int inleft,int inright)
    {
        if(preleft>preright) return null;
        if(preleft==preright) return new TreeNode(preorder[preleft]);
        TreeNode now=new TreeNode(preorder[preleft]);
        int leftlen=0,i;
        for(i=inleft;i<=inright;i++,leftlen++)
            if(inorder[i]==preorder[preleft])
                break;
        now.left=build(preorder,inorder,preleft+1,preleft+leftlen,inleft,i-1);
        now.right=build(preorder,inorder,preleft+1+leftlen,preright,i+1,inright);
        return now;
    }
```

[78. 子集](https://leetcode.cn/problems/subsets/)

错了一个地方就是把travel(nums,i+1);写错了，写成了now+1

```
LinkedList<Integer> path=new LinkedList<>();
    LinkedList<List<Integer>> res=new LinkedList<>();
    public List<List<Integer>> subsets(int[] nums) {
        travel(nums,0);
        return res;
    }
    void travel(int nums[],int now)
    {
        res.add(new LinkedList<>(path));
        for(int i=now;i< nums.length;i++)
        {
            path.add(nums[i]);
            travel(nums,i+1);
            path.removeLast();
        }
    }
```

[155. 最小栈](https://leetcode.cn/problems/min-stack/)

有一个思考点，维护两个栈，就是只有当前元素比minstack元素小的时候才加入栈，因为即使新加入了大于最小栈栈顶的元素，在出栈的时候最小栈栈顶元素也不出栈，因为后进先出

```java
    class MinStack {

        LinkedList<Integer> minstack = new LinkedList<>();
        LinkedList<Integer> stack = new LinkedList<>();

        public MinStack() {
        }

        public void push(int val) {
            stack.push(val);
            // 当 minstack 为空或者 val 小于等于 minstack 栈顶元素时，将 val 压入 minstack
            if (minstack.isEmpty() || val <= minstack.peek()) {
                minstack.push(val);
            }
        }

        public void pop() {
            int t = stack.pop();
            // 当弹出的元素等于 minstack 栈顶元素时，将 minstack 栈顶元素弹出
            if (t == minstack.peek()) {
                minstack.pop();
            }
        }

        public int top() {
            return stack.peek();
        }

        public int getMin() {
            // 直接返回 minstack 栈顶元素
            return minstack.peek();
        }
    }
```

[101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

漏了一个条件， if(r1.val!=r2.val) return false;

```java
public boolean isSymmetric(TreeNode root) {
        return compare(root.left,root.right);
    }

    boolean compare(TreeNode r1,TreeNode r2)
    {
        if(r1==null&&r2==null) return true;
        if(r1==null||r2==null) return false;
        if(r1.val!=r2.val) return false;
        return compare(r1.left,r2.right)&&compare(r1.right,r2.left);
    }
```

[**394. 字符串解码**](https://leetcode.cn/problems/decode-string/)

想复杂了，但是确实要一些逻辑，做两个栈，一个数字栈，一个临时字母栈还有一个

```java
public String decodeString(String s) {
        // 用于存储最终解码后的字符串
        StringBuilder res = new StringBuilder();
        // 用于存储重复次数的栈
        LinkedList<Integer> numStack = new LinkedList<>();
        // 用于存储待拼接的字符串的栈
        LinkedList<StringBuilder> strStack = new LinkedList<>();
        // 临时存储当前解析到的数字
        int num = 0;

        // 遍历输入字符串的每个字符
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                // 如果当前字符是数字，更新 num 的值
                // 处理多位数的情况，例如 "123[" 中的 123
                num = num * 10 + (c - '0');
            } else if (c == '[') {
                // 当遇到左括号时，将当前的重复次数压入 numStack
                numStack.push(num);
                // 将当前的 res 压入 strStack，保存当前已解析的字符串
                strStack.push(res);
                // 重置 res，用于存储括号内的字符串
                res = new StringBuilder();
                // 重置 num，准备解析下一个数字
                num = 0;
            } else if (c == ']') {
                // 当遇到右括号时，取出括号内的字符串
                StringBuilder temp = res;
                // 从 strStack 中取出之前保存的字符串
                res = strStack.pop();
                // 从 numStack 中取出重复次数
                int repeatTimes = numStack.pop();
                // 将括号内的字符串按照重复次数添加到之前保存的字符串后面
                for (int i = 0; i < repeatTimes; i++) {
                    res.append(temp);
                }
            } else {
                // 如果是字母，直接添加到 res 中
                res.append(c);
            }
        }
        // 返回最终解码后的字符串
        return res.toString();
    }
```

[221. 最大正方形](https://leetcode.cn/problems/maximal-square/)

```java
 if(i>0&&j>0)
	dp[i][j]=Math.min(Math.min(dp[i-1][j-1],dp[i][j-1]),dp[i-1][j])+1;
 else dp[i][j]=1;
```

[470. 用 Rand7() 实现 Rand10()](https://leetcode.cn/problems/implement-rand10-using-rand7/)

万能方法，拆成7行七列然后取余

![1748246471641](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1748246471641.png)

```java
class Solution extends SolBase {
    public int rand10() {
        int num;
        do {
            // 生成 1 到 49 之间的随机数
            num = (rand7() - 1) * 7 + rand7();
        } while (num > 40);
        // 映射到 1 到 10 之间的随机数
        return (num - 1) % 10 + 1;
    }
}
```

[128. 最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

```java
public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int ans = 0;
        for(int i : nums) set.add(i);
        for(int i : set){
            if(set.contains(i - 1)) continue;
            int y = i + 1;
            while(set.contains(y)) y++;
            ans = Math.max(ans , y - i);
        }
        return ans;
    }
```

[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

可以在左下角或者右上角开始遍历，只会往右上走或者左下走

```java
public boolean searchMatrix(int[][] matrix, int target) {
        int i=matrix.length-1,j=0;
        while(true)
        {
            if(i<0||j>=matrix[0].length) return false;
            if(matrix[i][j]>target)
                i--;
            else if(matrix[i][j]<target)
                j++;
            else return true;
        }
    }
```

[234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

使用快慢指针找到链表中点，前半部分尾部置空，同时反转后面的节点，然后两个指针比较是否相等，相等的话就是回文表，前半部分的长度和后半长度相等或者少一个，只要判断第一个链表到头就可以了

[**162. 寻找峰值**](https://leetcode.cn/problems/find-peak-element/)

已知左边右边是最小值，假设右边比当前值小，那左边包括mid一定有峰值，因为右边比他小了，左边界是最小的

如果右边比mid大，那右边不包括mid一定有峰值

```java
public int findPeakElement(int[] nums) {
        int l=0,r=nums.length-1;
        while(l<=r)
        {
            int mid=l+(r-l)/2;
            if(nums[mid]<=(mid+1<nums.length?nums[mid+1]:Long.MIN_VALUE))
                l=mid+1;
            else r=mid-1;
        }
        return l;
    }
```

[152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)

三个变量，一个结果两个实时的最大最小值变量

```java
public int maxProduct(int[] nums) {
        int maxnum=nums[0],minnum=nums[0];
        int res=nums[0];
        for(int i=1;i<nums.length;i++)
        {
            if(nums[i]<0)
            {
                int temp=maxnum;
                maxnum=minnum;
                minnum=temp;
            }
            maxnum=Math.max(nums[i]*maxnum,nums[i]);
            minnum=Math.min(nums[i],nums[i]*minnum);
            res=Integer.max(maxnum,res);
        }
        return res;
    }
```

[**662. 二叉树最大宽度**](https://leetcode.cn/problems/maximum-width-of-binary-tree/)

想法非常好，根据完全二叉树的性质，左节点编号是根节点两倍，而右节点是根节点两倍加一

然后有一个直接查询本层编号差的方法，就是直接获取最后一个元素和第一个元素做差然后加一

```java
class node{
        TreeNode root;
        int num;
        node(TreeNode r,int num)
        {
            this.num=num;
            this.root=r;
        }
    }
    int res=0;
    public int widthOfBinaryTree(TreeNode root) {
        Deque<node> dq=new LinkedList<>();
        dq.add(new node(root,1));
        while(!dq.isEmpty())
        {
            int size=dq.size();
            int maxnum=dq.getLast().num;
            int minnum=dq.getFirst().num;
            res=Math.max(res,maxnum-minnum+1);
            for(int i=0;i<size;i++)
            {
                node temp=dq.poll();
                if(temp.root.left!=null)
                    dq.add(new node(temp.root.left,temp.num*2));
                if(temp.root.right!=null)
                    dq.add(new node(temp.root.right,temp.num*2+1));
            }
        }
        return res;
    }
```

[**179. 最大数**](https://leetcode.cn/problems/largest-number/)

一个是integer的问题，还有一个是equals("0")的问题

```java
public String largestNumber(int[] nums) {
        String num[]=new String[nums.length];
        for(int i=0;i< num.length;i++)
            num[i]=String.valueOf(nums[i]);
        //Integer.parseInt(b+a)-Integer.parseInt(a+b)是错误的会超过Integer的界限
        Arrays.sort(num,(a,b)->((b + a).compareTo(a + b)));
        if (num[0].equals("0")) {
            return "0";
        }
        StringBuilder sb=new StringBuilder();
        for(String s:num)
            sb.append(s);
        return sb.toString();
    }
```

[560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

非常巧妙，使用了前缀和的性质，不太容易想到，然后还有一个就是初始放一个（0，1）

![1748518074185](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1748518074185.png)

```java
public int subarraySum(int[] nums, int k) {
    java.util.HashMap<Integer, Integer> prefixSumCount = new java.util.HashMap<>();
    prefixSumCount.put(0, 1);
    int prefixSum = 0;
    int cnt = 0;

    for (int num : nums) {
        // 计算当前位置的前缀和
        prefixSum += num; 
        // 检查是否存在满足条件的前缀和
        if (prefixSumCount.containsKey(prefixSum - k)) { 
            // 若存在，将其出现次数累加到结果中
            cnt += prefixSumCount.get(prefixSum - k); 
        }
        // 更新当前前缀和的出现次数
        prefixSumCount.put(prefixSum, prefixSumCount.getOrDefault(prefixSum, 0) + 1); 
    }

    return cnt;
}
```

[227. 基本计算器 II](https://leetcode.cn/problems/basic-calculator-ii/)

有点难，需要思考好几个点

第一点  为什么要注释掉‘ ’，防止最后几个字符是空格最后的数字入不了栈

第二点 为什么需要if (!Character.isDigit(c) && c != ' ' || i == strs.length - 1)而不是直接else，原因是用于处理最后一个数字。

第三个很巧妙的点是把减法当加法最后用加法，然后一开始的presign是‘+’

```java
public int calculate(String s) {
        Deque<Integer> numstack = new LinkedList<>();
        char strs[] = s.toCharArray();
        int num = 0;
        char presign = '+';

        for (int i = 0; i < strs.length; ++i) {
            char c = strs[i];
            // if (c == ' ') continue;
            if (Character.isDigit(c)) {
                num = num * 10 + c - '0';
            }
            // 当遇到非数字字符或者到达字符串末尾时处理当前数字
            if (!Character.isDigit(c) && c != ' ' || i == strs.length - 1) { 
                if (sign == '+') {
                    numstack.push(num);
                } else if (sign == '-') {
                    numstack.push(-num);
                } else if (sign == '*') {
                    numstack.push(numstack.pop() * num);
                } else {
                    numstack.push(numstack.pop() / num);
                }
                num = 0;
                presign = c;
            }
        }

        int res = 0;
        for (int n : numstack) {
            res += n;
        }
        return res;
    }
```

[139. 单词拆分](https://leetcode.cn/problems/word-break/)

i的意思是不包含的上界，这样处理会很方便

```java
public boolean wordBreak(String s, List<String> wordDict) {
        boolean dp[]=new boolean[s.length()+1];
        dp[0]=true;
        for(int i=1;i<=s.length();i++)
        {
            for(String t:wordDict)
            {
                if(i>=t.length()&&dp[i-t.length()]&&s.substring(i-t.length(),i).equals(t))
                    dp[i]=true;
            }
        }
        return dp[s.length()];
    }
```

[283. 移动零](https://leetcode.cn/problems/move-zeroes/)

双指针，一个指针指向最前面非零元素可以放的地方，然后另一个指针遍历，一个个指针放过去，最后全部都是0元素，置零

```java
public void moveZeroes(int[] nums) {
         int i,j;
         i=0;j=0;
         for(;j< nums.length;j++)
         {
             if(nums[j]==0) continue;
             nums[i++]=nums[j];
         }
         while(i< nums.length)
         {
             nums[i++]=0;
         }
    }
```

[24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

找到要交换的两个节点的前一个结点，方便交换

```java
public ListNode swapPairs(ListNode head) {
        if (head==null||head.next==null) return head;
        ListNode dummy=new ListNode(),pre=dummy;
        dummy.next=head;
        while(pre.next!=null&&pre.next.next!=null)
        {
            ListNode fir=pre.next;
            ListNode sec=pre.next.next;
            fir.next=sec.next;
            sec.next=fir;
            pre.next=sec;
            pre=pre.next.next;
        }
        return dummy.next;
    }
```

[912. 排序数组](https://leetcode.cn/problems/sort-an-array/)  主要说堆排序

快速排序法，if(nums[l]<=p)可以免除与首元素相同元素额外赋值的问题  如果小于等于直接移动指针

如果大于就指针与右侧交换位置然后右侧减一，左侧不变。

优化思路，基准值使用随机数，双指针分区随机替换。

```java
void swap(int[] nums,int i,int j)
    {
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }

    public int[] sortArray(int[] nums) {
        quick(nums,0,nums.length-1);
        return nums;
    }

    void quick(int nums[],int left,int right)
    {
        if(left>=right) return;
        int l=left+1,r=right,p=nums[left];
        while(l<=r)
        {
            if(nums[l]<=p)
                l++;
            else swap(nums,l,r--);
        }
        swap(nums,left,l);
        quick(nums,left,l-1);
        quick(nums,l+1,right);
    }
```

```java
public static Random rd=new Random();
    public int[] sortArray(int[] nums) {
        Qsort(nums,0,nums.length-1);

        return nums;
    }

    public  void Qsort(int[] arr,int l,int r){
            if(l<r){
                int pivotpos=partition(arr,l,r);
                Qsort(arr,l,pivotpos-1);
                Qsort(arr,pivotpos+1,r);
            }
    }

    private int partition(int[] arr, int l, int r) {
        int rdindex=rd.nextInt(r-l+1)+l;
        int tmp=arr[rdindex];
        arr[rdindex]=arr[l];
        arr[l]=tmp;
        int pivot=arr[l];
        int i=l+1;
        int j=r;
        while(i<=j){
            while(i<=j&&arr[i]<pivot) i++;//不要改成<=不然时间复杂度很高
            while(i<=j&&arr[j]>pivot) j--;
            if(i>j) break;

            int temp=arr[i];
            arr[i]=arr[j];
            arr[j]=temp;

            i++;
            j--;
        }

        int temp=arr[j];
        arr[j]=arr[l];
        arr[l]=temp;

        return j;
    }
```

[138. 随机链表的复制](https://leetcode.cn/problems/copy-list-with-random-pointer/)

直接用hashmap可以直接给到一一对应的关系

```java
public Node copyRandomList(Node head) {
        HashMap<Node,Node> hm=new HashMap<>();
        Node temp=head;
        while(temp!=null)
        {
            hm.put(temp,new Node(temp.val));
            temp=temp.next;
        }
        for(Node p:hm.keySet())
        {
            hm.get(p).next=hm.get(p.next);
            hm.get(p).random=hm.get(p.random);
        }
        return hm.get(head);
    }
```

[468. 验证IP地址](https://leetcode.cn/problems/validate-ip-address/)

主要就是split(":", -1)这个函数给一个-1的参数，`split(":", -1)` 会把连续冒号、开头 / 结尾冒号都拆成空串）

```java
public String validIPAddress(String queryIP) {
        if (queryIP == null || queryIP.isEmpty()) {
            return "Neither";
        }
        // 尝试 IPv4：必须包含 '.' 且不包含 ':'
        if (queryIP.contains(".") && !queryIP.contains(":")) {
            if (isValidIPv4(queryIP)) {
                return "IPv4";
            }
        }
        // 尝试 IPv6：必须包含 ':' 且不包含 '.'
        if (queryIP.contains(":") && !queryIP.contains(".")) {
            if (isValidIPv6(queryIP)) {
                return "IPv6";
            }
        }
    }
    boolean isipv4(String ip)
    {
        String[] ips = ip.split("\\.", -1);
        //分割后不仅要检查数组长度是否为 8，还要确保 没有任何字段是空字符串（因为 split(":", -1) 会把连续冒号、开头 / 结尾冒号都拆成空串）
        if(ips.length!=4) return false;
        for(String s:ips)
        {
            if(s.length()==0||s.length()>3) return false;
            if(s.length()>1&&s.charAt(0)=='0')
                return false;
            for(char c:s.toCharArray())
                if(Character.isLetter(c))
                    return false;
            if(Integer.parseInt(s)>255)
                return false;
        }
        return true;
    }

    boolean isipv6(String ip)
    {
        String[] ips = ip.split(":", -1);
        //分割后不仅要检查数组长度是否为 8，还要确保 没有任何字段是空字符串（因为 split(":", -1) 会把连续冒号、开头 / 结尾冒号都拆成空串）
        if(ips.length!=8) return false;
        for(String s:ips)
        {
            if(s.length()==0||s.length()>4) return false;
            for(char c:s.toCharArray())
            {
                if(c>='0'&&c<='9'||c>='a'&&c<='f'||c>='A'&&c<='F')
                    continue;
                else return false;
            }
        }
        return true;
    }
```

[224. 基本计算器](https://leetcode.cn/problems/basic-calculator/)

```java
public int calculate(String s) {
        Deque<Integer> numStack = new LinkedList<>();
        Deque<Integer> signStack = new LinkedList<>();
        int result = 0;
        int sign = 1; // 初始为正数
        int n = s.length();
        int i = 0;
        while (i < n) {
            char c = s.charAt(i);
            if (c == ' ') {
                i++; // 跳过空格
                continue;
            }
            if (Character.isDigit(c)) {
                // 处理多位数
                int number = 0;
                while (i < n && Character.isDigit(s.charAt(i))) {
                    number = number * 10 + (s.charAt(i) - '0');
                    i++;
                }
                result += sign * number; // 加上当前的数字
                continue;
            }
            if (c == '+') {
                sign = 1; // 遇到 + 号，设置为正号
                i++;
            } else if (c == '-') {
                sign = -1; // 遇到 - 号，设置为负号
                i++;
            } else if (c == '(') {
                // 遇到左括号，将当前结果和符号入栈
                numStack.push(result);
                signStack.push(sign);
                // 重置当前的结果和符号
                result = 0;
                sign = 1;
                i++;
            } else if (c == ')') {
                // 遇到右括号，将栈中的结果和符号取出，合并到当前的计算结果
                result = numStack.pop() + signStack.pop() * result;
                i++;
            }
        }
        return result;
    }
```

[224. 基本计算器](https://leetcode.cn/problems/basic-calculator/)

碰到左括号要入双栈，保留原来左侧的全部内容，然后重新开始新的状态

对于输入是符号的时候，你只要改符号就行，不用入栈，只有左括号才要保留现场

```java
public int calculate(String s) {
    // 使用双栈法：numStack 存储中间结果，signStack 存储符号
    Deque<Integer> numStack = new LinkedList<>();
    Deque<Integer> signStack = new LinkedList<>();
    
    int result = 0; // 当前的计算结果
    int sign = 1;    // 当前的符号，初始为 1（正数）
    int n = s.length();
    int i = 0;       // 字符串遍历的索引
    
    while (i < n) {
        char c = s.charAt(i);
        
        // 1. 处理空格：直接跳过
        if (c == ' ') {
            i++;
            continue;
        }
        
        // 2. 处理数字：可能是多位数（如 "123"）
        if (Character.isDigit(c)) {
            int number = 0;
            // 循环读取连续的数字字符，构建完整的数字
            while (i < n && Character.isDigit(s.charAt(i))) {
                number = number * 10 + (s.charAt(i) - '0');
                i++;
            }
            // 将当前数字乘以符号后累加到结果
            result += sign * number;
            continue; // 继续处理下一个字符
        }
        
        // 3. 处理 '+' 号：设置当前符号为正
        if (c == '+') {
            sign = 1;
            i++;
        }
        // 4. 处理 '-' 号：设置当前符号为负
        else if (c == '-') {
            sign = -1;
            i++;
        }
        // 5. 处理 '(' 号：进入一个新的子表达式
        else if (c == '(') {
            // 将当前结果和符号压入栈，以便后续恢复
            numStack.push(result);
            signStack.push(sign);
            // 重置当前结果和符号，开始计算子表达式
            result = 0;
            sign = 1;
            i++;
        }
        // 6. 处理 ')' 号：子表达式结束，合并结果
        else if (c == ')') {
            // 弹出栈顶的符号和之前的结果，合并计算
            result = numStack.pop() + signStack.pop() * result;
            i++;
        }
    }
    
    return result; // 返回最终计算结果
}
```

[297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

1、使用层次遍历序列化，包括null值都在里面，在反序列化时除了根节点，其他不可能碰到null值，因为递归子节点的时候没有入队

```java
public class Codec {
    // 序列化：前序遍历（根→左→右），#表空节点，,分隔
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serial(root, sb);
        return sb.toString();
    }

    // 序列化辅助：递归写入节点
    private void serial(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append("#,"); // 空节点标记
            return;
        }
        sb.append(root.val).append(','); // 写入当前节点值+分隔符
        serial(root.left, sb); // 递归左子树
        serial(root.right, sb); // 递归右子树
    }

    private int idx = 0; // 反序列化索引指针

    // 反序列化：从字符串重建二叉树
    public TreeNode deserialize(String data) {
        idx = 0; // 重置索引
        return deserial(data);
    }

    // 反序列化辅助：递归解析节点
    private TreeNode deserial(String data) {
        // 解析空节点
        if (data.charAt(idx) == '#') {
            idx += 2; // 跳过#和,
            return null;
        }

        // 解析数字（支持负数）
        int num = 0;
        int sign = 1;
        if (data.charAt(idx) == '-') { // 处理负号
            sign = -1;
            idx++;
        }
        while (idx < data.length() && Character.isDigit(data.charAt(idx))) { // 解析数字部分
            num = num * 10 + (data.charAt(idx) - '0');
            idx++;
        }
        num *= sign; // 应用符号

        // 构建当前节点并递归解析子树
        TreeNode root = new TreeNode(num);
        idx++; // 跳过分隔符,
        root.left = deserial(data); // 左子树
        root.right = deserial(data); // 右子树
        return root;
    }
}
```

使用深搜代码量更少，但是递归有点抽象，非常推荐！

```java
//序列化，正常的前序遍历
public String serialize(TreeNode root) {
    if (root == null) {
        return ""; // 空树返回空字符串
    }
    StringBuilder sb = new StringBuilder();
    dfs(root, sb); // 从根节点开始DFS
    return sb.toString();
}

void dfs(TreeNode node, StringBuilder sb) {
    if (node == null) {
        sb.append("none,"); // 空节点标记为 "none,"
        return;
    }
    sb.append(node.val).append(","); // 当前节点的值
    dfs(node.left, sb);  // 递归左子树
    dfs(node.right, sb); // 递归右子树
}

//反序列化，使用了全局变量跟踪节点序号，前序建立树
public TreeNode deserialize(String data) {
    if (Objects.equals(data, "")) {
        return null; // 空字符串返回空树
    }
    String[] split = data.split(","); // 按逗号分割字符串
    return build(split); // 从数组重建树
}

int idx = 0; // 全局索引，跟踪当前处理的节点位置

private TreeNode build(String[] split) {
    if (split[idx].equals("none")) {
        idx++; // 跳过空节点
        return null;
    }
    TreeNode node = new TreeNode(Integer.parseInt(split[idx++])); // 创建当前节点
    node.left = build(split);  // 递归构建左子树
    node.right = build(split); // 递归构建右子树
    return node;
}
```

[153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

最后找左边的时候记得是left到mid而不是mid-1

```java
public int findMin(int[] nums) {
        return find(nums,0,nums.length-1);
    }
    int find(int[] nums,int left,int right)
    {
        if(left==right) return nums[left];
        int mid=(left+right)/2;
        if(nums[mid]>nums[right]) return find(nums,mid+1,right);
        else return find(nums,left,mid);
    }

//为什么 这样写呢，因为每次除以二的时候那个指针会偏向左边一点，所以我们要用右边作为基准去比较，如果右边有序，说明最小值在左边包括mid，否则说明最小值在mid右边，不包括mid
public int findMin(int[] nums) {
        int left=0,right=nums.length-1;
        while(left<right)
        {
            int mid=left+(right-left)/2;
            if(nums[mid]<nums[right])
                right=mid;
            else left=mid+1;
        }
        return nums[left];
    }
```

[79. 单词搜索](https://leetcode.cn/problems/word-search/)

DFS+visited数组

```java
public boolean exist(char[][] board, String word) {
        visited=new boolean[board.length][board[0].length];
        for(int i=0;i<board.length;i++)
            for(int j=0;j<board[0].length;j++)
                if(find(board,word,0,i,j))
                    return true;
        return false;
    }

    int dir[][]=new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
    boolean visited[][];
    boolean find(char [][]board,String word,int index,int x,int y)
    {
        if(board[x][y]==word.charAt(index))
        {
            if(++index==word.length()) return true;
        } else return false;
        visited[x][y]=true;
        for(int i=0;i<4;i++)
        {
            int nextx=x+dir[i][0],nexty=y+dir[i][1];
            if(nextx<0||nexty<0||nextx>= board.length||nexty>=board[0].length||visited[nextx][nexty])
                continue;
            if(find(board,word,index,nextx,nexty))
                return true;
        }
        visited[x][y]=false;
        return false;
    }
```

[402. 移掉 K 位数字](https://leetcode.cn/problems/remove-k-digits/)

单调栈加贪心，左侧数字比右侧大就可以出栈，若还有剩余把最后的删掉，因为它比较大，两次贪心

![1748861608949](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1748861608949.png)

```java
public String removeKdigits(String num, int k) {
        if(k>=num.length()) return "0";
        Deque<Character> dq=new LinkedList<>();
        for(char c:num.toCharArray())
        {
            while(!dq.isEmpty()&&c<dq.peekLast()&&k>0)
            {
                dq.removeLast();
                --k;
            }
            dq.addLast(c);
        }
        while(k>0)
        {
            dq.removeLast();
            --k;
        }
        StringBuilder sb=new StringBuilder();
        boolean prezero=true;
        for(char c:dq) {
            if (prezero && c == '0') continue;
            else prezero = false;
            sb.append(c);
        }
        if(sb.length()==0) return "0";
        return sb.toString();
    }
```

[47. 全排列 II](https://leetcode.cn/problems/permutations-ii/)

主要是去重逻辑，就是if(visited[i]||i>0&&nums[i]==nums[i-1]&&visited[i-1]==false)continue;

```java
LinkedList<Integer> path=new LinkedList<>();
    List<List<Integer>> res=new ArrayList<>();
    boolean visited[];
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        visited=new boolean[nums.length];
        travel(nums);
        return res;
    }
    void travel(int nums[])
    {
        if(path.size()==nums.length)
        {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i=0;i< nums.length;i++)
        {
            if(visited[i]||i>0&&nums[i]==nums[i-1]&&visited[i-1]==false)continue;
            visited[i]=true;
            path.add(nums[i]);
            travel(nums);
            path.removeLast();
            visited[i]=false;
        }
    }
```

**[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)**

双指针贪心，从最左侧最右侧开始计算面积，更新面积最大的，然后更新较短一侧指针，说不定可以变大，虽然长少了但是宽说不定变大。

```java
public int maxArea(int[] height) {
        int res=Integer.MIN_VALUE;
        int i=0,j=height.length-1;
        while(i<j)
        {
            res=Math.max(res,Math.min(height[i],height[j])*(j-i));
            if(height[i]>height[j])
                --j;
            else ++i;
        }
        return res;
    }
```

[460. LFU 缓存](https://leetcode.cn/problems/lfu-cache/)

最难题，双向链表基础结构，频率哈希表同时维护LRU的逻辑，链表节点哈希表，需要一个当前最小频率的全局变量。

```java
/**
 * LFU (Least Frequently Used) 缓存实现
 * 核心思想：淘汰访问频率最低的键，频率相同时淘汰最久未使用的键（LRU）
 * 时间复杂度：get/put 均为 O(1)
 */
class LFUCache {
    // 双向链表节点类（存储键值对和访问频率）
    class ListNode {
        int key;        // 键
        int value;      // 值
        int cnt;        // 访问计数（频率）
        ListNode pre;   // 前驱节点
        ListNode next;  // 后继节点

        // 默认构造函数（用于创建虚拟头节点）
        ListNode() {}

        // 带参构造函数（初始化键值对和频率）
        ListNode(int k, int v, int c) {
            key = k;
            value = v;
            cnt = c;
        }
    }
    // 核心数据结构
    private int capacity;     // 缓存容量
    private int minFreq;      // 当前最小频率（用于快速定位淘汰候选）
    private Map<Integer, ListNode> nodeMap;  // key -> Node 的映射（O(1)访问）
    private Map<Integer, ListNode> cntMap;   // freq -> 虚拟头节点 的映射（频率分层）

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.minFreq = 0;
        this.nodeMap = new HashMap<>();
        this.cntMap = new HashMap<>();
    }

    /**
     * 获取键值对（O(1)）
     * 1. 若键不存在返回-1
     * 2. 若存在：
     *    - 从原频率链表移除节点
     *    - 更新minFreq（若原链表变空且为minFreq）
     *    - 提升节点频率并插入新频率链表头部
     */
    public int get(int key) {
        if (!nodeMap.containsKey(key)) {
            return -1;
        }
        ListNode node = nodeMap.get(key);
        // 从原频率链表移除节点
        removeNode(node);
        // 检查是否需要更新minFreq
        ListNode dummy = cntMap.get(node.cnt);
        if (dummy.next == dummy && minFreq == node.cnt) {
            minFreq++; // 若移除后链表为空且为minFreq，则minFreq+1
        }
        
        // 提升频率并重新插入
        node.cnt++;
        insertNode(node, node.cnt);
        return node.value;
    }

    /**
     * 插入/更新键值对（O(1)）
     * 1. 若键存在：更新值并提升频率（同get逻辑）
     * 2. 若键不存在：
     *    - 若缓存已满，淘汰minFreq链表尾部节点
     *    - 创建新节点（频率=1）并插入频率1链表
     *    - 更新minFreq=1
     */
    public void put(int key, int value) {
        if (capacity <= 0) return; // 边界条件处理
        ListNode node;
        if (nodeMap.containsKey(key)) {
            // 情况1：键已存在（逻辑同get）
            node = nodeMap.get(key);
            node.value = value;
            removeNode(node);
            ListNode dummy = cntMap.get(node.cnt);
            if (dummy.next == dummy && minFreq == node.cnt) {
                minFreq++;
            }
            node.cnt++;
            insertNode(node, node.cnt);
        } else {
            // 情况2：键不存在
            if (nodeMap.size() >= capacity) {
                // 缓存已满，淘汰minFreq链表尾部节点
                ListNode dummy = cntMap.get(minFreq);
                ListNode tail = dummy.pre; // 尾部是最久未使用
                nodeMap.remove(tail.key);  // 移除键映射
                removeNode(tail);          // 从链表移除
            } else if (capacity > 0) {
                capacity--; // 仅用于初始化容量判断
            }
            // 创建新节点（频率初始为1）
            node = new ListNode(key, value, 1);
            insertNode(node, 1);
            minFreq = 1; // 新节点频率必为1
        }
        nodeMap.put(key, node); // 更新键映射
    }

    /**
     * 初始化频率链表（创建虚拟头节点）
     * 虚拟头节点简化链表操作（无需处理null指针）
     */
    private void initCnt(int cnt) {
        ListNode head = new ListNode();
        head.pre = head;
        head.next = head; // 自环表示空链表
        cntMap.put(cnt, head);
    }
    /**
     * 将节点插入对应频率链表的头部（O(1)）
     * 1. 若频率不存在，先初始化链表
     * 2. 将节点插入虚拟头节点之后（头部）
     */
    private void insertNode(ListNode node, int cnt) {
        if (!cntMap.containsKey(cnt)) {
            initCnt(cnt);
        }
        ListNode head = cntMap.get(cnt);
        // 插入到虚拟头节点之后
        node.next = head.next;
        node.pre = head;
        head.next.pre = node;
        head.next = node;
    }
    /**
     * 从链表中移除节点（O(1)）
     * 标准双向链表删除操作
     */
    private void removeNode(ListNode node) {
        node.pre.next = node.next;
        node.next.pre = node.pre;
    }
}
```

[912. 排序数组](https://leetcode.cn/problems/sort-an-array/)

归并排序，从前往后排，每次需要一个O(n)空间的消耗

```java
public int[] sortArray(int[] nums) {
        mergesort(nums,0,nums.length-1);
        return nums;
    }

    void mergesort(int[] nums,int left,int right)
    {
        if(right-left<1) return;
        int mid=left+(right-left)/2;
        mergesort(nums,left,mid);
        mergesort(nums,mid+1,right);
        merge(nums,left,mid,mid+1,right);
    }
    void merge(int[] nums,int l1,int r1,int l2,int r2)
    {
        int[] res=new int[r2-l1+1];
        int idx=0,i=l1,j=l2;
        while(i<=r1&&j<=r2)
        {
            if(nums[i]<nums[j]) res[idx++]=nums[i++];
            else res[idx++]=nums[j++];
        }
        while(i<=r1) res[idx++]=nums[i++];
        while(j<=r2) res[idx++]=nums[j++];
        System.arraycopy(res,0,nums,l1,res.length);//这个函数第一次遇到不会的话就一个一个拷回去吧
    }
```

堆排序

```java
public int[] sortArray(int[] nums) {
    heapSort(nums);
    return nums;
}

void heapSort(int[] nums) {
    int n = nums.length;
    
    // 1. 构建最大堆（从最后一个非叶子节点开始调整）
    for (int i = n / 2 - 1; i >= 0; i--) {
        heapify(nums, n, i);
    }
    
    // 2. 逐个提取堆顶元素（最大值）并调整堆
    for (int i = n - 1; i > 0; i--) {
        // 交换堆顶（最大值）和当前末尾元素
        swap(nums, 0, i);
        // 调整剩余堆（长度减1，排除已归位的元素）
        heapify(nums, i, 0);
    }
}

// 调整以节点i为根的子树，使其满足最大堆性质
void heapify(int[] nums, int heapSize, int i) {
    int largest = i;       // 初始化当前节点为最大值
    int left = 2 * i + 1;  // 左子节点索引
    int right = 2 * i + 2; // 右子节点索引
    
    // 如果左子节点存在且大于父节点
    if (left < heapSize && nums[left] > nums[largest]) {
        largest = left;
    }
    
    // 如果右子节点存在且大于当前最大值（可能是父节点或左子节点）
    if (right < heapSize && nums[right] > nums[largest]) {
        largest = right;
    }
    
    // 如果最大值不是父节点，交换并递归调整受影响子树
    if (largest != i) {
        swap(nums, i, largest);
        heapify(nums, heapSize, largest); // 递归调整被交换的子节点
    }
}

// 交换数组中两个元素
void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

[498. 对角线遍历](https://leetcode.cn/problems/diagonal-traverse/)

左下边界转上，右上边界转下

```java
public int[] findDiagonalOrder(int[][] mat) {
    if (mat.length == 0) return new int[0];
    int m = mat.length, n = mat[0].length;
    int[] res = new int[m * n];
    int row = 0, col = 0;
    boolean up = true; // 初始方向为“上”
    
    for (int i = 0; i < res.length; i++) {
        res[i] = mat[row][col];
        
        if (up) { // 向上遍历
            if (col == n - 1) { // 右边界：转向下
                row++;
                up = false;
            } else if (row == 0) { // 上边界：转向下
                col++;
                up = false;
            } else { // 正常向上移动
                row--;
                col++;
            }
        } else { // 向下遍历
            if (row == m - 1) { // 下边界：转向上
                col++;
                up = true;
            } else if (col == 0) { // 左边界：转向上
                row++;
                up = true;
            } else { // 正常向下移动
                row++;
                col--;
            }
        }
    }
    return res;
}
```

[40. 组合总和 II](https://leetcode.cn/problems/combination-sum-ii/)

难在去重的条件：if(i>idx&&candidates[i]==candidates[i-1])而不是i>0；

```java
List<List<Integer>> res=new ArrayList<>();
LinkedList<Integer> path=new LinkedList<>();
public List<List<Integer>> combinationSum2(int[] candidates, int target) {
    Arrays.sort(candidates);
    travel(candidates,0,target);
    return res;
}
void travel(int candidates[],int idx,int target)
{
    if(target==0)
    {
        res.add(new ArrayList<>(path));
        return;
    }
    for(int i=idx;i<candidates.length;i++)
    {
        if(i>idx&&candidates[i]==candidates[i-1])continue;
        if(target<candidates[i]) break;
        path.add(candidates[i]);
        travel(candidates,i+1,target-candidates[i]);
        path.removeLast();
    }
}
```

[16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)

双指针秒了，每次都要比较一下，还需要一个额外的变量记录当前距离最小的绝对值。

[74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

两种方法，二分法去找，第二种方法，从左下和右上去找，越界了就说明没有。

二分变成一维去找行和列会很方便

```java
public boolean searchMatrix(int[][] matrix, int target) {
        int row= matrix.length,col= matrix[0].length;
        int left=0,right=row*col-1;
        while(left<=right)
        {
            int mid=(left+right)/2;
            int nowr=mid/col,nowc=mid%col;
            if(target==matrix[nowr][nowc])
                return true;
            if(target<matrix[nowr][nowc])
                right=mid-1;
            else left=mid+1;
        }
        return false;
    }
```

[7. 整数反转](https://leetcode.cn/problems/reverse-integer/)

```java
public int reverse(int x) {
        int result = 0;
        while(x != 0) {
            int digit = x % 10;
            if(result > Integer.MAX_VALUE / 10  || result < Integer.MIN_VALUE / 10) return 0;
            result = result * 10 + digit;
            x /= 10;
        }

        return result;
    }
```

[50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

快速幂算法，这个省了很多中间变量，最后肯定会经过1把它乘进去

```java
public double myPow(double x, int n) {
        long N = n; // 避免 Integer.MIN_VALUE 溢出
        if (N < 0) {
            x = 1.0 / x;
            N = -N;
        }
        double res = 1.0; // 结果用 double 存储
        while (N > 0) {
            if (N % 2 == 1) {
                res *= x;
            }
            x *= x; // 无论奇偶都要平方
            N /= 2; // 无论奇偶都要折半
        }
        return res;
    }
```

[75. 颜色分类](https://leetcode.cn/problems/sort-colors/)

维护三个指针，包括当前指针，0和2的当前指针，在遇到不同情况时采取不同措施，有的now要++有的不要

```java
public void sortColors(int[] nums) {
        int zero=0,two=nums.length-1,now=0;
        while(now<=two)
        {
            if(nums[now]==0)
                swap(nums,zero++,now++);
            else if(nums[now]==1)
                now++;
            else swap(nums,now,two--);
        }
    }
    void swap(int[] nums,int i,int j)
    {
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }
```

[572. 另一棵树的子树](https://leetcode.cn/problems/subtree-of-another-tree/)

直接DFS找所有节点然后比较就好了，本来想用哈希表存对应节点然后遍历根节点值相同的原树的集合，过于复杂

```java
public boolean isSubtree(TreeNode root, TreeNode subRoot) {
    if (root == null) return subRoot == null;
    return isSame(root, subRoot) || 
           isSubtree(root.left, subRoot) || 
           isSubtree(root.right, subRoot);
}

private boolean isSame(TreeNode a, TreeNode b) {
    if (a == null && b == null) return true;
    if (a == null || b == null) return false;
    return a.val == b.val && 
           isSame(a.left, b.left) && 
           isSame(a.right, b.right);
}
```

[LCR 143. 子结构判断](https://leetcode.cn/problems/shu-de-zi-jie-gou-lcof/)

### **问题 572（另一个树的子树）与本题（树的子结构）的区别**

这两道题目看似相似，但实际要求有 **关键区别**：

|     **题目**     |               **LeetCode 572. 另一个树的子树**               |                    **本题（树的子结构）**                    |
| :--------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|   **题目要求**   | 判断树 `t` 是否是树 `s` 的 **完全相同的子树**（包括所有后代节点） | 判断树 `B` 是否是树 `A` 的 **子结构**（不要求完全匹配所有后代节点） |
|   **匹配规则**   |  `t` 必须和 `s` 的某个子树 **完全相同**（结构和值完全一致）  | `B` 只需和 `A` 的某一部分 **结构相同**（允许 `A` 的节点比 `B` 多） |
|   **空树处理**   |              `t` 为空时，`s` 也必须为空才算匹配              |        `B` 为空时，不算匹配（题目约定空树不是子结构）        |
| **递归终止条件** |  `if (s == null && t == null) return true;`（必须同时为空）  |    `if (b == null) return true;`（`B` 的子树匹配完即可）     |
|   **示例对比**   |     `s = [3,4,5,1,2], t = [4,1,2]` → `true`（完全匹配）      | `A = [3,4,5,1,2], B = [4,1]` → `true`（`B` 是 `A` 的子结构，即使 `A` 的 `4` 有右子树 `2`） |

```java
public boolean isSubStructure(TreeNode a, TreeNode b) {
    if (a == null || b == null) return false; // 题目约定空树不是任意树的子结构
    return isSame(a, b) || isSubStructure(a.left, b) || isSubStructure(a.right, b);
}

boolean isSame(TreeNode a, TreeNode b) {
    if (b == null) return true; // b 的子树已匹配完
    if (a == null || a.val != b.val) return false;
    return isSame(a.left, b.left) && isSame(a.right, b.right); // 必须左右子树同时匹配
}
```

[LCR 159. 库存管理 III](https://leetcode.cn/problems/zui-xiao-de-kge-shu-lcof/)

使用两个方法

1、快速选择算法，想要得到前k个最小的元素，可以每次得到一个元素的准确位置，然后再递归，看k的位置在哪，然后所有左边的元素就是

2、堆排序算法，维护一个k大的最大堆，然后根据每一个元素的大小和堆顶比较决定是否要poll然后加入

核心是快速选择算法，这是排序的核心代码：

```java
int partition(int[] arr, int left, int right) {
    int pivot = arr[left];
    int l = left, r = right;
    while (l < r) {
        while (l < r && arr[r] >= pivot) r--;
        while (l < r && arr[l] <= pivot) l++;
        if (l < r) swap(arr, l, r);
    }
    swap(arr, left, l);
    return l;
}
```

[LCR 170. 交易逆序对的总数](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

归并排序思想，在不断归并之后逐步累加逆序对数目

核心的两个代码：count += mid - i + 1;以及System.arraycopy(temp, left, nums, left, right - left + 1);

当然别忘了if (nums[i] <= nums[j]) {
            // 情况1：左元素 <= 右元素 → 无逆序对，直接放入临时数组
            temp[k++] = nums[i++];
        } 左边比较小的时候直接移动指针

```java
public int reversePairs(int[] nums) {
    // 边界检查：空数组或单元素数组无逆序对
    if (nums == null || nums.length < 2) {
        return 0;
    }
    // 辅助数组，用于归并排序时临时存储合并后的有序序列
    int[] temp = new int[nums.length];
    // 调用归并排序并统计逆序对
    return mergeSortAndCount(nums, 0, nums.length - 1, temp);
}

/**
 * 归并排序并统计逆序对
 * @param nums  原始数组
 * @param left  当前子数组的起始下标
 * @param right 当前子数组的结束下标
 * @param temp  临时辅助数组
 * @return      当前子数组内的逆序对总数
 */
private int mergeSortAndCount(int[] nums, int left, int right, int[] temp) {
    // 递归终止条件：子数组长度为1时，逆序对数为0
    if (left >= right) {
        return 0;
    }
    // 分治：将数组分成左右两部分
    int mid = left + (right - left) / 2;
    // 递归统计左半部分的逆序对 + 右半部分的逆序对
    int count = mergeSortAndCount(nums, left, mid, temp) 
              + mergeSortAndCount(nums, mid + 1, right, temp);
    // 核心逻辑：合并两个有序子数组，并统计跨越左右的逆序对
    int i = left;      // 左半部分的指针（范围：[left, mid]）
    int j = mid + 1;   // 右半部分的指针（范围：[mid+1, right]）
    int k = left;      // 临时数组的填充指针
    // 合并过程
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            // 情况1：左元素 <= 右元素 → 无逆序对，直接放入临时数组
            temp[k++] = nums[i++];
        } else {
            // 情况2：左元素 > 右元素 → 产生逆序对
            // 此时，左半部分中从 i 到 mid 的所有元素均 > nums[j]，因此逆序对数为 mid - i + 1
            count += mid - i + 1;
            temp[k++] = nums[j++];
        }
    }
    // 处理剩余元素：左半部分或右半部分可能还有未合并的元素
    while (i <= mid) {
        temp[k++] = nums[i++];
    }
    while (j <= right) {
        temp[k++] = nums[j++];
    }
    // 将临时数组中已排序的部分拷贝回原数组，保证后续递归的正确性
    System.arraycopy(temp, left, nums, left, right - left + 1);
    return count;
}
```

[440. 字典序的第K小数字](https://leetcode.cn/problems/k-th-smallest-in-lexicographical-order/)

![1749385333884](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749385333884.png)

![1749385344416](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749385344416.png)

![1749385397421](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749385397421.png)

![1749384007041](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749384007041.png)

```java
public int findKthNumber(int n, int k) {
    long prefix = 1; // 当前前缀（初始为1）
    int count = 1;    // 当前已统计的数字数量（初始为1，即数字1本身）
    
    while (count < k) {
        // 计算以prefix为前缀的数字总数（包括prefix本身）
        long total = countNumbersWithPrefix(n, prefix);
        
        if (count + total > k) {
            // 情况1：第k个数字在prefix的子树中
            prefix *= 10;  // 进入下一层（如1→10）
            count++;       // 每深入一层，count+1（因为prefix本身是一个数字）
        } else {
            // 情况2：第k个数字不在prefix的子树中
            prefix++;      // 切换到下一个前缀（如1→2）
            count += total; // 跳过当前前缀的所有数字
        }
    }
    return (int) prefix;
}

private long countNumbersWithPrefix(int n, long prefix) {
    long nextPrefix = prefix + 1; // 下一个前缀（如prefix=1时，nextPrefix=2）
    long count = 0;
    
    while (prefix <= n) {
        // 当前层的数字数量 = min(nextPrefix, n+1) - prefix
        count += Math.min(nextPrefix, n + 1) - prefix;
        prefix *= 10;      // 进入下一层（如1→10）
        nextPrefix *= 10;   // 下一层的下一个前缀（如2→20）
    }
    return count;
}
```

[91. 解码方法](https://leetcode.cn/problems/decode-ways/)

动态规划，递推方程比较新颖

主要是有两个转移逻辑，找寻前一个数字是不是符合以及前两个数字是不是符合，如果符合就加上

一个是初始化，i表示到下标多少之前一共有多少种可能

```java
public int numDecodings(String s) {
        if (s == null || s.length() == 0 || s.charAt(0) == '0') return 0;
        int n = s.length();
        int[] dp = new int[n + 1];  // dp[i] 表示前 i 个字符的解码方式数量
        dp[0] = 1;  // 空字符串只有一种解码方式
        for (int i = 1; i <= n; i++) {
            // 如果当前字符是有效的解码字符 ('1' to '9')
            if (s.charAt(i - 1) != '0') {
                dp[i] += dp[i - 1];
            }
            // 如果前两个字符是有效的解码字符 (10-26)
            if (i > 1 && s.charAt(i - 2) != '0' && Integer.parseInt(s.substring(i - 2, i)) <= 26) {
                dp[i] += dp[i - 2];
            }
        }
        return dp[n];
    }
```

[442. 数组中重复的数据](https://leetcode.cn/problems/find-all-duplicates-in-an-array/)

看题目，有一个非常重要的数据就是给你一个长度为 n 的整数数组 nums ，其中 nums 的所有整数都在**范围 [1, n] 内**，且每个整数出现 最多两次 。请你找出所有出现 两次 的整数，并以数组形式返回。

都在范围1到n内，说明可以使用哈希找到当前值的唯一下标使用负数做标记。

```java
public List<Integer> findDuplicates(int[] nums) {
    List<Integer> res = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        int index = Math.abs(nums[i]) - 1; // 转换为0-based索引
        if (nums[index] > 0) {
            nums[index] = -nums[index];    // 标记为负数表示已访问
        } else {
            res.add(Math.abs(nums[i]));    // 已经是负数，说明重复
        }
    }
    return res;
}
```

[329. 矩阵中的最长递增路径](https://leetcode.cn/problems/longest-increasing-path-in-a-matrix/)

DFS+记忆化，记忆化的方法，在DFS中增加保存当前位置的最大的逻辑。

```java
public int longestIncreasingPath(int[][] matrix) {
    if (matrix.length == 0) return 0; // 处理空矩阵
    int m = matrix.length, n = matrix[0].length;
    int[][] dp = new int[m][n]; // 改为局部变量
    int max = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            max = Math.max(max, travel(matrix, i, j, dp)); // 传递 dp 并更新 max
        }
    }
    return max;
}

int[][] dir = {{0, 1}, {0, -1}, {-1, 0}, {1, 0}}; // 方向数组

int travel(int[][] matrix, int x, int y, int[][] dp) {
    if (dp[x][y] != 0) return dp[x][y]; // 已计算过，直接返回
    int max = 1; // 至少为 1（自身）
    for (int[] d : dir) {
        int nextX = x + d[0], nextY = y + d[1];
        // 检查边界和递增条件
        if (nextX >= 0 && nextX < matrix.length && nextY >= 0 && nextY < matrix[0].length 
            && matrix[nextX][nextY] > matrix[x][y]) {
            max = Math.max(max, travel(matrix, nextX, nextY, dp) + 1);
        }
    }
    dp[x][y] = max; // 记录结果
    return max;
}
```

[445. 两数相加 II](https://leetcode.cn/problems/add-two-numbers-ii/)

细节非常多，推荐循环相加的时候使用新节点，反转两次就好  while(t1!=null||t2!=null||add!=0)

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    l1=reversenode(l1);
    l2=reversenode(l2);
    ListNode t1=l1,t2=l2;
    int add=0;
    ListNode dummy=new ListNode();
    ListNode cur=dummy;
    while(t1!=null||t2!=null||add!=0)
    {
        int res=0;
        if(t1!=null)res+=t1.val;
        if(t2!=null)res+=t2.val;
        res+=add;
        if(res>9)
        {
            cur.next=new ListNode(res-10);
            add=1;
        }else{
            cur.next=new ListNode(res);
            add=0;
        }
        if(t1!=null) t1=t1.next;
        if(t2!=null) t2=t2.next;
        cur=cur.next;
    }
    return reversenode(dummy.next);
}

ListNode reversenode(ListNode l)
{
    ListNode dummy=new ListNode();
    dummy.next=l;
    ListNode cur=l;
    while(cur.next!=null)
    {
        ListNode ne=cur.next;
        cur.next=cur.next.next;
        ne.next=dummy.next;
        dummy.next=ne;
    }
    return dummy.next;
}
```

[10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)

初始化比较复杂，就是s串可能是空串或者p串是多个*串类似于\*a\*b\*c这样的串也可以匹配空串

其次是状态转移，一个可能是当前为 . 或者字符匹配了，如果是*可能匹配0个或者多个，如果匹配的话就直接

if (prev == '.' || prev == sc) {
                    dp\[i][j] |= dp\[i-1][j]; //一次或多次
                }

```java
public boolean isMatch(String s, String p) {
    if (p.startsWith("*")) return false; // * 不能开头
    int m = s.length(), n = p.length();
    boolean[][] dp = new boolean[m+1][n+1];
    dp[0][0] = true;

    // 初始化：p 的前 j 个字符能否匹配空字符串
    for (int j = 1; j <= n; j++) {
        if (p.charAt(j-1) == '*') {
            dp[0][j] = dp[0][j-2]; // * 匹配零次
        }
    }

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            char sc = s.charAt(i-1);
            char pc = p.charAt(j-1);
            if (pc == '.' || pc == sc) {
                dp[i][j] = dp[i-1][j-1]; // 直接匹配
            } else if (pc == '*') {
                dp[i][j] = dp[i][j-2]; // 匹配0次
                char prev = p.charAt(j-2);
                if (prev == '.' || prev == sc) {
                    dp[i][j] |= dp[i-1][j]; //一次或多次
                }
            }
        }
    }
    return dp[m][n];
}
```

### 字节跳动高频题——圆环回原点问题

![1749625554833](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749625554833.png)

![1749625580240](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749625580240.png)

[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

推荐直接使用堆，调库，主要是注意怎么使用for遍历map的写法

第二种方法，是根据频率使用快速选择算法，找到频率为k大的位置，前面k个元素就是前k个数组。

```java
public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer,Integer> hm=new HashMap<>();
        for(int num:nums)
            hm.put(num,hm.getOrDefault(num,0)+1);
        PriorityQueue<int[]> pq=new PriorityQueue<>((a,b)->b[1]-a[1]);
        for(Map.Entry<Integer,Integer> entry:hm.entrySet())
            pq.add(new int[]{entry.getKey(),entry.getValue()});
        int res[]=new int[k];
        for(int i=0;i<k;i++)
            res[i]=pq.poll()[0];
        return res;
    }
```

[208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

前缀树的实现，建树的时候用for循环迭代建树就可以了，然后有一个非常小的点，**建树的时候只有之前没有节点的时候才创建新节点，不然新创建节点会把之前的树顶掉，这样不对**

```java
class Trie {

        HashSet<String> hs=new HashSet<>();
        node root;
        class node{
            node []next=new node[26];
            String word;

        }
        public Trie() {
            root=new node();
        }

        public void insert(String word) {
            hs.add(word);
            node t=root;
            for(int i=0;i<word.length();i++)
            {
                int idx=word.charAt(i)-'a';
                if (t.next[idx] == null) 
                    t.next[idx] = new node(); // 仅当节点不存在时创建
                t=t.next[idx];
            }
            t.word=word;
        }

        public boolean search(String word) {
            return hs.contains(word);
        }

        public boolean startsWith(String prefix) {
            node p=root;
            for(int i=0;i<prefix.length();i++)
            {
                int idx=prefix.charAt(i)-'a';
                if(p.next[idx]==null)
                    return false;
                p=p.next[idx];
            }
            return true;
        }
    }
```

[295. 数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)

有一个问题是一开始的想法是都放左边的堆，只要size比右边大就poll一个过去

![1749732020131](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749732020131.png)

所以可行的办法是优先加入之后直接poll一个过去，如果另一个比这个多就poll回来，保证优先丢进去的堆的大小更多一点。

```java
class MedianFinder {
    PriorityQueue<Integer> maxHeap; // 存储较小的一半（最大堆）
    PriorityQueue<Integer> minHeap; // 存储较大的一半（最小堆）

    public MedianFinder() {
        maxHeap = new PriorityQueue<>((a, b) -> b - a);
        minHeap = new PriorityQueue<>();
    }

    public void addNum(int num) {
        // 1. 优先插入 maxHeap
        maxHeap.offer(num);
        // 2. 平衡堆：确保 maxHeap 的堆顶 <= minHeap 的堆顶
        minHeap.offer(maxHeap.poll());
        // 3. 维持堆的大小关系：maxHeap.size() >= minHeap.size()
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }

    public double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek(); // 奇数个元素时，maxHeap 多一个
        } else {
            return (maxHeap.peek() + minHeap.peek()) / 2.0; // 偶数个元素时取平均
        }
    }
}
```

[106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

又死在索引上，一个是leftlen=idx-inleft，而不是＋1，因为左子树长度等于left到idx-1，第二个是右子树的post左边界是postleft+leftlen，又多加了一个1，不用加。

```java
public TreeNode buildTree(int[] inorder, int[] postorder) {
        return build(inorder,postorder,0,inorder.length-1,0,postorder.length-1);
    }

    TreeNode build(int[] inorder, int[] postorder,int inleft,int inright,int postleft,int postright)
    {
        if(inleft>inright) return null;
        if(inleft==inright) return new TreeNode(inorder[inleft]);
        int idx=inleft;
        while(inorder[idx]!=postorder[postright])
            ++idx;
        int leftlen=idx-inleft;
        TreeNode now=new TreeNode(postorder[postright]);
        now.left=build(inorder,postorder,inleft,idx-1,postleft,postleft+leftlen-1);
        now.right=build(inorder,postorder,idx+1,inright,postleft+leftlen,postright-1);
        return now;
    }
```

[LCR 187. 破冰游戏](https://leetcode.cn/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

约瑟夫环问题，一个是数论，一个是在容器中一个一个移走

数论法

```Java
/**
 * 约瑟夫环问题的数学解法（最优解）
 * 时间复杂度O(n)，空间复杂度O(1)
 * 
 * 数学推导过程：
 * 1. 设f(n,m)表示n个人报数m时最后幸存者的编号
 * 2. 基本情况：f(1,m)=0（只有一个人时他就是幸存者）
 * 3. 递推关系：f(n,m)=(f(n-1,m)+m)%n
 *    - 当n个人时，第一个被淘汰的是(m-1)%n
 *    - 剩下的n-1个人组成新的约瑟夫环，从m%n开始
 *    - 新环的幸存者f(n-1,m)在原环中的位置就是(f(n-1,m)+m)%n
 * 4. 从f(1,m)=0开始，递推计算f(2,m)到f(n,m)
 * 
 * @param num 总人数
 * @param target 报数到target的人出局
 * @return 最后幸存者的初始编号
 */
public int iceBreakingGame(int num, int target) {
    int res = 0; // f(1,target)=0
    // 递推计算f(2,target)到f(num,target)
    for (int i = 2; i <= num; i++) {
        // f(i,target)=(f(i-1,target)+target)%i
        res = (res + target) % i;
    }
    return res;
}
```

模拟法

```java
/**
 * 约瑟夫环问题的ArrayList模拟解法
 * 时间复杂度O(n^2)，空间复杂度O(n)
 * 
 * 算法步骤：
 * 1. 初始化一个包含0到num-1的ArrayList
 * 2. 初始化当前索引为0（从第一个人开始）
 * 3. 循环直到只剩一个人：
 *    a. 计算要删除的位置：(当前索引 + target - 1) % 当前人数
 *       - target-1是因为当前位置的人算第一个
 *       - 取模运算处理循环情况
 *    b. 删除该位置的元素
 *    c. 人数减1
 *    d. 新的当前索引就是被删除元素的位置（因为后面元素会自动前移）
 * 4. 返回最后剩下的元素
 * 
 * @param num 总人数
 * @param target 报数到target的人出局
 * @return 最后幸存者的初始编号
 */
public int iceBreakingGame(int num, int target) {
    // 1. 初始化列表
    List<Integer> list = new ArrayList<>();
    for (int i = 0; i < num; i++) {
        list.add(i);
    }
    // 2. 初始化当前索引
    int index = 0;
    // 3. 循环删除直到只剩一人
    while (num > 1) {
        // 计算要删除的位置
        index = (index + target - 1) % num;
        // 删除该位置的元素
        list.remove(index);
        // 人数减1
        num--;   
        // index不需要更新，因为删除后后面的元素会自动前移
    }
    // 4. 返回最后剩下的元素
    return list.get(0);
}
```

[213. 打家劫舍 II](https://leetcode.cn/problems/house-robber-ii/)

极限压缩递推式，降到O(1)就可以

```java
/**
 * 解决环形房屋抢劫问题（房屋围成一圈，不能抢相邻的房屋）
 * 时间复杂度O(n)，空间复杂度O(1)
 */
public int rob(int[] nums) {
    if (nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];  // 修正：当只有一间房时应返回该房价值
    
    // 环形问题分解为两个线性问题：
    // 1. 抢第一间不抢最后一间
    // 2. 不抢第一间抢最后一间
    return Math.max(
        robRange(nums, 0, nums.length - 2),  // 情况1
        robRange(nums, 1, nums.length - 1)   // 情况2
    );
}

private int robRange(int[] nums, int start, int end) {
    // 使用两个变量代替DP数组，优化空间复杂度
    int prevNotRob = 0;  // 前一间房没抢时的最大价值
    int prevRob = 0;      // 前一间房抢了时的最大价值
    
    for (int i = start; i <= end; i++) {
        // 当前不抢 = 前一间抢或不抢的较大值
        int currNotRob = Math.max(prevNotRob, prevRob);
        // 当前抢 = 前一间没抢 + 当前房价值
        int currRob = prevNotRob + nums[i];
        
        // 更新状态为下一轮准备
        prevNotRob = currNotRob;
        prevRob = currRob;
    }
    
    return Math.max(prevNotRob, prevRob);
}
```

[516. 最长回文子序列](https://leetcode.cn/problems/longest-palindromic-subsequence/)

注意以后都用int的dp数组去解回文子序列和子数组的题，dp的含义是i到j最大回文序列长度

是从左下到右上，初始化也包括在里面了，包括i和j相等的，i和j相差一，如果相等会是在矩阵左下角为0的位置0+2=2，初始化的情况也包括进去了。

如果是子数组，那dp含义是从i到j是不是回文子数组，是的话就是长度，不是就为0.

```java
public int longestPalindromeSubseq(String s) {
        int dp[][]=new int[s.length()][s.length()];
        char []str=s.toCharArray();
        for(int i=s.length()-1;i>=0;i--)
            for(int j=i;j<s.length();j++)
            {
                if(i==j) dp[i][j]=1;
                else if(str[i]==str[j])
                    dp[i][j]=dp[i+1][j-1]+2;
                else dp[i][j]=Math.max(dp[i][j-1],dp[i+1][j]);
            }
        return dp[0][s.length()-1];
    }
```

[189. 轮转数组](https://leetcode.cn/problems/rotate-array/)

先k%=num.length,然后全面反转，再反转左边k，再反转右边

[225. 用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/)

一个应该也行，但是用两个，一个专门存栈顶，一个专门存轮转数组

```java
class MyStack {
        Deque<Integer> q1,q2;
        public MyStack() {
            q1=new LinkedList<>();
            q2=new LinkedList<>();
        }

        public void push(int x) {
            q1.add(x);
        }

        public int pop() {
            while(q1.size()>1)
                q2.add(q1.poll());
            int res=q1.poll();
            Deque<Integer> temp=q1;
            q1=q2;
            q2=temp;
            return res;
        }

        public int top() {
            while(q1.size()>1)
                q2.add(q1.poll());
            return q1.peek();
        }

        public boolean empty() {
            return q1.isEmpty();
        }
    }
```

[678. 有效的括号字符串](https://leetcode.cn/problems/valid-parenthesis-string/)

贪心最快，两个变量，一个匹配最少的左括号数，一个是最多，如果最多都是负数，说明右括号太多了

注意对minleft使用的是max  ，在最后返回条件是比较minleft是不是为零，这两个点需要注意

```java
public boolean checkValidString(String s) {
    int minLeft = 0; // 最少未匹配的左括号数（* 尽量当作 ')')
    int maxLeft = 0; // 最多未匹配的左括号数（* 尽量当作 '(')

    for (char c : s.toCharArray()) {
        if (c == '(') {
            minLeft++;
            maxLeft++;
        } else if (c == ')') {
            minLeft = Math.max(minLeft - 1, 0); // 不能小于 0
            maxLeft--;
            if (maxLeft < 0) return false; // 右括号过多
        } else { // '*'
            minLeft = Math.max(minLeft - 1, 0); // 当作 ')'
            maxLeft++;                         // 当作 '('
        }
    }
    return minLeft == 0; // 所有左括号均被匹配
}
```

DFS+记忆化，每次的结果存入Hash中方便找，相对复杂

```java
public boolean checkValidString(String s) {
    return check(s.toCharArray(), 0, 0, new HashMap<>());
}

private boolean check(char[] str, int idx, int left, Map<String, Boolean> memo) {
    String key = idx + "," + left; // 用 (idx, left) 作为唯一键
    if (memo.containsKey(key)) return memo.get(key); // 查缓存

    if (idx == str.length) {
        memo.put(key, left == 0);
        return left == 0;
    }
    if (left < 0) { // 提前终止无效分支
        memo.put(key, false);
        return false;
    }

    boolean isValid = false;
    if (str[idx] == '(') {
        isValid = check(str, idx + 1, left + 1, memo);
    } else if (str[idx] == ')') {
        isValid = left > 0 && check(str, idx + 1, left - 1, memo);
    } else { // '*'
        isValid = check(str, idx + 1, left + 1, memo)    // 当作 '('
               || check(str, idx + 1, left, memo)        // 当作 ''
               || (left > 0 && check(str, idx + 1, left - 1, memo)); // 当作 ')'
    }

    memo.put(key, isValid); // 存缓存
    return isValid;
}
```

[887. 鸡蛋掉落](https://leetcode.cn/problems/super-egg-drop/)

### **动态规划解法**

![1764330239450](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1764330239450.png)

![1749874946371](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749874946371.png)

楼层覆盖的情况，使用k个鸡蛋进行m次尝试可以覆盖的总楼层数

### **4. 修正后的代码（数学优化版）**

```java
public int superEggDrop(int k, int n) {
    int m = 0;//m次尝试k个鸡蛋
    int[][] dp = new int[n + 1][k + 1]; // dp[m][k] = 最多能测的楼层数

    while (dp[m][k] < n) {
        m++;
        for (int i = 1; i <= k; i++) {
            dp[m][i] = dp[m - 1][i - 1] + dp[m - 1][i] + 1;
            //        没碎，m-1次尝试i-1个鸡蛋   碎了，测到了dp[m - 1][i]个鸡蛋  +1本层
        }
    }
    return m;
}
```

#### **优化空间（O(k)）**

```java
public int superEggDrop(int k, int n) {
    int[] dp = new int[k + 1]; // dp[k] = 当前尝试次数下最多能测的楼层数
    int m = 0;
    while (dp[k] < n) {
        m++;
        for (int i = k; i >= 1; i--) {
            dp[i] += dp[i - 1] + 1;
        }
    }
    return m;
}
```

[384. 打乱数组](https://leetcode.cn/problems/shuffle-an-array/)

使用两个数组备份，一个原数组不动，一个专门用来洗牌打乱，核心是学会洗牌算法

```java
class Solution {
    private int[] original;
    private int[] shuffled;
    private Random random;

    public Solution(int[] nums) {
        original = nums.clone();      // 备份原数组
        shuffled = nums.clone();      // 初始化 shuffled
        random = new Random();
    }

    public int[] reset() {
        return original.clone();      // 返回原数组的副本（避免外部修改）
    }

    //Fisher-Yates 算法的关键是：每一步只处理 “未确定位置的元素”，确保每个元素被选中的概率仅与剩余未处理元素数量相关。
    //简单说：洗牌的核心是 “不回头”—— 一旦某个元素的位置确定，就不再参与后续交换，这样才能保证等概率。
    可以这样理解 三个元素，最后一个元素概率1/3 然后2/3*1/2 最后一个就是2/3*1/2*1/1
    public int[] shuffle() {
        // Fisher-Yates 洗牌算法（直接在 shuffled 上操作）
        for (int i = shuffled.length - 1; i > 0; i--) {
            int j = random.nextInt(i + 1); // 随机选一个索引 [0, i]
            // 交换 shuffled[i] 和 shuffled[j]
            int temp = shuffled[i];
            shuffled[i] = shuffled[j];
            shuffled[j] = temp;
        }
        return shuffled.clone();      // 返回打乱后的副本（避免外部修改）
    }
}
```

[611. 有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/)

我的想法是三重循环，但是时间复杂度很大，使用双指针，选取最长边然后left和right移动，每一次right-1如果还能满足条件说明从left到right-1的位置都可以形成三角形

```java
public int triangleNumber(int[] nums) {
        Arrays.sort(nums);
        int cnt = 0;
        for (int i = nums.length - 1; i >= 2; i--) {
            if (nums[i] == 0) continue; // 0 不能作为边长
            int left = 0, right = i - 1;
            while (left < right) {
                if (nums[left] + nums[right] > nums[i]) {
                    cnt += right - left;
                    right--;
                } else {
                    left++;
                }
            }
        }
        return cnt;
    }
```

[44. 通配符匹配](https://leetcode.cn/problems/wildcard-matching/)

动态规划Boolean数组dp\[m][n]，表示的是能否在m和n前缀这样的条件下能否匹配上，初始化很难想，如果前面几个都是\*那初始化也要加上，匹配*的时候也有一点点难理解

```java
public boolean isMatch(String s, String p) {
    boolean dp[][]=new boolean[s.length()+1][p.length()+1];
    dp[0][0]=true;
    for(int i=1;i<=p.length();i++)
        if(p.charAt(i-1)=='*')
            dp[0][i]=dp[0][i-1];
    for (int i=1;i<=s.length();++i) {
        for(int j=1;j<=p.length();j++)
        {
            if(p.charAt(j-1)==s.charAt(i-1)||p.charAt(j-1)=='?')
                dp[i][j]=dp[i-1][j-1];
            else if(p.charAt(j-1)=='*')
                dp[i][j]=dp[i][j-1]||dp[i-1][j];
        }
    }
    return dp[s.length()][p.length()];
}
```

[84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

双指针解法加上单调栈解法，非常抽象，双指针解法更快，但是用了动态规划的思想

有两个注意的点，第一个是在跳跃的时候，找下一个用的是大于等于和小于等于，第二个，初始化的时候，左侧初始值是负一，右侧是n，存的是下标而不是高度，跳跃式更新，动态规划比单调栈还快。

接雨水也是类似的思路，找到每一个点左边最大的高度下标和右边最大高度下标，取两个高度最小值再减去该位置的高度，就是在这一个位置的装雨水的量

插一句接雨水，初始化有讲究

![1749906356980](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749906356980.png)

```java
public int largestRectangleArea(int[] heights) {
    if(heights.length==1) return heights[0];
    int left[]=new int[heights.length];
    int right[]=new int[heights.length];
    left[0]=-1;right[heights.length-1]=heights.length;
    for(int i=1;i<heights.length;++i)
    {
        int t=i-1;
        while(t>=0&&heights[t]>=heights[i]) t=left[t];
        left[i]=t;
    }
    for(int i=heights.length-2;i>=0;--i)
    {
        int t=i+1;
        while(t<heights.length&&heights[t]>=heights[i]) t=right[t];
        right[i]=t;
    }
    int max=0;
    for(int i=0;i<heights.length;++i)
        max=Math.max(max,(right[i]-left[i]-1)*heights[i]);
    return max;
}
```

[85. 最大矩形](https://leetcode.cn/problems/maximal-rectangle/)

在会了84题的基础上，以每行为界，构造一个全新矩形，以第i行为底，每一列构造一个最大矩形的问题遍历就可以了，注意它是字符零而不是整数零。

```java
public int maximalRectangle(char[][] matrix) {
    int heights[]=new int[matrix[0].length];
    int max=0;
    for(int i=0;i<matrix.length;i++)
    {
        for(int j=0;j<matrix[0].length;j++)
        {
            if(matrix[i][j]=='0') heights[j]=0;
            else ++heights[j];
        }
        max=Math.max(max,largestRectangleArea(heights));
    }
    return max;
}
```

单调栈写法

```java
public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        int result = 0;
        Stack<Integer> stack = new Stack<>();
        // 在数组头部和尾部分别添加 0，方便处理边界情况
        int[] newHeights = new int[heights.length + 2];
        newHeights[0] = 0;
        newHeights[newHeights.length - 1] = 0;
        System.arraycopy(heights, 0, newHeights, 1, heights.length);
        stack.push(0); // 初始时栈中放入第一个元素的索引 0
        for (int i = 1; i < newHeights.length; i++) {
            /*
            if (newHeights[i] > newHeights[stack.peek()]) {
                // 情况一：当前高度大于栈顶高度，直接入栈
                stack.push(i);
            } else if (newHeights[i] == newHeights[stack.peek()]) {
                // 情况二：当前高度等于栈顶高度，可以弹出旧的再入栈（也可以不处理）
                stack.pop();
                stack.push(i);
            } else {
                // 情况三：当前高度小于栈顶高度，需要计算面积
                while (!stack.isEmpty() && newHeights[i] < newHeights[stack.peek()]) {
                    int mid = stack.pop(); // 弹出栈顶元素，作为矩形的高度
                    if (!stack.isEmpty()) {
                        int left = stack.peek(); // 左边界是新的栈顶
                        int right = i;          // 右边界是当前索引
                        int width = right - left - 1;
                        int height = newHeights[mid];
                        result = Math.max(result, width * height);
                    }
                }
                stack.push(i); // 当前索引入栈
            }
            */
            //可以直接简化成这样
            while (!stack.isEmpty() && newHeights[i] < newHeights[stack.peek()]) {
                    int mid = stack.pop(); // 弹出栈顶元素，作为矩形的高度
                    if (!stack.isEmpty()) {
                        int left = stack.peek(); // 左边界是新的栈顶
                        int right = i;          // 右边界是当前索引
                        int width = right - left - 1;
                        int height = newHeights[mid];
                        result = Math.max(result, width * height);
                    }
                }
                stack.push(i);
        }
        return result;
    }
```

[400. 第 N 位数字](https://leetcode.cn/problems/nth-digit/)

数学推导找规律的思想，比较难想，就是一位的数字有1\*9 两位的数字2\*90 每占据一位就有count*k个位置被用掉，然后就是求num，它等于now+(n-1)/k    而得到的数字字符是charAt((n-1)%k)-'0'  每一个都需要一定的理解

```java
public int findNthDigit(int n) {
        int now=1,k=1;
        long count=9;
        while(n>count*k)
        {
            n-=k*count;
            now*=10;
            count*=10;
            ++k;
        }
        long num=now+(n-1)/k;
        return String.valueOf(num).charAt((n-1)%k)-'0';
    }
```

[134. 加油站](https://leetcode.cn/problems/gas-station/)

贪心算法，首先如果所有的油比消耗的油多，直接返回-1就好，如果当前消耗的油比开始得到的油多，res=i+1，说明从0到i都不是起始点，然后当前now归零

```java
public int canCompleteCircuit(int[] gas, int[] cost) {
        int costs=0,gases=0;
        for(int g:gas)
            gases+=g;
        for(int c:cost)
            costs+=c;
        if(costs>gases) return -1;
        int now=0,res=0;
        for(int i=0;i<gas.length;i++)
        {
            now+=gas[i]-cost[i];
            if(now<0){
                now=0;
                res=i+1;
            }
        }
        return res;
    }
```

### 三十六进制字符串相加

![1749971489417](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749971489417.png)

```java
int toint(char c)
    {
        if(Character.isDigit(c))
            return c-'0';
        else return c-'a'+10;
    }

    char tochar(int c)
    {
        if(c<10)
            return (char)('0'+c);
        else return (char)(c-10+'a');
    }
    String addStrings(String num1, String num2)
    {
        StringBuilder sb=new StringBuilder();
        int idx1=num1.length()-1,idx2=num2.length()-1;
        int carry=0;
        while(idx1>=0||idx2>=0||carry!=0)
        {
            int sum=0;
            if(idx1>=0)
                sum+=toint(num1.charAt(idx1--));
            if(idx2>=0)
                sum+=toint(num2.charAt(idx2--));
            sum+=carry;
            carry=sum/36;
            sum%=36;
            sb.append(tochar(sum));
        }
        return sb.reverse().toString();
    }
```

[395. 至少有 K 个重复字符的最长子串](https://leetcode.cn/problems/longest-substring-with-at-least-k-repeating-characters/)

两种方法，分治法以及滑动窗口

滑动窗口，维护两个变量，分别是达标的字符数valid以及不同字符的总数unique

![1749975376888](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749975376888.png)

```java
public int longestSubstring(String s, int k) {
        int res = 0;
        for (int t = 1; t <= 26; t++) {
            int[] cnt = new int[26];
            int unique = 0, valid = 0; // 当前窗口的字符种类数和满足条件的字符数
            int left = 0;
            for (int right = 0; right < s.length(); right++) {
                int c = s.charAt(right) - 'a';
                if (cnt[c] == 0) unique++;
                cnt[c]++;
                if (cnt[c] == k) valid++;
                while (unique > t) { // 窗口内字符种类数超过 t
                    int d = s.charAt(left++) - 'a';
                    if (cnt[d] == k) valid--;
                    cnt[d]--;
                    if (cnt[d] == 0) unique--;
                }
                if (unique == t && valid == t) {
                    res = Math.max(res, right - left + 1);
                }
            }
        }
        return res;
    }
```

![1749976960025](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1749976960025.png)

```java
public int longestSubstring(String s, int k) {
    // 基本情况：如果字符串为空或长度小于k，直接返回0
    if (s == null || s.length() < k) {
        return 0;
    }
    // 统计当前字符串中每个字符的频率
    int[] freq = new int[26];
    for (char ch : s.toCharArray()) {
        freq[ch - 'a']++;
    }
    // 遍历字符串，寻找第一个不满足条件的字符（频率 < k）
    for (int i = 0; i < s.length(); i++) {
        char ch = s.charAt(i);
        if (freq[ch - 'a'] < k) {
            // 找到分割点后，跳过所有连续不满足条件的字符
            int j = i + 1;
            while (j < s.length() && freq[s.charAt(j) - 'a'] < k) {
                j++;
            }
            // 递归处理左子串（从开头到分割点前）和右子串（跳过不满足条件的字符后）
            int left = longestSubstring(s.substring(0, i), k);
            int right = longestSubstring(s.substring(j), k);
            // 返回左右子串中的最大值
            return Math.max(left, right);
        }
    }
    // 如果所有字符的频率都 >= k，整个字符串就是合法子串
    return s.length();
}
```

[1004. 最大连续1的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/)

![1750043828659](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1750043828659.png)

```java
public int longestOnes(int[] nums, int k) {
        int zero=0,left=0,right=0,res=0;
        while(right<nums.length)
        {
            if(nums[right]==0)
                ++zero;
            while(zero>k)
            {
                if(nums[left++]==0)
                    --zero;
            }
            res=Math.max(res,right-left+1);
            ++right;
        }
        return res;
    }
```

[97. 交错字符串](https://leetcode.cn/problems/interleaving-string/)

初始化有点难搞，最开始的条件别忘了，递推方程含义是要么匹配S1，要么匹配S2

```java
public boolean isInterleave(String s1, String s2, String s3) {
        if(s1.length()+s2.length()!=s3.length()) return false;
        boolean dp[][]=new boolean[s1.length()+1][s2.length()+1];
        dp[0][0]=true;
        for(int i=1;i<=s1.length();++i)
            if(s3.charAt(i-1)==s1.charAt(i-1))
                dp[i][0]=dp[i-1][0];
        for(int i=1;i<=s2.length();++i)
            if(s3.charAt(i-1)==s2.charAt(i-1))
                dp[0][i]=dp[0][i-1];
        for(int i=1;i<=s1.length();++i)
            for(int j=1;j<=s2.length();++j)
                dp[i][j]=(dp[i-1][j]&&s1.charAt(i-1)==s3.charAt(i+j-1))||
                        (dp[i][j-1]&&s2.charAt(j-1)==s3.charAt(i+j-1));
        return dp[s1.length()][s2.length()];
    }
```

#### **阿拉伯数字转中文**

挺麻烦的，看看吧就

```java
public String num2cn(int n) {
    // 特殊情况：直接处理0
    if (n == 0) {
        return "零";
    }
    // 数字和单位的映射表
    String[] digits = {"零", "一", "二", "三", "四", "五", "六", "七", "八", "九"};
    String[] units = {"", "十", "百", "千"};          // 小单位（个、十、百、千）
    String[] bigUnits = {"", "万", "亿"};             // 大单位（万、亿）
    StringBuilder result = new StringBuilder();      // 存储最终结果
    boolean isNegative = false;                      // 标记是否为负数
    // 处理负数：加上“负”字，并转成正数处理
    if (n < 0) {
        isNegative = true;
        n = -n;
    }
    int unitPos = 0;         // 记录当前大单位的位置（0=无单位，1=万，2=亿）
    boolean needZero = false; // 标记是否需要补“零”（用于处理连续的零）
    // 分段处理：每4位为一组（个、十、百、千）
    while (n > 0) {
        int segment = n % 10000; // 取出当前最低的4位
        if (segment > 0) {
            // 如果需要补零（例如：10001 → 一万零一）
            if (needZero) {
                result.insert(0, "零");
                needZero = false;
            }
            // 处理当前4位数字
            StringBuilder segmentStr = new StringBuilder();
            for (int i = 0; i < 4; i++) {
                int digit = segment % 10; // 取出当前最低位
                segment /= 10;           // 移除已处理的最低位
                if (digit != 0) {
                    // 插入单位和数字（例如：3 + 百 → 三百）
                    segmentStr.insert(0, units[i]);
                    segmentStr.insert(0, digits[digit]);
                } else if (i > 0 && segment % 10 != 0) {
                    // 处理中间的零（例如：101 → 一百零一）
                    segmentStr.insert(0, "零");
                }
            }
            // 加上大单位（万/亿）
            segmentStr.append(bigUnits[unitPos]);
            result.insert(0, segmentStr);
        } else {
            // 当前4位全为零，标记需要补零（例如：100000001 → 一亿零一）
            needZero = true;
        }
        n /= 10000;    // 移除已处理的4位
        unitPos++;     // 移动到下一个大单位
    }
    // 处理负数
    if (isNegative) {
        result.insert(0, "负");
    }
    // 优化“一十”开头的表达（习惯上省略“一”）
    if (result.toString().startsWith("一十")) {
        result.deleteCharAt(0); // 删除开头的“一”
    }
    return result.toString();
}
```

[679. 24 点游戏](https://leetcode.cn/problems/24-game/)

纯递归，没有简单解法

每一次做一次运算然后递归进去，临界条件是只剩下一个数字了，每次挑选集合中的两个数字运算一下。

```java
public boolean judgePoint24(int[] cards) {
        List<Double> nums=new ArrayList<>();
        for(int card:cards)
            nums.add((double) card);
        return dfs(nums);
    }

    boolean dfs(List<Double> nums)
    {
        if(nums.size()==1)
            return Math.abs(nums.get(0)-24)<0.001;
        for(int i=0;i<nums.size();i++)
            for(int j=0;j<nums.size();++j)
            {
                if(i==j)continue;
                ArrayList<Double> nextnums=new ArrayList<>();
                for(int k=0;k<nums.size();++k)
                    if(k!=i&&k!=j)
                        nextnums.add(nums.get(k));
                nextnums.add(nums.get(i)+nums.get(j));
                if(dfs(nextnums)) return true;
                nextnums.remove(nextnums.size()-1);
                nextnums.add(nums.get(i)-nums.get(j));
                if(dfs(nextnums)) return true;
                nextnums.remove(nextnums.size()-1);
                nextnums.add(nums.get(i)*nums.get(j));
                if(dfs(nextnums)) return true;
                nextnums.remove(nextnums.size()-1);
                if(nums.get(j)!=0)
                {
                    nextnums.add(nums.get(i)/nums.get(j));
                    if(dfs(nextnums)) return true;
                    nextnums.remove(nextnums.size()-1);
                }
            }
        return false;
    }
```

[316. 去除重复字母](https://leetcode.cn/problems/remove-duplicate-letters/)

单调栈加贪心算法，有点难想

**用栈搭序列，遇小弹大（能补则弹），标记去重，剩余计数保不丢**

```java
public String removeDuplicateLetters(String s) {
    HashMap<Character, Integer> hm = new HashMap<>();
    for (int i = 0; i < s.length(); i++) {
        hm.put(s.charAt(i), i); // 记录字符的最后出现位置
    }
    boolean[] visited = new boolean[26]; // 标记字符是否在栈中
    Deque<Character> stack = new LinkedList<>();
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (visited[c - 'a']) continue; // 已存在于栈中则跳过
        // 当前字符比栈顶小，且栈顶字符后续还会出现，则弹出栈顶（贪心选择更小的字符）
        while (!stack.isEmpty() && c < stack.getLast() && hm.get(stack.getLast()) > i) {
            visited[stack.getLast() - 'a'] = false;
            stack.removeLast();
        }
        stack.addLast(c);
        visited[c - 'a'] = true;
    }
    // 构造结果字符串
    StringBuilder sb = new StringBuilder();
    for (char c : stack) {
        sb.append(c);
    }
    return sb.toString();
}
```

[264. 丑数 II](https://leetcode.cn/problems/ugly-number-ii/)

使用动态规划，然后慢慢移动3个指针，每一次找最小最近的丑数

```java
public int nthUglyNumber(int n) {
    // dp数组：dp[i]表示第i+1个丑数（因为数组从0开始）
    int[] dp = new int[n];
    dp[0] = 1; // 第一个丑数是1
    // 三个指针，分别表示当前2、3、5的倍数候选位置
    int idx2 = 0, idx3 = 0, idx5 = 0;
    int i = 1; // 当前填充dp的位置
    while (i < n) {
        // 计算下一个丑数，取三个指针指向的最小值
        int next2 = dp[idx2] * 2;
        int next3 = dp[idx3] * 3;
        int next5 = dp[idx5] * 5;
        dp[i] = Math.min(next2, Math.min(next3, next5));
        // 更新指针：如果当前丑数是由某个指针生成的，则该指针后移
        if (dp[i] == next2) idx2++;
        if (dp[i] == next3) idx3++;
        if (dp[i] == next5) idx5++;
        i++; // 填充下一个位置
    }
    return dp[n - 1]; // 返回第n个丑数
}
```

[673. 最长递增子序列的个数](https://leetcode.cn/problems/number-of-longest-increasing-subsequence/)

```java
public int findNumberOfLIS(int[] nums) {
    // dp[i] 表示以 nums[i] 结尾的最长递增子序列（LIS）的长度
    int[] dp = new int[nums.length];
    // cnt[i] 表示以 nums[i] 结尾的最长递增子序列的个数
    int[] cnt = new int[nums.length];
    // 初始化：每个元素本身至少是一个长度为1的LIS
    Arrays.fill(dp, 1);
    Arrays.fill(cnt, 1);
    // maxlen 记录全局最长LIS的长度
    int maxlen = 1;

    // 动态规划填充dp和cnt数组
    for (int i = 1; i < nums.length; ++i) {
        for (int j = 0; j < i; ++j) {
            // 只有当nums[i] > nums[j]时，才可能延长递增子序列
            if (nums[i] > nums[j]) {
                // 情况1：发现更长的递增子序列
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;  // 更新长度
                    cnt[i] = cnt[j];     // 重置数量（新的最长路径）
                }
                // 情况2：发现另一条相同长度的递增子序列
                else if (dp[i] == dp[j] + 1) {
                    cnt[i] += cnt[j];    // 累加数量（多条路径）
                }
            }
        }
        // 更新全局最长LIS长度
        maxlen = Math.max(maxlen, dp[i]);
    }

    // 统计所有长度为maxlen的LIS的总个数
    int res = 0;
    for (int i = 0; i < nums.length; ++i) {
        if (dp[i] == maxlen) {
            res += cnt[i];
        }
    }
    return res;
}
```

[494. 目标和](https://leetcode.cn/problems/target-sum/)

![1750260811317](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1750260811317.png)

```java
public int findTargetSumWays(int[] nums, int target) {
        int sum=0;
        for(int num:nums)
            sum+=num;
        if(sum<Math.abs(target)) return 0;
        if((sum+target)%2!=0) return 0;
        int t=(sum+target)/2;
        int dp[]=new int[t+1];
        dp[0]=1;
        for(int i=0;i<nums.length;++i)
            for(int j=t;j>=nums[i];--j)
            {
                dp[j]+=dp[j-nums[i]];
            }
        return dp[t];
    }
```

[670. 最大交换](https://leetcode.cn/problems/maximum-swap/)

是九就跳过，只要有一个数字在后面比它大，就更新maxidx，最后还需要判断这个max是和原来就一样么，一样就不换了。

```java
public int maximumSwap(int num) {
        char[] numArray = String.valueOf(num).toCharArray();
        for (int i = 0; i < numArray.length; i++) {
            // 跳过已经是最大的数字（如 '9'）
            if (numArray[i] == '9') continue;

            int maxIdx = i;
            // 找到右边最大的数字（如果有多个，选最右边的）
            for (int j = i + 1; j < numArray.length; j++) {
                if (numArray[j] >= numArray[maxIdx]) { // 注意是 >= 而非 >
                    maxIdx = j;
                }
            }

            // 如果右边有更大的数字，交换并返回
            if (maxIdx != i && numArray[maxIdx] != numArray[i]) {
                char temp = numArray[i];
                numArray[i] = numArray[maxIdx];
                numArray[maxIdx] = temp;
                return Integer.parseInt(new String(numArray));
            }
        }
        return num; // 已经是最大可能值
    }
```

[LCR 186. 文物朝代判断](https://leetcode.cn/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

```java
public boolean checkDynasty(int[] places) {
    // 初始化计数数组（骰子数字范围：1-13）
    int[] cnt = new int[14];  // cnt[1]~cnt[13] 分别统计数字1~13的出现次数
    int zero = 0;             // 统计万能牌（0）的数量
    int min = 14;             // 初始化最小值为14（比最大值13大1，便于后续更新）
    int max = 0;              // 初始化最大值为0（比最小值1小1，便于后续更新）

    // 遍历骰子数组
    for (int x : places) {
        if (x > 0) {
            // 非万能牌处理：
            if (++cnt[x] > 1) {
                // 如果数字x重复出现，无法构成顺子（顺子要求数字唯一）
                return false;
            }
            // 更新最小值和最大值
            min = Math.min(x, min);
            max = Math.max(x, max);
        } else {
            // 统计万能牌数量
            ++zero;
        }
    }

    // 判断是否可以构成顺子：
    // 核心逻辑：万能牌数量 >= 需要填补的缺口数量
    // 缺口数量 = (max - min + 1) - (5 - zero)
    //   - max - min + 1：当前非零数字覆盖的连续区间长度
    //   - 5 - zero：实际非零数字的数量
    //   - 缺口数量：连续区间长度与实际数字数量的差值
    return zero >= (max - min + 1 - (5 - zero));
}
```

[416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

两个注意的点

```
public boolean canPartition(int[] nums) {
        int sum=0;
        for(int num:nums)
            sum+=num;
        if(sum%2==1) return false;
        int n=sum/2;
        boolean dp[]=new boolean[n+1];//这里构造sum/2的
        dp[0]=true;
        for(int num:nums)
        {
            for(int i=n;i>=num;--i)
                dp[i]=dp[i-num]||dp[i];//这里要或一下，不然就覆盖了
        }
        return dp[n];
    }
```

[459. 重复的子字符串](https://leetcode.cn/problems/repeated-substring-pattern/)

自己想出来的非常聪明

```java
public boolean repeatedSubstringPattern(String s) {
        for(int i=s.length()/2;i>=1;--i)
        {
            if(s.length()%i!=0) continue;
            int now=0;
            String temp=s.substring(0,i);
            while(now<s.length())
                if(s.substring(now,now+i).equals(temp))
                    now+=i;
                else break;
            if(now==s.length())
                return true;
        }
        return false;
    }
```

[111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

![1750423158104](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1750423158104.png)

[443. 压缩字符串](https://leetcode.cn/problems/string-compression/)

写入的时候双指针，然后移动

```java
public int compress(char[] chars) {
        int writeIdx = 0; // 写入位置
        int readIdx = 0;  // 读取位置
        while (readIdx < chars.length) {
            char currentChar = chars[readIdx];
            int count = 0;
            // 统计连续相同字符的数量
            while (readIdx < chars.length && chars[readIdx] == currentChar) {
                readIdx++;
                count++;
            }
            // 写入字符
            chars[writeIdx++] = currentChar;
            // 写入计数（如果计数>1）
            if (count > 1) {
                // 将计数转为字符数组（例如 12 -> ['1', '2']）
                char[] countChars = Integer.toString(count).toCharArray();
                for (char c : countChars) {
                    chars[writeIdx++] = c;
                }
            }
        }
        return writeIdx; // 返回压缩后的长度
    }
```

[168. Excel 表列名称](https://leetcode.cn/problems/excel-sheet-column-title/)

26进制

注意每次循环之前要-1，这样可以映射到0-25

```java
public String convertToTitle(int columnNumber) {
        StringBuilder result = new StringBuilder();
        while (columnNumber > 0) {
            // 余数在0到25之间，对应A到Z
            columnNumber--; // 使其从0到25（避免直接映射）
            result.append((char) ('A' + columnNumber % 26)); // 获得对应的字符
            columnNumber /= 26; // 继续除以26，向高位进位
        }
        return result.reverse().toString(); // 字符串反转
    }
```

![1750425818439](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1750425818439.png)

[343. 整数拆分](https://leetcode.cn/problems/integer-break/)=

```java
		dp[1]=dp[2]=1;
        for(int i=3;i<=n;++i)
        {
            for(int j=1;j<=i/2;++j)
                dp[i]=Math.max(dp[i],Math.max(dp[i-j]*j,j*(i-j)));
            //注意递推公式，一个是拆成两半，一个拆成当前数字加上两个及以上的i-j
        }
        return dp[n];
```

[37. 解数独](https://leetcode.cn/problems/sudoku-solver/)

一个是递归逻辑，一个是check查九宫格的时候

```java
	boolean travel(char[][] board,int x,int y)
    {
        if(x==9) return true;
        if(y==9) return travel(board,x+1,0);
        if(board[x][y]!='.')return travel(board,x,y+1);
        for(char i='1';i<='9';++i)
        {
            if(check(board,x,y,i)) {
                board[x][y] = i;
                if(travel(board,x,y+1))//注意是y+1不是x+1
                    return true;
                board[x][y] = '.';
            }
        }
        return false;
    }
    
    
    for(int i=x/3*3;i<x/3*3+3;++i)
        {
            for(int j=y/3*3;j<y/3*3+3;++j)
            {
                if(i==x&&j==y)continue;
                if(board[i][j]==now) return false;
            }
        }
```

[567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)

```java
public int nextGreaterElement(int n) {
    char[] num = String.valueOf(n).toCharArray();
    int i = num.length - 2;
    // 1. 从右向左找到第一个降序的位置 i
    while (i >= 0 && num[i] >= num[i + 1]) {
        i--;
    }
    if (i < 0) return -1; // 已经是最大排列

    // 2. 在 i 右侧找到比 num[i] 大的最小字符 j
    int j = num.length - 1;
    while (j >= 0 && num[j] <= num[i]) {
        j--;
    }

    // 3. 交换 i 和 j
    swap(num, i, j);

    // 4. 将 i 右侧的字符升序排列（保证最小）
    Arrays.sort(num, i + 1, num.length);

    // 5. 解析为整数，处理溢出
    try {
        long result = Long.parseLong(new String(num));
        return result > Integer.MAX_VALUE ? -1 : (int) result;
    } catch (NumberFormatException e) {
        return -1;
    }
}

private void swap(char[] num, int i, int j) {
    char temp = num[i];
    num[i] = num[j];
    num[j] = temp;
}
```

[503. 下一个更大元素 II](https://leetcode.cn/problems/next-greater-element-ii/)

单调栈两次加入

```java
public int[] nextGreaterElements(int[] nums) {
        Deque<Integer> dq=new LinkedList<>();
        int res[]=new int[nums.length];
        Arrays.fill(res,-1);
        for(int i=0;i<nums.length;++i)
        {
            while(!dq.isEmpty()&&nums[dq.getLast()]<nums[i])
                res[dq.removeLast()]=nums[i];
            dq.addLast(i);
        }
        for(int i=0;i<nums.length;++i)
        {
            while(!dq.isEmpty()&&nums[dq.getLast()]<nums[i])
                res[dq.removeLast()]=nums[i];
            dq.addLast(i);
        }
        return res;
    }
```

[556. 下一个更大元素 III](https://leetcode.cn/problems/next-greater-element-iii/)

数学单调性的运用

![1764989155410](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1764989155410.png)

```java
    public int nextGreaterElement(int n) {
        char[] num = String.valueOf(n).toCharArray();
        int i = num.length - 2;
        // 1. 从右向左找到第一个降序的位置 i
        while (i >= 0 && num[i] >= num[i + 1]) {
            i--;
        }
        if (i < 0) return -1; // 已经是最大排列

        // 2. 在 i 右侧找到比 num[i] 大的最小字符 j
        int j = num.length - 1;
        while (j >= 0 && num[j] <= num[i]) {
            j--;
        }
        // 3. 交换 i 和 j
        swap(num, i, j);
        // 4. 将 i 右侧的字符升序排列（保证最小）
        Arrays.sort(num, i + 1, num.length);
        // 5. 解析为整数，处理溢出
        try {
            long result = Long.parseLong(new String(num));
            return result > Integer.MAX_VALUE ? -1 : (int) result;
        } catch (NumberFormatException e) {
            return -1;
        }
    }

    private void swap(char[] num, int i, int j) {
        char temp = num[i];
        num[i] = num[j];
        num[j] = temp;
    }
```

[378. 有序矩阵中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/)

```java
public int kthSmallest(int[][] matrix, int k) {
    // 初始化二分查找的左右边界：最小值为矩阵左上角，最大值为矩阵右下角
    int left = matrix[0][0], right = matrix[matrix.length - 1][matrix.length - 1];
    // 二分查找：寻找第 k 小的元素
    while (left < right) {
        // 防止整数溢出，计算中间值
        int mid = left + (right - left) / 2;
        // 统计矩阵中 <= mid 的元素个数
        int cnt = countLessOrEqual(matrix, mid);
        if (cnt < k) {
            // 如果 <= mid 的元素个数不足 k，说明第 k 小的元素在 mid 右侧
            left = mid + 1;
        } else {
            // 否则，第 k 小的元素在 mid 或其左侧
            right = mid;
        }
    }
    
    // 终止时 left == right，且一定是矩阵中的某个元素
    return right;
}

/**
 * 统计矩阵中 <= mid 的元素个数
 * 利用矩阵的行列有序性，从左下角开始遍历：
 * 1. 如果 matrix[i][j] <= mid，则当前列及其上方所有元素均 <= mid，计数后右移
 * 2. 否则，上移
 */
private int countLessOrEqual(int[][] matrix, int mid) {
    int n = matrix.length;
    int i = n - 1, j = 0; // 从左下角开始
    int cnt = 0;
    
    while (i >= 0 && j < n) {
        if (matrix[i][j] <= mid) {
            cnt += i + 1; // 当前列及其上方共 i+1 个元素 <= mid
            j++;          // 右移
        } else {
            i--;          // 上移
        }
    }
    
    return cnt;
}
```

[LCR 120. 寻找文件副本](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

交换法，我用的是相反数法，不好使，因为会有0的出现。。。相反数不变，失去了效果

```java
public int findRepeatDocument(int[] nums) {
        for(int i = 0; i < nums.length; i ++) {
            while(nums[i] != i) {
                // 待交换的nums[i]和nums[nums[i]]重复
                if(nums[nums[i]] == nums[i]) return nums[i];
                int t = nums[nums[i]];
                nums[nums[i]] = nums[i];
                nums[i] = t;
            }
        }
        return -1;
    }
```

[LCR 164. 破解闯关密码](https://leetcode.cn/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

```java
public String crackPassword(int[] password) {
        // 1. 将整数数组转换为字符串数组
        String[] strs = new String[password.length];
        for (int i = 0; i < password.length; i++) {
            strs[i] = String.valueOf(password[i]);
        }
        // 2. 自定义排序：比较 a+b 和 b+a
        Arrays.sort(strs, (a, b) -> (a + b).compareTo(b + a));
        // 3. 拼接所有字符串
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            sb.append(s);
        }
        return sb.toString();
    }
```

[260. 只出现一次的数字 III](https://leetcode.cn/problems/single-number-iii/)

```java
public int[] singleNumber(int[] nums) {
        int xor = 0;
        for (int num : nums) {
            xor ^= num;
        }

        int diff = xor & (-xor); // 提取最低位的 1
        int a = 0, b = 0;
        for (int num : nums) {
            if ((num & diff) == 0) {
                a ^= num; // 属于第一组
            } else {
                b ^= num; // 属于第二组
            }
        }
        return new int[]{a, b};
    }
```

![1751071685109](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1751071685109.png)

![1751071806121](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1751071806121.png)

[18. 四数之和](https://leetcode.cn/problems/4sum/)

核心代码：

```java
while (left < right) {
                // 使用long防止整数溢出
                long sum = (long)nums[i] + nums[j] + nums[left] + nums[right];
                
                if (sum == target) {
                    res.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                    // 同时移动两个指针
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    left++;
                    right--;
                } else if (sum < target) {
                    left++;
                } else {
                    right--;
                }
            }
```

[LCR 153. 二叉树中和为目标值的路径](https://leetcode.cn/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

主要是边界条件必须要整清楚

```java
if(root==null) return;
        if(root.left==null&&root.right==null)
        {
            if(target==root.val)
            {
                path.add(root.val);
                res.add(new ArrayList<>(path));
                path.remove(path.size()-1);
            }
            return;
        }
```

[86. 分隔链表](https://leetcode.cn/problems/partition-list/)

核心思想是别想着就在这一个链表上做改动，我们新开两个头节点，一个存小于x的其他的存另一个，最后第二个末尾要置为空。

[1444. 切披萨的方案数](https://leetcode.cn/problems/number-of-ways-of-cutting-a-pizza/)

### 切披萨问题详解：1444. 切披萨的方案数

#### 问题描述

给定一个矩形披萨，用字符串数组 `pizza` 表示，其中：

- `'A'` 表示苹果
- `'.'` 表示空位

需要将披萨切成 `k` 块（进行 `k-1` 次切割），每次切割规则：

1. 只能水平或垂直切割
2. 切割后总是保留右下部分
3. **每块披萨必须至少包含一个苹果**

求所有切割方案的数目（结果需对 `10^9 + 7` 取模）。

------

### 核心思路：动态规划 + 前缀和优化

#### 1. 问题分析

- **切割特性**：每次切割后保留右下部分，意味着每次操作都是在当前披萨的左上角切割
- **关键要求**：每块披萨必须至少有一个苹果
- **目标**：计算所有满足条件的切割方案数

#### 2. 前缀和预处理

```java
// 创建 (rows+1) x (cols+1) 的前缀和数组 从0,0到i,j的苹果个数
int[][] pre = new int[rows+1][cols+1];
for (int i = 1; i <= rows; i++) {
    for (int j = 1; j <= cols; j++) {
        int apple = (pizza[i-1].charAt(j-1) == 'A') ? 1 : 0;
        pre[i][j] = pre[i-1][j] + pre[i][j-1] - pre[i-1][j-1] + apple;
    }
}
```

- **作用**：O(1) 时间计算任意子矩阵的苹果数量
- **公式**：子矩阵 `[r1, c1]` 到 `[r2, c2]` 的苹果数 =
   `pre[r2+1][c2+1] - pre[r1][c2+1] - pre[r2+1][c1] + pre[r1][c1]`

#### 3. 动态规划状态定义

```java
// dp[i][j][p]：从 (i,j) 到右下角的子矩阵，切割成 p 块的方案数
int[][][] dp = new int[rows][cols][k+1];
```

- **i, j**：当前子矩阵的左上角坐标
- **p**：需要切割成的块数

#### 4. 初始化（p=1）

```java
for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        int apples = pre[rows][cols] - pre[i][cols] - pre[rows][j] + pre[i][j];
        dp[i][j][1] = (apples > 0) ? 1 : 0;
    }
}
```

- 当只需要1块时，方案数为1（如果子矩阵有苹果）或0（无苹果）

#### 5. 状态转移（p ≥ 2）

```java
for (int p = 2; p <= k; p++) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            long count = 0;
            
            // 水平切割：枚举切割线 r0 (i < r0 < rows)
            for (int r0 = i+1; r0 < rows; r0++) {
                int discard = pre[r0][cols] - pre[i][cols] - pre[r0][j] + pre[i][j];
                if (discard > 0) {
                    count = (count + dp[r0][j][p-1]) % MOD;
                }
            }
            
            // 垂直切割：枚举切割线 c0 (j < c0 < cols)
            for (int c0 = j+1; c0 < cols; c0++) {
                int discard = pre[rows][c0] - pre[i][c0] - pre[rows][j] + pre[i][j];
                if (discard > 0) {
                    count = (count + dp[i][c0][p-1]) % MOD;
                }
            }
            
            dp[i][j][p] = (int) count;
        }
    }
}
```

- **水平切割**：在行 `r0` 处切割，丢弃上半部分，保留下半部分
- **垂直切割**：在列 `c0` 处切割，丢弃左半部分，保留右半部分
- **关键检查**：丢弃部分必须至少有一个苹果

#### 6. 返回结果

```
return dp[0][0][k];
```

- 表示从整个披萨（左上角 `(0,0)`）切割成 `k` 块的方案数

------

### 关键公式详

#### 1. 子矩阵苹果数计算

对于左上角 `(i,j)`，右下角 `(rows-1, cols-1)` 的子矩阵：

```
int apples = pre[rows][cols] - pre[i][cols] - pre[rows][j] + pre[i][j];
```

#### 2. 水平切割丢弃部分

切割线在 `r0` 行，丢弃部分为 `[i, j]` 到 `[r0-1, cols-1]`：

```
int discard = pre[r0][cols] - pre[i][cols] - pre[r0][j] + pre[i][j];
```

#### 3. 垂直切割丢弃部分

切割线在 `c0` 列，丢弃部分为 `[i, j]` 到 `[rows-1, c0-1]`：

```
int discard = pre[rows][c0] - pre[i][c0] - pre[rows][j] + pre[i][j];
```

#### 前缀和矩阵示例

对于示例披萨：

```
原始披萨：
行0: A . .
行1: A A A
行2: . . .

前缀和矩阵pre：
   [0] [1] [2] [3]
[0] 0   0   0   0
[1] 0   1   1   1
[2] 0   2   3   4
[3] 0   2   3   4
```

#### 4. 切割验证实例

**案例1**：从(0,0)开始，水平切割在行1

```
丢弃区域：行0
计算：
pre[1][3] - pre[0][3] - pre[1][0] + pre[0][0] = 1 - 0 - 0 + 0 = 1
（有1个苹果，可以切割）
```

**案例2**：从(0,0)开始，垂直切割在列1

```
丢弃区域：列0
计算：
pre[3][1] - pre[0][1] - pre[3][0] + pre[0][0] = 2 - 0 - 0 + 0 = 2
（有2个苹果，可以切割）
```

### 完整代码实现

```java
class Solution {
    private static final int MOD = 1000000007;

    public int ways(String[] pizza, int k) {
        int rows = pizza.length;
        int cols = pizza[0].length();
        
        // 1. 前缀和预处理
        int[][] pre = new int[rows+1][cols+1];
        for (int i = 1; i <= rows; i++) {
            for (int j = 1; j <= cols; j++) {
                int apple = (pizza[i-1].charAt(j-1) == 'A') ? 1 : 0;
                pre[i][j] = pre[i-1][j] + pre[i][j-1] - pre[i-1][j-1] + apple;
            }
        }
        
        // 2. 初始化DP数组
        int[][][] dp = new int[rows][cols][k+1];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                //剩下的右下角那一块
                int apples = pre[rows][cols] - pre[i][cols] - pre[rows][j] + pre[i][j];
                dp[i][j][1] = (apples > 0) ? 1 : 0;
            }
        }
        
        // 3. 动态规划状态转移 从i，j右下角去切
        for (int p = 2; p <= k; p++) {
            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < cols; j++) {
                    long count = 0;
                    
                    // 水平切割
                    for (int r0 = i+1; r0 < rows; r0++) {
                        int discard = pre[r0][cols] - pre[i][cols] - pre[r0][j] + pre[i][j];
                        if (discard > 0) {
                            count = (count + dp[r0][j][p-1]) % MOD;
                        }
                    }
                    
                    // 垂直切割
                    for (int c0 = j+1; c0 < cols; c0++) {
                        int discard = pre[rows][c0] - pre[i][c0] - pre[rows][j] + pre[i][j];
                        if (discard > 0) {
                            count = (count + dp[i][c0][p-1]) % MOD;
                        }
                    }
                    
                    dp[i][j][p] = (int) count;
                }
            }
        }
        
        return dp[0][0][k];
    }
}
```

优化方法，难的掉眼泪

```java
static private final int mod = 1000000007;

public int ways(String[] pizza, int k) {
    int m = pizza.length, n = pizza[0].length();
    // 前缀和数组：s[i][j]表示从(i,j)到右下角的苹果总数
    int[][] s = new int[m + 1][n + 1];
    // DP状态数组：f[i][j]表示从(i,j)开始切割的方案数
    int[][] f = new int[m + 1][n + 1];
    
    // 1. 预处理阶段：逆向计算前缀和
    for (int i = m - 1; i >= 0; i--) {
        for (int j = n - 1; j >= 0; j--) {
            // 容斥原理计算二维前缀和
            s[i][j] = s[i + 1][j] + s[i][j + 1] - s[i + 1][j + 1] 
                     + (pizza[i].charAt(j) == 'A' ? 1 : 0);
            
            // 初始化：当前区域有苹果则初始方案数为1（无需切割的情况）
            if (s[i][j] > 0) {
                f[i][j] = 1;
            }
        }
    }
    
    // 2. 动态规划：k-1次切割迭代
    for (; k > 1; k--) {
        int[] cs = new int[n];  // 列累加器：cs[j]存储当前列下方所有方案数之和
        for (int i = m - 1; i >= 0; i--) {
            int rs = 0;  // 行累加器：存储当前行右侧所有方案数之和
            
            /* 关键优化：逆向遍历
               从右下到左上的顺序确保：
               1. 计算(i,j)时，rs已包含右侧(j+1..n-1)的方案
               2. cs[j]已包含下方(i+1..m-1)的方案 */
            for (int j = n - 1; j >= 0; j--) {
                int t = f[i][j];  // 保存上层状态
                
                // 3. 智能状态转移
                if (s[i][j] == s[i + 1][j]) {  
                    // 情况1：第i行没有苹果 → 直接继承正下方状态
                    f[i][j] = f[i + 1][j];
                } else if (s[i][j] == s[i][j + 1]) { 
                    // 情况2：第j列没有苹果 → 直接继承正右方状态
                    f[i][j] = f[i][j + 1];
                } else {  
                    // 情况3：当前行列有苹果 → 合并所有切割方案
                    // 总方案 = 右侧垂直切割方案(rs) + 下方水平切割方案(cs[j])
                    f[i][j] = (rs + cs[j]) % mod;
                }
                
                // 4. 更新累加器
                rs = (rs + t) % mod;      // 将当前点加入行累加器
                cs[j] = (cs[j] + t) % mod; // 将当前点加入列累加器
            }
        }
    }
    return f[0][0];  // 返回从左上角开始的方案数
}
```

[LCR 128. 库存管理 I](https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

快速选择算法找到数组中最大或者最小的那个数，就是quickselect的时候边界是使用0还是len-1的问题

一个比较重要的是quickselect方法，一个是partition的i<=j是要加等号的，以便j回到0的位置。

```java
public int inventoryManagement(int[] stock) {
        return quickselect(stock,0,stock.length-1);
    }
    int quickselect(int[] stock, int left, int right) {
        if (left == right) {
            return stock[left];
        }
        int pivotIndex = partition(stock, left, right);
        if (pivotIndex == 0) {
            return stock[0];
        }
        return quickselect(stock, left, pivotIndex - 1);
    }

    int partition(int[] stock,int left,int right)
    {
        int p=stock[left];
        int i=left+1,j=right;
        while(i<=j)
        {
            while(i<=j&&stock[i]<p) ++i;
            while(i<=j&&stock[j]>=p) --j;
            if(i<=j)
            {
                swap(stock,i,j);
                ++i;
                --j;//这里一定要移动指针，不然j无法回到0的位置
            }
        }
        swap(stock,left,j);
        return j;
    }
    void swap(int[] stock,int i,int j)
    {
        int temp=stock[i];
        stock[i]=stock[j];
        stock[j]=temp;
    }
```

[1044. 最长重复子串](https://leetcode.cn/problems/longest-duplicate-substring/)

玄学，二分查找加滚动哈希

```java
public class Solution {
    // 哈希基数，通常取大于字符集大小的质数
    private static final long BASE = 26;
    // 大质数，用于取模避免哈希值溢出
    private static final long MOD = (long)1e9 + 7;

    public String longestDupSubstring(String s) {
        int n = s.length();
        int left = 1;       // 最短可能的重复子串长度
        int right = n - 1;  // 最长可能的重复子串长度
        String result = ""; // 存储结果

        // 二分查找可能的最长重复子串长度
        while (left <= right) {
            int mid = left + (right - left) / 2; // 中间长度
            String dup = findDuplicate(s, mid);  // 检查是否存在长度为 mid 的重复子串
            if (dup != null) {
                result = dup;    // 如果存在，更新结果并尝试更长的长度
                left = mid + 1;
            } else {
                right = mid - 1; // 否则尝试更短的长度
            }
        }
        return result;
    }

    // 检查字符串 s 中是否存在长度为 len 的重复子串
    private String findDuplicate(String s, int len) {
        Set<Long> seen = new HashSet<>(); // 记录已经出现的子串哈希值
        long hash = 0;                    // 当前子串的哈希值
        long power = 1;                   // BASE^len % MOD，用于滚动哈希计算

        // 预计算 power = BASE^len % MOD
        for (int i = 0; i < len; i++) {
            power = (power * BASE) % MOD;
        }

        // 计算初始子串（前 len 个字符）的哈希值
        for (int i = 0; i < len; i++) {
            hash = (hash * BASE + s.charAt(i)) % MOD;
        }
        seen.add(hash); // 记录初始子串的哈希值

        // 滑动窗口，计算后续子串的哈希值
        for (int i = len; i < s.length(); i++) {
            // 移除最左边字符的哈希贡献，加入新字符的哈希贡献
            hash = (hash * BASE - s.charAt(i - len) * power % MOD + MOD) % MOD;
            hash = (hash + s.charAt(i)) % MOD;

            // 如果哈希值已存在，检查是否真的存在重复子串
            if (seen.contains(hash)) {
                String candidate = s.substring(i - len + 1, i + 1); // 当前子串
                // 检查 candidate 是否在原字符串中之前出现过
                if (s.indexOf(candidate) != i - len + 1) {
                    return candidate; // 如果出现过，返回该子串
                }
            }
            seen.add(hash); // 记录当前子串的哈希值
        }
        return null; // 没有找到重复子串
    }
}
```

[380. O(1) 时间插入、删除和获取随机元素](https://leetcode.cn/problems/insert-delete-getrandom-o1/)

核心数据结构

![1751286120344](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1751286120344.png)

[410. 分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

二分查找加贪心策略

```java
public int splitArray(int[] nums, int k) {
        /* 方法思路：
           1. 使用二分查找确定可能的最小最大和
           2. 用贪心算法验证某个最大和是否可行
           3. 通过调整二分边界找到最优解
           
           时间复杂度：O(n log S)，其中n是数组长度，S是数组总和
           空间复杂度：O(1)
        */
        
        // 1. 初始化二分查找边界
        long left = 0;      // 最小可能值：数组中的最大值（至少需要容纳最大的单个元素）
        long right = 0;     // 最大可能值：数组总和（整个数组作为一段）
        
        for (int num : nums) {
            left = Math.max(left, num);  // 更新左边界为数组最大值
            right += num;                // 计算数组总和作为右边界
        }

        // 2. 二分查找框架
        while (left < right) {
            long mid = left + (right - left) / 2;  // 取中间值作为当前尝试的最大和
            
            // 验证是否可以用k段实现最大和为mid
            if (canSplit(nums, k, mid)) {
                right = mid;    // 可行，尝试更小的最大和
            } else {
                left = mid + 1; // 不可行，需要更大的最大和
            }
        }
        
        return (int) left;  // 最终收敛到最小可能的最大和
    }

    /**
     * 贪心验证函数：判断是否可以将数组分成最多k段，每段和不超过maxSum
     * @param nums 输入数组
     * @param k 最大分段数
     * @param maxSum 当前尝试的最大和
     * @return 是否可行
     */
    private boolean canSplit(int[] nums, int k, long maxSum) {
        int count = 1;       // 当前分段数（至少为1）
        long currentSum = 0; // 当前段的累加和
        
        for (int num : nums) {
            // 尝试将当前数字加入当前段
            if (currentSum + num > maxSum) {
                count++;        // 超过maxSum，需要开启新段
                currentSum = num; // 新段的起始值为当前数字
                
                // 如果分段数已经超过k，直接返回失败
                if (count > k) {
                    return false;
                }
            } else {
                currentSum += num;  // 可以加入当前段
            }
        }
        return true;  // 成功用不超过k段实现目标
    }
```

[6. Z 字形变换](https://leetcode.cn/problems/zigzag-conversion/)

有两个注意的点，一个是如果只有一行可以直接返回，然后sb初始化不能用for(Stringbuilder s:sbs)循环要用下标去遍历

```java
if (numRows == 1 || s.length() <= numRows) {
            return s;
        }
        StringBuilder[] sbs=new StringBuilder[numRows];
        for(int i=0;i<numRows;++i)
            sbs[i]=new StringBuilder();
```

[622. 设计循环队列](https://leetcode.cn/problems/design-circular-queue/)

可以引入size变量用来判空和满，这样不会损失一个格子

然后队列头部是要消费的格子，tail是下一个要插入的格子，想清楚了要

```java
class MyCircularQueue {
    private int[] queue;
    private int head;  // 指向队列头部
    private int tail;  // 指向下一个插入位置
    private int size;  // 当前元素个数
    private final int capacity;  // 队列容量

    public MyCircularQueue(int k) {
        this.capacity = k;
        this.queue = new int[k];
        this.head = 0;
        this.tail = 0;
        this.size = 0;
    }

    public boolean enQueue(int value) {
        if (isFull()) {
            return false;
        }
        queue[tail] = value;
        tail = (tail + 1) % capacity;
        size++;
        return true;
    }

    public boolean deQueue() {
        if (isEmpty()) {
            return false;
        }
        head = (head + 1) % capacity;
        size--;
        return true;
    }

    public int Front() {
        if (isEmpty()) {
            return -1;
        }
        return queue[head];
    }

    public int Rear() {
        if (isEmpty()) {
            return -1;
        }
        // 计算尾部位置（tail-1可能为负，所以加capacity）
        int lastPos = (tail - 1 + capacity) % capacity;
        return queue[lastPos];
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public boolean isFull() {
        return size == capacity;
    }
}
```

[301. 删除无效的括号](https://leetcode.cn/problems/remove-invalid-parentheses/)

贪心加回溯加剪枝

```java
HashSet<String> hs = new HashSet<>(); // 结果集
    StringBuilder path = new StringBuilder(); // 当前路径
    public List<String> removeInvalidParentheses(String s) {
        // 计算多余的左右括号
        int leftRemove = 0, rightRemove = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                leftRemove++;
            } else if (c == ')') {
                if (leftRemove > 0) {
                    leftRemove--;
                } else {
                    rightRemove++;
                }
            }
        }
        // 开始回溯
        travel(0, s, 0, 0, leftRemove, rightRemove);
        return new ArrayList<>(hs);
    }
    void travel(int idx, String s, int left, int right, int leftRemove, int rightRemove) {
        // 递归结束条件
        if (idx == s.length()) {
            if (leftRemove == 0 && rightRemove == 0) {
                hs.add(path.toString()); // 将当前路径加入结果集
            }
            return;
        }
        char c = s.charAt(idx);
        // 处理多余的括号
        if (c == '(' && leftRemove > 0) {
            travel(idx + 1, s, left, right, leftRemove - 1, rightRemove); // 不保留当前左括号
        }
        if (c == ')' && rightRemove > 0) {
            travel(idx + 1, s, left, right, leftRemove, rightRemove - 1); // 不保留当前右括号
        }
        // 保留当前字符
        path.append(c);
        if (c != '(' && c != ')') {
            // 如果是普通字符，直接递归
            travel(idx + 1, s, left, right, leftRemove, rightRemove);
        } else if (c == '(') {
            // 保留左括号，递归
            travel(idx + 1, s, left + 1, right, leftRemove, rightRemove);
        } else if (c == ')' && right < left) {//这个地方比较坑
            // 保留右括号，递归
            travel(idx + 1, s, left, right + 1, leftRemove, rightRemove);
        }
        // 回溯
        path.deleteCharAt(path.length() - 1);
    }
```

[863. 二叉树中所有距离为 K 的结点](https://leetcode.cn/problems/all-nodes-distance-k-in-binary-tree/)

怎样从一个节点找到到其他节点的距离呢，这是一个好方法，建立节点映射父节点的hm，然后每次向三个方向BFS然后到达相应的距离之后全部存起来返回就可以了。

```java
	List<Integer> res=new ArrayList<>();
    HashMap<TreeNode,TreeNode> parentmap=new HashMap<>();
    HashSet<TreeNode> visited=new HashSet<>();
    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        buildparentmap(root,null);
        Deque<TreeNode> queue=new LinkedList<>();
        queue.add(target);
        while(!queue.isEmpty())
        {
            if(k==0)
            {
                for(TreeNode t:queue)
                    res.add(t.val);
                return res;
            }
            int size=queue.size();
            while(size-->0)
            {
                TreeNode temp=queue.poll();
                visited.add(temp);
                if(temp.left!=null&&!visited.contains(temp.left))
                    queue.add(temp.left);
                if(temp.right!=null&&!visited.contains(temp.right))
                    queue.add(temp.right);
                TreeNode parent=parentmap.get(temp);
                if(parent!=null&&!visited.contains(parent))
                    queue.add(parent);
            }
            --k;
        }
        return res;
    }

    void buildparentmap(TreeNode root,TreeNode parent)
    {
        if(root==null) return;
        parentmap.put(root,parent);
        buildparentmap(root.left,root);
        buildparentmap(root.right,root);
    }
```

[115. 不同的子序列](https://leetcode.cn/problems/distinct-subsequences/)

dp下标表示从i-1能匹配多少个到j-1下标的子序列

```java
public int numDistinct(String s, String t) {
        int m=s.length(),n=t.length();
        int dp[][]=new int[s.length()+1][t.length()+1];
        for(int i=0;i<=m;++i)
            dp[i][0]=1;
        for(int i=1;i<=m;++i)
        {
            for(int j=1;j<=n;++j)
            {
                if(s.charAt(i-1)==t.charAt(j-1))
                    dp[i][j]=dp[i-1][j-1]+dp[i-1][j];
                else dp[i][j]=dp[i-1][j];
            }
        }
        return dp[m][n];
    }
```

[257. 二叉树的所有路径](https://leetcode.cn/problems/binary-tree-paths/)

注意一下对于箭头的回溯，比较有意思，使用sb的setlength方法，先记录原先的值，后面再直接设定就行，这样做的原因是原来的数字可能不止一位，所以需要这样设置

```java
class Solution {
    List<String> res=new ArrayList<>();
    StringBuilder sb=new StringBuilder();
    public List<String> binaryTreePaths(TreeNode root) {
        if(root==null) return res;
        travel(root);
        return res;
    }

    void travel(TreeNode node) {
        // 保存当前长度用于回溯
        int len = sb.length();
        // 添加当前节点值
        if (len != 0) sb.append("->");
        sb.append(node.val);
        // 叶子节点：找到完整路径
        if (node.left == null && node.right == null) {
            res.add(sb.toString());
        } else {
            // 递归处理子节点
            if (node.left != null) travel(node.left);
            if (node.right != null) travel(node.right);
        }
        // 回溯：恢复到进入前的状态
        sb.setLength(len);
    }
}
```

[188. 买卖股票的最佳时机 IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

依然不是那么简单的，一维的话意思是遍历每一个天数，先用第0天进行初始化，之后遍历每一天，不过在遍历内层循环的时候是从1开始，因为dp[0]一直都是0，表示没有进行任何买卖

```java
public int maxProfit(int k, int[] prices) {
        int dp[]=new int[2*k+1];
        for(int i=0;i<=2*k;++i)
            if(i%2==0)
                dp[i]=0;
        else dp[i]=-prices[0];
        for(int i=1;i<prices.length;++i)
        {
            for(int j=1;j<=2*k;++j)
            {
                if(j%2==0)
                    dp[j]=Math.max(dp[j],dp[j-1]+prices[i]);
                else dp[j]=Math.max(dp[j],dp[j-1]-prices[i]);
            }
        }
        return dp[2*k];
    }
```

[LCR 152. 验证二叉搜索树的后序遍历序列](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

注意最后的judge的right要减一，排除掉当前节点递归

```java
public boolean verifyTreeOrder(int[] postorder) {
        return judge(postorder,0,postorder.length-1);
    }

    boolean judge(int[] postorder,int left,int right)
    {
        if(left>right) return true;
        int now=postorder[right];
        int ridx=right-1;
        while(ridx>=left&&postorder[ridx]>now) --ridx;
        for(int i=left;i<=ridx;++i)
            if(postorder[i]>now)
                return false;
        return judge(postorder, left, ridx)&&judge(postorder, ridx+1, right-1);
    }
```

[面试题 17.24. 最大子矩阵](https://leetcode.cn/problems/max-submatrix-lcci/)

列和压缩，kadane算法（最大子数组贪心），把它转化成了一个压缩饼干，从i到j行压缩的饼干然后找最大子矩阵

注意一下m是行数，n是列数。

```java
public int[] getMaxMatrix(int[][] matrix) {
    int n = matrix.length;         // 矩阵行数
    int m = matrix[0].length;      // 矩阵列数
    int maxSum = Integer.MIN_VALUE; // 记录全局最大和
    int currentSum;                // 当前子矩阵和
    int startCol;                  // 当前子矩阵起始列
    int[] result = new int[4];     // 存储结果坐标[i1,j1,i2,j2]
    int[] colSum;                  // 列和数组（压缩存储）
    
    // 1. 枚举所有可能的行组合（上边界i到下边界j）
    for (int i = 0; i < n; i++) {
        // 初始化列和数组（压缩i行到j行的列和）
        colSum = new int[m];
        for (int j = i; j < n; j++) {
            currentSum = 0;    // 重置当前子矩阵和
            startCol = 0;      // 重置起始列为0
            // 2. 列方向处理（将i-j行压缩为一维数组）
            for (int k = 0; k < m; k++) {
                // 累加当前列的i-j行元素和（动态计算列和）
                colSum[k] += matrix[j][k];
                // 3. 应用Kadane算法求最大子数组和
                currentSum += colSum[k];
                // 更新全局最大值和坐标
                if (currentSum > maxSum) {
                    maxSum = currentSum;
                    result[0] = i;    // 上边界行
                    result[1] = startCol; // 左边界列
                    result[2] = j;     // 下边界行
                    result[3] = k;     // 右边界列
                }
                // 当前和小于0时重置（Kadane算法核心）
                if (currentSum < 0) {
                    currentSum = 0;
                    startCol = k + 1;  // 新的子矩阵从下一列开始
                }
            }
        }
    }
    return result;
}
```

[438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

使用两个数组直接使用函数比较

```java
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> res = new ArrayList<>();
    if (s.length() < p.length() || p.length() == 0) return res;
    
    // 1. 初始化频率表
    int[] pCount = new int[26];
    int[] window = new int[26];
    for (char c : p.toCharArray()) {
        pCount[c - 'a']++;
    }
    
    // 2. 初始化滑动窗口
    for (int i = 0; i < p.length(); i++) {
        window[s.charAt(i) - 'a']++;
    }
    
    // 3. 初始匹配检查
    if (Arrays.equals(pCount, window)) {
        res.add(0);
    }
    
    // 4. 滑动窗口遍历
    for (int i = p.length(); i < s.length(); i++) {
        // 移除左边界的字符
        char outChar = s.charAt(i - p.length());
        window[outChar - 'a']--;
        
        // 添加右边界的字符
        char inChar = s.charAt(i);
        window[inChar - 'a']++;
        
        // 检查是否匹配
        if (Arrays.equals(pCount, window)) {
            res.add(i - p.length() + 1);
        }
    }
    
    return res;
}
```

[528. 按权重随机选择](https://leetcode.cn/problems/random-pick-with-weight/)

使用前缀和，最后一个前缀和就是总和了，然后总和拿一个随机数，看它的区间选择下标

快一点，前缀和数组加上二分去找，判断条件是比当前前缀和少但是比左边前缀和大

[862. 和至少为 K 的最短子数组](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/)

因为k是大于0的，所以可以使用前缀和算出前缀和，并且双端队列内保持递增的顺序，然后看减去第一个下标的前缀和是不是符合要求，循环判断找最小长度

```java
public int shortestSubarray(int[] nums, int k) {
    int n = nums.length;
    int minLen = n + 1; // 初始化为不可能的值
    
    // 1. 计算前缀和
    long[] prefixSum = new long[n + 1];
    for (int i = 0; i < n; i++) {
        prefixSum[i + 1] = prefixSum[i] + nums[i];
    }
    
    // 2. 使用双端队列维护候选索引
    Deque<Integer> deque = new ArrayDeque<>();
    
    for (int i = 0; i <= n; i++) {
        // 移除不满足单调性的元素
        while (!deque.isEmpty() && prefixSum[i] <= prefixSum[deque.peekLast()]) {
            deque.pollLast();
        }
        
        // 检查队列头部是否满足条件
        while (!deque.isEmpty() && prefixSum[i] - prefixSum[deque.peekFirst()] >= k) {
            minLen = Math.min(minLen, i - deque.pollFirst());
        }
        
        deque.offerLast(i);
    }
    
    return minLen <= n ? minLen : -1;
}
```

[554. 砖墙](https://leetcode.cn/problems/brick-wall/)

```java
public int leastBricks(List<List<Integer>> wall) {
    // 用于记录每个缝隙位置的出现次数
    Map<Integer, Integer> gapCount = new HashMap<>();
    for (List<Integer> row : wall) {
        int prefixSum = 0;
        // 注意：不考虑最后一块的右边缘（墙的最右侧）
        for (int i = 0; i < row.size() - 1; i++) {
            prefixSum += row.get(i);
            gapCount.put(prefixSum, gapCount.getOrDefault(prefixSum, 0) + 1);
        }
    }
    // 找到出现次数最多的缝隙位置
    int maxGap = 0;
    for (int count : gapCount.values()) {
        maxGap = Math.max(maxGap, count);
    }
    // 最少穿过的砖块数 = 总行数 - 最多缝隙对齐的行数
    return wall.size() - maxGap;
}
```

[13. 罗马数字转整数](https://leetcode.cn/problems/roman-to-integer/)

哈希表，找规律，发现左边字母比右边小说明是减掉

```java
public int romanToInt(String s) {
    Map<Character, Integer> roman = new HashMap<>();
    roman.put('I', 1);
    roman.put('V', 5);
    roman.put('X', 10);
    roman.put('L', 50);
    roman.put('C', 100);
    roman.put('D', 500);
    roman.put('M', 1000);
    
    int sum = 0;
    int prev = 0;
    
    for (int i = s.length() - 1; i >= 0; i--) {
        int curr = roman.get(s.charAt(i));
        sum += (curr < prev) ? -curr : curr;
        prev = curr;
    }
    
    return sum;
}
```

[525. 连续数组](https://leetcode.cn/problems/contiguous-array/)

```java
public int findMaxLength(int[] nums) {
    // 使用哈希表记录每个前缀和第一次出现的索引位置
    // key: 前缀和, value: 第一次出现该前缀和的索引
    Map<Integer, Integer> prefixSumMap = new HashMap<>();
    
    // 初始状态：前缀和为0出现在索引-1处（虚拟位置）
    // 这样当后续出现前缀和为0时，可以正确计算子数组长度
    prefixSumMap.put(0, -1);
    
    int maxLength = 0;  // 记录最大长度
    int currentSum = 0;  // 当前前缀和（把0当作-1，1当作+1）
    
    for (int i = 0; i < nums.length; i++) {
        // 更新当前前缀和：遇到1加1，遇到0减1
        // 这样当子数组的和为0时，表示0和1的数量相等
        currentSum += (nums[i] == 1) ? 1 : -1;
        
        // 检查当前前缀和是否曾经出现过
        if (prefixSumMap.containsKey(currentSum)) {
            // 如果出现过，说明这两个位置之间的子数组和为0
            // 即0和1的数量相等，计算当前子数组长度
            int length = i - prefixSumMap.get(currentSum);
            // 更新最大长度
            maxLength = Math.max(maxLength, length);
        } else {
            // 如果是第一次出现这个前缀和，记录它的位置
            // 这样后续再次出现时才能计算最长子数组
            prefixSumMap.put(currentSum, i);
        }
    }
    
    return maxLength;
}
```

[1262. 可被三整除的最大和](https://leetcode.cn/problems/greatest-sum-divisible-by-three/)

动态规划，用int[3] 存0-2余数的当前数字最大和，然后每个数字都要重新分配一个数组，然后newdp重新计算之后赋值给dp

```java
public int maxSumDivThree(int[] nums) {
        int dp[]={0, Integer.MIN_VALUE, Integer.MIN_VALUE};
        for(int num:nums)
        {
            int newdp[]=Arrays.copyOf(dp,dp.length);
            for(int i=0;i<3;++i)
            {
                int nextyu=(i-num%3+3)%3;
                newdp[nextyu]=Math.max(newdp[nextyu],dp[i]+num);
            }
            dp=newdp;
        }
        return dp[0];
    }
```

### IPv4地址与十进制数相互转换

```java
public static long ipToDecimal(String ipAddress) {
    String[] octets = ipAddress.split("\\.");
    
    long result = 0;
    for (int i = 0; i < 4; i++) {
        int octet = Integer.parseInt(octets[i]);
        result = (result << 8) | octet;
    }
    return result;
}

public static String decimalToIp(long decimal) {    
    return String.format("%d.%d.%d.%d",
        (decimal >> 24) & 0xFF,
        (decimal >> 16) & 0xFF,
        (decimal >> 8) & 0xFF,
        decimal & 0xFF);
}
```

[LCR 154. 复杂链表的复制](https://leetcode.cn/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

建立原node和新node之间的一个映射，这样遍历原链表或者hm的keyset可以建立两个指针的映射。

[1095. 山脉数组中查找目标值](https://leetcode.cn/problems/find-in-mountain-array/)

二分查找先找到山顶，判断条件是山顶左右两个元素都比他小，然后找到山顶后从0到山顶，山顶+1到最后一个下标。从左边递增区间一个函数找，找到直接返回，如果没有就右边递减区间一个函数找，找到返回否则返回-1

[面试题 17.14. 最小K个数](https://leetcode.cn/problems/smallest-k-lcci/)

快速选择算法，这个算法主要是那个左右循环的处理，记住都打上等号就好了，然后最后返回的是right的位置

```java
public int[] smallestK(int[] arr, int k) {
        if (arr.length == 0 || k <= 0) return new int[0];
        if (k >= arr.length) return arr.clone();
        
        int l = 0, r = arr.length - 1;
        while (l <= r) {
            int temp = quickselect(arr, l, r);
            if (temp == k) break;
            else if (temp < k) l = temp + 1;
            else r = temp - 1;
        }
        return Arrays.copyOf(arr, k);
    }

    int quickselect(int[] arr, int l, int r) {
        int p = arr[l];
        int left = l + 1, right = r;
        
        // 先移动左指针的版本
        while (left <= right) {
            while (left <= right && arr[left] <= p) left++;//这行和下面一行可以互换
            while (left <= right && arr[right] >= p) right--;
            if (left <= right) {
                swap(arr, left, right);
            }
        }
        swap(arr, l, right);
        return right;
    }

    void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
```

[946. 验证栈序列](https://leetcode.cn/problems/validate-stack-sequences/)

模拟栈操作，遇到元素就入栈，然后循环判断下标是不是可以出栈。

```java
public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> stack = new LinkedList<>();
        int i = 0; // popped序列的指针

        for (int num : pushed) {
            stack.push(num);
            while (!stack.isEmpty() && stack.peek() == popped[i]) {
                stack.pop();
                i++;
            }
        }

        return stack.isEmpty();
    }
```

[面试题 08.06. 汉诺塔问题](https://leetcode.cn/problems/hanota-lcci/)

递归，递归使用函数移动，太抽象了，这个盘子指针会来会变动

```java
public void hanota(List<Integer> A, List<Integer> B, List<Integer> C) {
        moveDisks(A.size(), A, B, C);
    }

    private void moveDisks(int n, List<Integer> from, List<Integer> helper, List<Integer> to) {
        if (n == 1) {
            // 基本情况：直接移动
            to.add(from.remove(from.size() - 1));
            return;
        }
        
        // 将n-1个盘子从from移到helper（借助to）
        moveDisks(n - 1, from, to, helper);
        
        // 移动最下面的盘子
        to.add(from.remove(from.size() - 1));
        
        // 将n-1个盘子从helper移到to（借助from）
        moveDisks(n - 1, helper, from, to);
    }
```

[540. 有序数组中的单一元素](https://leetcode.cn/problems/single-element-in-a-sorted-array/)

可以全部异或或者二分缩小范围，如果当前右侧一个和它相等且下标是偶数或者左边一个相等且下标是奇数，说明在右边，不然在左边

```java
public int singleNonDuplicate(int[] nums) {
        int n = nums.length;
        int l = 0, r = n-1 ;
        while(l < r){
            int mid = l + (r - l)/ 2;
            if (mid % 2 ==0 && nums[mid] == nums[mid+1] || mid % 2 !=0 && nums[mid] == nums[mid-1]){
                l = mid + 1;
            }else {
                r = mid;
            }
        }
        return  nums[l];
    }
```

[172. 阶乘后的零](https://leetcode.cn/problems/factorial-trailing-zeroes/)

贪心算法，每除以一个5得到的结果就是5的倍数，25的倍数（两个5），125（三个5）

```java
public int trailingZeroes(int n){
        int ans=0;
        while(n!=0){
            ans+=n/5;
            n=n/5;
        }
        return ans;
    }
```

[204. 计数质数](https://leetcode.cn/problems/count-primes/)

#### 埃氏筛，从二开始然后对质数的倍数进行标记，从i*i开始

```java
public int countPrimes(int n) {
        if (n <= 2) return 0;
        boolean[] isPrime = new boolean[n];
        for (int i = 2; i < n; i++) isPrime[i] = true;
        for (int i = 2; i * i < n; i++) {
            if (isPrime[i]) {
                // 从 i*i 开始，步长为 i
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        int count = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) count++;
        }
        return count;
    }
```

[109. 有序链表转换二叉搜索树](https://leetcode.cn/problems/convert-sorted-list-to-binary-search-tree/)

先把所有的值放到一个列表里面，然后使用二分分别构建二叉树

```java
public TreeNode sortedListToBST(ListNode head) {
        ArrayList<Integer> al=new ArrayList<>();
        while(head!=null)
        {
            al.add(head.val);
            head=head.next;
        }
        return build(al,0,al.size()-1);
    }
    
    TreeNode build(ArrayList<Integer> al,int begin,int end)
    {
        if(begin>end) return null;
        int mid=(begin+end)/2;
        TreeNode res=new TreeNode(al.get(mid));
        res.left=build(al,begin,mid-1);
        res.right=build(al,mid+1,end);
        return res;
    }
```

[680. 验证回文串 II](https://leetcode.cn/problems/valid-palindrome-ii/)

只有一次删除的机会，所以只要有一个不相等就进入特殊情况然后返回就好

```java
    public boolean validPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                // 尝试跳过左边字符或右边字符
                return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
            }
            left++;
            right--;
        }
        return true;
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
```

[109. 有序链表转换二叉搜索树](https://leetcode.cn/problems/convert-sorted-list-to-binary-search-tree/)

先把有序链表放到arraylist里面，然后就转化成了用有序数组构建二叉树，用二分法直接访问中间元素，然后左右建树就完成了，更快的方法是用指针移动访问长度的元素，这样快一点。

[547. 省份数量](https://leetcode.cn/problems/number-of-provinces/)

两个想法，一个是采用并查集，看看有多少个连通分量，注意在最后是要用哈希表去重，并且有些father没有路径压缩要记得去压缩再加入。第二个是采用dfs然后加上visited表看看有几个连通分量，速度更快一点

```
for(int fa:father)
            hs.add(find(fa));
```

[LCR 148. 验证图书取出顺序](https://leetcode.cn/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

模拟栈，主要是while循环的停止条件，一个是放入栈一个是栈不空就可以不断出栈

```java
public boolean validateBookSequences(int[] putIn, int[] takeOut) {
        LinkedList<Integer> stack=new LinkedList<>();
        int i=0,j=0;
        while(i<putIn.length||!stack.isEmpty())
        {
            if(!stack.isEmpty()&&stack.getLast()==takeOut[j])
                {
                    stack.removeLast();
                    ++j;
                }
            else if(i<putIn.length){
                stack.add(putIn[i++]);
            }else return false;
        }
        return j==takeOut.length;
    }
```

[面试题 10.03. 搜索旋转数组](https://leetcode.cn/problems/search-rotate-array-lcci/)

需要单独判断一下0的位置，因为在这个用例过不去arr = [5,5,5,1,2,3,4,5]

注意判断条件是两个，是target分别与left或者right和mid比

```java
public int search(int[] arr, int target) {
    	if(arr[0]==target) return 0;//可能最左侧是的
        int left = 0, right = arr.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;    
            // 找到目标值时，向左查找最左边界
            if (arr[mid] == target) {
                // 继续向左搜索最左边界
                while (mid > left && arr[mid - 1] == target) {
                    mid--;
                }
                return mid;
            }   
            // 处理无法判断有序区间的情况
            if (arr[left] == arr[mid] && arr[mid] == arr[right]) {
                left++;
                right--;
            } 
            // 左半区有序
            else if (arr[left] <= arr[mid]) {
                if (arr[left] <= target && target < arr[mid]) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } 
            // 右半区有序
            else {
                if (arr[mid] < target && target <= arr[right]) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }     
        return -1;
    }
```

[617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

如果为空直接返回另一个就好，然后加入root1中

```java
public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        if(root1==null) return root2;
        if(root2==null) return root1;
        root1.val+=root2.val;
        root1.left=mergeTrees(root1.left,root2.left);
        root1.right=mergeTrees(root1.right,root2.right);
        return root1;
    }
```

[386. 字典序排数](https://leetcode.cn/problems/lexicographical-numbers/)

使用DFS就可以了，贪心选择从1到9的数字然后一个个×10再加入个位数添加

```java
ArrayList<Integer> res=new ArrayList<>();
    public List<Integer> lexicalOrder(int n) {
        for(int i=1;i<=9;++i)
            if(i<=n) dfs(i,n);
        return res;
    }

    void dfs(int now,int n)
    {
        res.add(now);
        for(int i=0;i<=9;++i)
        {
            int temp=now*10+i;
            if(temp<=n)
                dfs(temp,n);
            else break;
        }
    }
```

[523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)

前缀和加上哈希表，存的是余数对应下标，因为只要余数相等他们的差即中间的东西加起来肯定是k的倍数

```java
public boolean checkSubarraySum(int[] nums, int k) {
        HashMap<Integer,Integer> hm=new HashMap<>();
        int prefix=0;
        hm.put(0,-1);
        for(int i=0;i<nums.length;++i)
        {
            prefix+=nums[i];
            int now=prefix%k;
            if(hm.containsKey(now)) {
                if (i - hm.get(now) >= 2)//注意if里面还有一个判断，如果包含的话只留下最早的那个下标，不用再加入了，不然5，0，0，0这个用例会出错
                    return true;
            }
            else hm.put(now,i);
        }
        return false;
    }
```

[334. 递增的三元子序列](https://leetcode.cn/problems/increasing-triplet-subsequence/)

贪心算法，找当前最小的第一个数，如果比第一个数小，置换它，不然找最小的第二个数，如果小，置换它，如果这个数既不比第一个小，也不比第二个小，那就是第三个数了返回true；

```java
public boolean increasingTriplet(int[] nums) {
        if(nums.length<3) return false;
        int first=Integer.MAX_VALUE;
        int second=Integer.MAX_VALUE;
        for(int num:nums)
        {
            if(num<=first)
                first=num;
            else if(num<=second)
                second=num;
            else return true;
        }
        return false;
    }
```

[974. 和可被 K 整除的子数组](https://leetcode.cn/problems/subarray-sums-divisible-by-k/)

看前面两个的解析，通过前缀和得到所有余数的前缀和，然后每个有两个余数以上的都有(n-1)*n/2个子序列达成目标

```java
public int subarraysDivByK(int[] nums, int k) {
        int yu[]=new int[k];
        int prefix=0;
        ++yu[0];
        for(int num:nums)
        {
            prefix+=num;
            ++yu[(prefix%k+k)%k];
        }
        int sum=0;
        for(int n:yu)
        {
            if(n>1)
                sum+=n*(n-1)/2;
        }
        return sum;
    }
```

[49. 字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

字母异位词可以把string转成char[ ]然后排序，再转成String，这样就有唯一字典序的key了，再用hashmap一一放进去

```java
public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> res=new ArrayList<>();
        HashMap<String,List<String>> hm=new HashMap<>();
        for(String s:strs)
        {
            char []str=s.toCharArray();
            Arrays.sort(str);
            String key=new String(str);
            if(hm.containsKey(key))
                hm.get(key).add(s);
            else {
                List<String> ls=new ArrayList<>();
                ls.add(s);
                hm.put(key,ls);
            }
        }
        for(List<String> ls:hm.values())
            res.add(ls);
        return res;
    }
```

[994. 腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)

BFS加入腐烂橘子坐标，最后再判断是不是还有没腐烂的橘子

```java
public int orangesRotting(int[][] grid) {
        Deque<int[]> dq=new LinkedList<>();
        for(int i=0;i<grid.length;++i)
            for(int j=0;j<grid[0].length;++j)
                if(grid[i][j]==2)
                    dq.add(new int[]{i,j});
        int cnt=0;
        int dir[][]={{0,-1},{0,1},{-1,0},{1,0}};
        while(!dq.isEmpty())
        {
            int dq_size=dq.size();
            for(int i=0;i<dq_size;++i)
            {
                int rot[]=dq.poll();
                for(int j=0;j<4;++j)
                {
                    int nextrow=rot[0]+dir[j][0];
                    int nextcol=rot[1]+dir[j][1];
                    if(nextrow<0||nextrow>=grid.length||
                            nextcol<0||nextcol>=grid[0].length
                                ||grid[nextrow][nextcol]!=1) continue;
                    dq.add(new int[]{nextrow,nextcol});
                    grid[nextrow][nextcol]=2;
                }
            }
            if(dq.size()!=0) ++cnt;
        }
        for(int i=0;i<grid.length;++i)
            for(int j=0;j<grid[0].length;++j)
                if(grid[i][j]==1)
                    return -1;
        return cnt;
    }
```

[1556. 千位分隔数](https://leetcode.cn/problems/thousand-separator/)

直接字符串处理，感觉也可以在原串逆着输入，碰到三的倍数就加入 . ，这样会更容易想

```java
public String thousandSeparator(int n) {
        String num = String.valueOf(n);
        StringBuilder res = new StringBuilder();
        
        for (int i = 0; i < num.length(); i++) {
            if (i > 0 && (num.length() - i) % 3 == 0) {
                res.append('.');
            }
            res.append(num.charAt(i));
        }
        
        return res.toString();
    }
```

[233. 数字 1 的个数](https://leetcode.cn/problems/number-of-digit-one/)

困难题

```java
public int countDigitOne(int n) {
        int count = 0; // 统计总次数
        int factor = 1; // 当前位的权重，初始化为个位
        int high = n / 10; // 高位
        int cur = n % 10; // 当前位
        int low = 0; // 低位

        while (high != 0 || cur != 0) {
            if (cur == 0) {
                count += high * factor; // 当前位为0时，只看高位对1的贡献
            } else if (cur == 1) {
                count += high * factor + low + 1; // 当前位为1时，包含高位和低位的贡献
            } else {
                count += (high + 1) * factor; // 当前位>1时，高位的贡献需要多加一个factor
            }

            // 更新变量，进入更高一位的分析
            low += cur * factor; // 将当前位加入低位
            cur = high % 10; // 更新当前位
            high /= 10; // 更新高位
            factor *= 10; // 权重乘10，进入下一位
        }

        return count;
    }
```

按位找，找每一位为1的时候有多少种可能，再把每一位的个数加起来就是总1的个数

为什么是三种情况呢

当当前位为0的时候高位的0到high-1可以满足到当前位为1的可能。

当当前位为1的时候，高位可以满足0到high-1的当前位为1，还可以满足high的部分是high当前位为1但是低位可以从0到low的可能

当当前位大于1的时候，高位可以满足0到high的high+1种可能

![1756298328113](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1756298328113.png)

[315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)

```java
public List<Integer> countSmaller(int[] nums) {
    List<Integer> ans = new ArrayList<>();
    int len = nums.length;
    
    // index数组：记录元素的原始位置，排序过程中元素会移动，但索引保持不变
    // 例如：初始时index[i] = i，表示第i个元素在原始数组中的位置是i
    int[] index = new int[len];
    
    // res数组：存储结果，res[i]表示原始位置为i的元素右侧有多少比它小的元素
    int[] res = new int[len];
    
    // 初始化索引数组
    for (int i = 0; i < len; i++) {
        index[i] = i;
    }
    
    // 进行归并排序并统计逆序对
    mergeSort2(res, index, nums, 0, len - 1);
    
    // 将结果数组转换为List
    for (int r : res) {
        ans.add(r);
    }
    return ans;
}

/**
 * 归并排序递归函数
 * @param res 结果数组
 * @param index 索引数组（记录元素的原始位置）
 * @param nums 原始数组
 * @param left 当前处理区间的左边界
 * @param right 当前处理区间的右边界
 */
private static void mergeSort2(int[] res, int[] index, int[] nums, int left, int right) {
    // 递归终止条件：区间只有一个元素或为空
    if (left >= right) {
        return;
    }
    
    // 计算中点
    int mid = left + (right - left) / 2;
    
    // 递归排序左半部分
    mergeSort2(res, index, nums, left, mid);
    
    // 递归排序右半部分
    mergeSort2(res, index, nums, mid + 1, right);
    
    // 合并两个有序区间并统计逆序对
    merge2(res, index, nums, left, mid, right);
}

/**
 * 合并两个有序区间并统计逆序对
 * @param res 结果数组
 * @param index 索引数组
 * @param nums 原始数组
 * @param left 左区间起点
 * @param mid 左区间终点（右区间起点为mid+1）
 * @param right 右区间终点
 */
private static void merge2(int[] res, int[] index, int[] nums, int left, int mid, int right) {
    int i = left;        // 左区间指针
    int j = mid + 1;     // 右区间指针
    int p = 0;           // 临时数组指针
    int rightCount = 0;  // 计数器：记录已经从右区间取出的元素数量
    
    // 临时数组，用于存储合并后的索引
    int[] temp = new int[right - left + 1];
    
    // 合并两个有序区间
    while (i <= mid && j <= right) {
        // 比较两个元素的值（通过索引访问原始数组）
        if (nums[index[i]] > nums[index[j]]) {
            // 情况1：右区间元素更小
            // 将右区间元素放入临时数组
            temp[p++] = index[j++];
            // 增加计数器：这个右区间元素比当前左区间所有剩余元素都小
            rightCount++;
        } else {
            // 情况2：左区间元素更小或相等
            // 将左区间元素放入临时数组
            // 同时，将当前rightCount累加到该元素的逆序数中
            res[index[i]] += rightCount;
            temp[p++] = index[i++];
        }
    }
    
    // 处理左区间剩余元素
    while (i <= mid) {
        // 所有右区间元素都已经处理完毕，且都比这些左区间元素小
        // 所以每个左区间元素的逆序数都要加上rightCount
        res[index[i]] += rightCount;
        temp[p++] = index[i++];
    }
    
    // 处理右区间剩余元素
    while (j <= right) {
        // 这些元素不会影响逆序数统计，直接放入临时数组
        temp[p++] = index[j++];
    }
    
    // 将临时数组中的索引拷贝回原索引数组
    for (int k = 0; k < temp.length; k++) {
        index[left + k] = temp[k];
    }
}
```

[1312. 让字符串成为回文串的最少插入次数](https://leetcode.cn/problems/minimum-insertion-steps-to-make-a-string-palindrome/)

思路非常简单，就是找到字符串的最长回文子序列长度，然后用字符串长度减去这个长度剩下的就是要插入的次数

```java
public int minInsertions(String s) {
        int n=s.length();
        int dp[][]=new int[n][n];
        for(int i=n-1;i>=0;--i)
        {
            dp[i][i]=1;//初始化
            for(int j=i+1;j<n;++j)
                if(s.charAt(i)==s.charAt(j))
                    dp[i][j]=dp[i+1][j-1]+2;
                else dp[i][j]=Math.max(dp[i+1][j],dp[i][j-1]);
        }
        return n-dp[0][n-1];
    }
```

[407. 接雨水 II](https://leetcode.cn/problems/trapping-rain-water-ii/)

三维的接雨水,最小堆存边界高度，然后一个个访问内部的块，当前水位高度取决于边界的最低高度，然后不断加入队列从外围一直加到里面，外围的高度会一直扩散到里面

```java
public int trapRainWater(int[][] heightMap) {
        if (heightMap == null || heightMap.length == 0 || heightMap[0].length == 0) {
            return 0;
        }
        int m = heightMap.length;
        int n = heightMap[0].length;
        int totalWater = 0;
        // 优先队列，按高度排序（最小堆）
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        boolean[][] visited = new boolean[m][n];
        // 将边界元素加入队列
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    pq.offer(new int[]{i, j, heightMap[i][j]});
                    visited[i][j] = true;
                }
            }
        }
        // 四个方向：上、右、下、左
        int[][] dirs = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
        while (!pq.isEmpty()) {
            int[] cell = pq.poll();
            int i = cell[0], j = cell[1], h = cell[2];
            for (int[] dir : dirs) {
                int ni = i + dir[0];
                int nj = j + dir[1];
                // 检查新位置是否有效且未访问
                if (ni >= 0 && ni < m && nj >= 0 && nj < n && !visited[ni][nj]) {
                    visited[ni][nj] = true;
                    // 当前水位高度取决于边界的最低高度
                    int waterLevel = Math.max(h, heightMap[ni][nj]);
                    totalWater += Math.max(0, h - heightMap[ni][nj]);
                    // 将新位置加入队列，水位高度取最大值
                    pq.offer(new int[]{ni, nj, waterLevel});
                }
            }
        }
        return totalWater;
    }
```

[437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

![1756362201483](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1756362201483.png)

```java
	HashMap<Long,Integer> hm=new HashMap<>();
    int cnt;
    public int pathSum(TreeNode root, int targetSum) {
        if(root==null) return 0;
        cnt=0;
        hm.put(0L,1);
        dfs(root,targetSum,0);
        return cnt;
    }

    void dfs(TreeNode root,int target,long nowsum)//long防止溢出
    {
        nowsum+=root.val;
        cnt+=hm.getOrDefault(nowsum-target,0);
        hm.put(nowsum,hm.getOrDefault(nowsum,0)+1);
        if(root.left!=null) dfs(root.left,target,nowsum);
        if(root.right!=null) dfs(root.right,target,nowsum);
        hm.put(nowsum,hm.get(nowsum)-1);
    }
```

[LCR 165. 解密数字](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

主要是使用了两个数字之后的条件判断，一个是不越界，没有前导零，然后小于26

```java
int res;
    public int crackNumber(int ciphertext) {
        res=0;
        String str=String.valueOf(ciphertext);
        dfs(str,0);
        return res;
    }

    void dfs(String str,int nowidx)
    {
        if(nowidx==str.length())
        {
            ++res;
            return;
        }
        dfs(str,nowidx+1);
        if(nowidx+2<=str.length()&&str.charAt(nowidx)!='0'
        &&Integer.parseInt(str.substring(nowidx,nowidx+2))<26)
            dfs(str,nowidx+2);
    }
```

[面试题 08.12. 八皇后](https://leetcode.cn/problems/eight-queens-lcci/)

八皇后更好的写法，摸索出来的，dfs要的是棋盘，当前行和行数

```java
List<List<String>> res=new ArrayList<>();
    char[][] chess;
    public List<List<String>> solveNQueens(int n) {
        chess=new char[n][n];
        for(int i=0;i<n;++i)
            for(int j=0;j<n;++j)
                chess[i][j]='.';

        dfs(n,0);
        return res;
    }

    void dfs(int n,int row)
    {
        if(row==n)
        {
            List<String> r=new ArrayList<>();
            for(char[] c:chess)
                r.add(new String(c));
            res.add(r);
            return;
        }
        for(int i=0;i<n;++i)
        {
            if(isok(n,row,i))
            {
                chess[row][i]='Q';
                dfs(n,row+1);
                chess[row][i]='.';
            }
        }
    }

    boolean isok(int n,int row,int col)
    {
        for(int i=0;i<row;++i)
            if(chess[i][col]=='Q') return false;
        for(int i=0;i<col;++i)
            if(chess[row][i]=='Q') return false;
        for(int i=row-1,j=col-1;i>=0&&j>=0;--i,--j)
            if(chess[i][j]=='Q') return false;
        for(int i=row-1,j=col+1;i>=0&&j<n;--i,++j)
            if(chess[i][j]=='Q') return false;
        return true;
    }
```

[902. 最大为 N 的数字组合](https://leetcode.cn/problems/numbers-at-most-n-given-digit-set/)

分为两部分，位数小于k的数字和位数等于k的数字，位数等于k的数字比较第一位，如果当前位小的话就可以加上pow的k-1位，因为肯定小于界限值，如果相等就加上下面一位的可能性，如果大于那就直接跳过

```java
public int atMostNGivenDigitSet(String[] digits, int n) {
        char[] s = String.valueOf(n).toCharArray();
        int k = s.length;
        int[] dp = new int[k + 1];
        dp[k] = 1; // 空字符串算作1种情况
        
    	//si指的是要比较的数字，di指的是当前位数字
        // 从低位到高位处理
        for (int i = k - 1; i >= 0; i--) {
            int si = s[i] - '0';
            for (String d : digits) {
                int di = d.charAt(0) - '0';
                if (di < si) {
                    dp[i] += Math.pow(digits.length, k - i - 1);
                } else if (di == si) {
                    dp[i] += dp[i + 1];
                }
            }
        }
        
        // 加上所有位数小于k的数字
        int sum = 0;
        for (int i = 1; i < k; i++) {
            sum += Math.pow(digits.length, i);
        }
        
        return dp[0] + sum;
    }
```

[60. 排列序列](https://leetcode.cn/problems/permutation-sequence/)

背吧，数学方法，先生成阶乘数组，然后看底下吧，每位的索引除以当前阶乘，加入之后移除掉这个元素再用k取余，真是米奇妙妙屋

```java
public String getPermutation(int n, int k) {
        // 1. 生成阶乘数组
        int[] factorial = new int[n + 1];
        factorial[0] = 1;
        for (int i = 1; i <= n; i++) {
            factorial[i] = factorial[i - 1] * i;
        }

        // 2. 初始化候选数字列表
        ArrayList<Integer> candidates = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            candidates.add(i);
        }

        // 3. 构建第 k 个排列
        StringBuilder result = new StringBuilder();
        k--; // 调整为 0 索引
        for (int i = 1; i <= n; i++) {
            int idx = k / factorial[n - i];
            result.append(candidates.get(idx));
            candidates.remove(idx);
            k %= factorial[n - i];
        }
        return result.toString();
    }
```

[238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)

构造前后缀乘积数组，然后数组的含义是排除掉当前数字的其他前后缀的乘积

```java
public int[] productExceptSelf(int[] nums) {
        int prefix[]=new int[nums.length];
        int lastfix[]=new int[nums.length];
        Arrays.fill(prefix,1);
        Arrays.fill(lastfix,1);
        for(int i=1;i<nums.length;++i)
            prefix[i]=prefix[i-1]*nums[i-1];//这里两个都是i-1
        for(int i=nums.length-2;i>=0;--i)
            lastfix[i]=lastfix[i+1]*nums[i+1];//这里两个都是i+1；
        int res[]=new int[nums.length];
        for(int i=0;i<nums.length;++i)
            res[i]=prefix[i]*lastfix[i];
        return res;
    }
```

[628. 三个数的最大乘积](https://leetcode.cn/problems/maximum-product-of-three-numbers/)

思路一，排序然后

```java
return Math.max(nums[0] * nums[1] * nums[n - 1], nums[n - 1]*nums[n - 2]*nums[n - 3]);
```

思路二，遍历一遍动态修改最大值最小值

```java
public int maximumProduct(int[] nums) {
        int max1=Integer.MIN_VALUE,max2=Integer.MIN_VALUE,max3=Integer.MIN_VALUE,
    min1=Integer.MAX_VALUE,min2=Integer.MAX_VALUE;
        for(int i=0;i<nums.length;i++){
            if(nums[i]>max1){
                max3=max2;
                max2=max1;
                max1=nums[i];
            }else if(nums[i]>max2){
                max3=max2;
                max2=nums[i];
            }else if(nums[i]>max3){
                max3=nums[i];
            } 
            if(nums[i]<min1){
                min2=min1;
                min1=nums[i];
            }else if(nums[i]<min2){
                min2=nums[i];
            }
             
        }
        return   Math.max(min1*min2*max1,max1*max2*max3);
    }
```

[658. 找到 K 个最接近的元素](https://leetcode.cn/problems/find-k-closest-elements/)

![1756518717255](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1756518717255.png)

```java
public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int left=0,right=arr.length-1;
        while(right-left+1>k)
        {
            if(Math.abs(arr[left]-x)>Math.abs(arr[right]-x))
                ++left;
            else --right;
        }
        List<Integer> res=new ArrayList<>();
        for(int i=left;i<=right;++i)
            res.add(arr[i]);
        return res;
    }
```

[68. 文本左右对齐](https://leetcode.cn/problems/text-justification/)

脉络非常清晰，看注释吧

```java
public List<String> fullJustify(String[] words, int maxWidth) {
        List<String> result = new ArrayList<>();
        int index = 0;
        // 遍历每个单词，按行处理
        while (index < words.length) {
            int totalChars = words[index].length();//一行中肯定有第一个单词
            int last = index + 1;//初始指针在下一个单词
            // 计算当前行能容纳的单词数
            while (last < words.length) {
                // `1`表示单词之间至少需要的空格
                if (totalChars + 1 + words[last].length() > maxWidth) break;
                totalChars += 1 + words[last].length();
                last++;
            }
            StringBuilder sb = new StringBuilder();
            int wordCount = last - index; // 计算本行的单词数
            // 如果是最后一行或者当前行只有一个单词，则左对齐
            if (last == words.length || wordCount == 1) {
                for (int i = index; i < last; i++) {
                    sb.append(words[i]);
                    if (i < last - 1) {
                        sb.append(' ');
                    }
                }
                // 补齐右边空格
                while (sb.length() < maxWidth) {
                    sb.append(' ');
                }
            } else {
                // 不是最后一行且有多个单词，计算空格
                int spaces = (maxWidth - totalChars + (wordCount - 1)) / (wordCount - 1);
                int extraSpaces = (maxWidth - totalChars + (wordCount - 1)) % (wordCount - 1);
                for (int i = index; i < last; i++) {
                    sb.append(words[i]);
                    if (i < last - 1) {
                        // 添加均匀分布的空格
                        for (int j = 0; j < spaces; j++) {
                            sb.append(' ');
                        }
                        // 如果有多余的空格，优先分配给左边
                        if (extraSpaces > 0) {
                            sb.append(' ');
                            extraSpaces--;
                        }
                    }
                }
            }   
            result.add(sb.toString());
            index = last; // 更新索引，处理下一行
        }
        return result;
    }
```

[845. 数组中的最长山脉](https://leetcode.cn/problems/longest-mountain-in-array/)

前后缀最长数组，前后缀的意思是除了当前位置之外，前面或者后面最长的递增和递减长度是多少

```java
public int longestMountain(int[] arr) {
        int predp[]=new int[arr.length];
        int lastdp[]=new int[arr.length];
        for(int i=1;i<arr.length;++i)
            if(arr[i]>arr[i-1]) predp[i]=predp[i-1]+1;
        for(int i=arr.length-2;i>=0;--i)
            if(arr[i]>arr[i+1]) lastdp[i]=lastdp[i+1]+1;
        int res=0;
        for(int i=0;i<arr.length;++i)
            if(lastdp[i]>0&&predp[i]>0)
                res=Math.max(res,lastdp[i]+predp[i]+1);
        return res<3?0:res;
    }
```

[486. 预测赢家](https://leetcode.cn/problems/predict-the-winner/)

![1756614370117](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1756614370117.png)

二维的

```java
public boolean predictTheWinner(int[] nums) {
    int n = nums.length;
    int[][] dp = new int[n][n];
    
    // 初始化：只有一个数字时
    for (int i = 0; i < n; i++) {
        dp[i][i] = nums[i];
    }
    
    // 填充dp表：从下到上，从左到右
    for (int i = n - 2; i >= 0; i--) {
        for (int j = i + 1; j < n; j++) {
            //意思是要么取下标i的要么取下标j的
            dp[i][j] = Math.max(nums[i] - dp[i+1][j], nums[j] - dp[i][j-1]);
        }
    }
    
    return dp[0][n-1] >= 0;
}
```

压缩成一维

```java
public boolean predictTheWinner(int[] nums) {
        int n = nums.length;
        if(n%2==0) return true;//数组长度为偶数有必胜策略
        int[] dp = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            dp[i] = nums[i];
            for (int j = i + 1; j < n; j++) {
                dp[j] = Math.max(nums[i] - dp[j], nums[j] - dp[j-1]);
            }
        }
        return dp[n-1] >= 0;
    }
```

[735. 小行星碰撞](https://leetcode.cn/problems/asteroid-collision/)

使用栈结构是比较难想的，然后

```java
    public int[] asteroidCollision(int[] asteroids) {
        Deque<Integer> stack = new LinkedList<>();

        for (int ast : asteroids) {
            boolean destroyed = false;
            // 当前小行星向左，且栈顶小行星向右
            //这个条件也有点难想，最后还要保证没有被摧毁
            while (!stack.isEmpty() && ast < 0 && stack.peek() > 0 && !destroyed) {
                int top = stack.peek();

                if (top > -ast) {
                    // 栈顶更大，当前小行星被摧毁
                    destroyed = true;
                } else if (top == -ast) {
                    // 大小相等，两者都摧毁
                    stack.pop();
                    destroyed = true;
                } else {
                    // 当前小行星更大，栈顶被摧毁
                    stack.pop();
                }
            }
            if (!destroyed) {
                stack.push(ast);
            }
        }
        // 将栈转换为数组
        int[] result = new int[stack.size()];
        for (int i = result.length - 1; i >= 0; i--) {
            result[i] = stack.pop();
        }
        return result;
    }
```

[73. 矩阵置零](https://leetcode.cn/problems/set-matrix-zeroes/)

使用原地算法，那么我们可以做上标记，把要置零的行和列设为true，然后再统一置零

```java
public void setZeroes(int[][] matrix) {
        boolean row[]=new boolean[matrix.length];
        boolean col[]=new boolean[matrix[0].length];
        for(int i=0;i< matrix.length;i++)
            for(int j=0;j<matrix[0].length;j++)
                if(matrix[i][j]==0)
                {
                    row[i]=true;
                    col[j]=true;
                }
        for(int i=0;i< row.length;i++)
            if(row[i])
                for(int j=0;j<col.length;j++)
                    matrix[i][j]=0;
        for(int i=0;i< col.length;i++)
            if(col[i])
                for(int j=0;j<row.length;j++)
                    matrix[j][i]=0;
    }
```

[456. 132 模式](https://leetcode.cn/problems/132-pattern/)

使用一个单调递减栈就可以解决问题

```java
public boolean find132pattern(int[] nums) {
    // 使用LinkedList模拟栈（比Stack更快）
    Deque<Integer> stack = new LinkedList<>();
    // 记录可能的"2"（即比当前"3"小的最大值）
    int two = Integer.MIN_VALUE;
    // 从后往前遍历（确保i<j<k的顺序）
    for (int i = nums.length - 1; i >= 0; i--) {
        // 当前数字作为"1"，若小于two（"2"），则存在132模式
        if (nums[i] < two) {
            return true;
        }
        // 维护单调递减栈：
        // 弹出所有小于当前数字的元素，并用其更新max
        while (!stack.isEmpty() && stack.peekLast() < nums[i]) {
            two = Math.max(two, stack.pollLast());
        }
        // 将当前数字压栈（作为候选的"3"）
        stack.offerLast(nums[i]);
    }
    return false;
}
```

[LCR 169. 招式拆解 II](https://leetcode.cn/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

第一种使用顺序哈希表

```java
public char dismantlingAction(String arr) {
        LinkedHashMap<Character,Integer> hm=new LinkedHashMap<>();
        for(char c:arr.toCharArray())
            hm.put(c,hm.getOrDefault(c,0)+1);
        for(char c:hm.keySet())
            if(hm.get(c)==1)
                return c;
        return ' ';
    }
```

第二种使用boolean普通哈希表，不知道为什么整数的就不行，懂了，第二次遍历要遍历原数组的顺序，而不是哈希表的keys

```java
public char dismantlingAction(String arr) {
        HashMap<Character, Boolean> hmap = new HashMap<>();
        char[] sc = arr.toCharArray();
        for(char c : sc)
            hmap.put(c, !hmap.containsKey(c));
        for(char c : sc)
            if(hmap.get(c)) return c;
        return ' ';
    }
```

[435. 无重叠区间](https://leetcode.cn/problems/non-overlapping-intervals/)

反向思维，结果 = 总区间数 - 最大不重叠区间数

```java
public int eraseOverlapIntervals(int[][] intervals) {
    // 边界情况：空数组或单个区间无需移除
    if (intervals.length <= 1) return 0;
    // 关键步骤1：按区间结束位置升序排序
    // 比较器：提取每个区间的a[1]（结束位置）进行排序
    Arrays.sort(intervals, Comparator.comparingInt(a -> a[1]));
    // 初始化：
    // cnt - 记录最大不重叠区间数（至少为1）
    // end - 记录当前不重叠区间的结束位置
    int cnt = 1;
    int end = intervals[0][1];
    // 关键步骤2：遍历排序后的区间
    for (int[] curr : intervals) {
        // 当前区间起始 ≥ 前一个区间的结束 → 不重叠
        if (curr[0] >= end) {
            cnt++;          // 增加不重叠区间计数
            end = curr[1]; // 更新结束位置
        }
        // 否则：区间重叠，跳过（隐含移除操作）
    }
    // 结果 = 总区间数 - 最大不重叠区间数
    return intervals.length - cnt;
}
```

[907. 子数组的最小值之和](https://leetcode.cn/problems/sum-of-subarray-minimums/)

单调最小栈，保留当前最小的值的下标，如果被替换掉了就可以算出它是多少个子数组的最小的候选值，等于左侧范围乘以右侧范围，这个初始化和终结计算栈非常有

```java
public int sumSubarrayMins(int[] arr) {
        long ans = 0;
        Deque<Integer> dq = new ArrayDeque<>();
        dq.push(-1); // 虚拟左边界
        // 遍历数组（包含虚拟右边界）
        for (int r = 0; r <= arr.length; r++) {
            // 当前元素值（虚拟右边界设为-1）
            int x = r < arr.length ? arr[r] : -1;
            // 维护单调递增栈（遇到较小值时触发计算）
            while (dq.size() > 1 && x <= arr[dq.peek()]) {
                int i = dq.pop(); // 弹出当前元素索引
                // 贡献值 = 左范围 * 右范围 * 元素值
                ans += (long)(i - dq.peek()) * (r - i) * arr[i];
            }   
            dq.push(r); // 压入当前索引
        }
        return (int)(ans % 1_000_000_007);//这个要记得打括号，不然只有ans强转了
    }
```

[306. 累加数](https://leetcode.cn/problems/additive-number/)

要使用大整数加法不然会溢出

```java
import java.math.BigInteger;
class Solution {
    public boolean isAdditiveNumber(String num) {
        if(num.length()<3) return false;
        for(int i=1;i<num.length();++i)
        {
            for(int j=i+1;j<num.length();++j)
            {
                String a=num.substring(0,i);
                String b=num.substring(i,j);
                if(a.length()>1&&a.charAt(0)=='0'||b.length()>1&&b.charAt(0)=='0') continue;
                if(is_valid(num,a,b,j)) return true;
            }
        }
        return false;
    }

    boolean is_valid(String num,String a,String b,int idx)
    {
        if(idx>=num.length()) return false;
        BigInteger A=new BigInteger(a),B=new BigInteger(b);
        while(idx<num.length())
        {
            BigInteger sum=A.add(B);
            if(num.substring(idx).startsWith(sum.toString()))
            {
                A=B;
                B=sum;
                idx+=sum.toString().length();
            }else return false;
        }
        return true;
    }
}
```

[149. 直线上最多的点数](https://leetcode.cn/problems/max-points-on-a-line/)

使用直线公式，斜率相等来计算，这样三重循环的顺序可以比较好的剪枝

```java
public int maxPoints(int[][] points) {
        int n=points.length;
        int res=1;
        for(int i=0;i<n-1;++i)
        {
            for(int j=i+1;j<n;++j)
            {
                int x1=points[i][0],y1=points[i][1];
                int x2=points[j][0],y2=points[j][1];
                int cnt=2;
                for(int k=j+1;k<n;++k)
                {
                    int x3=points[k][0],y3=points[k][1];
                    if((y1-y2)*(x2-x3)==(y2-y3)*(x1-x2))
                        ++cnt;
                }
                res=Math.max(res,cnt);
            }
        }
        return res;
    }
```

[166. 分数到小数](https://leetcode.cn/problems/fraction-to-recurring-decimal/)

只能这么写，毁灭吧，必须用long，然后还要考虑负数

```java
public String fractionToDecimal(int numerator, int denominator) {
        // 1. 处理符号
        if (numerator == 0) return "0"; // 如果分子为0，直接返回"0"
        // 结果字符串
        StringBuilder result = new StringBuilder();
        // 如果符号不同，结果为负数
        if ((numerator < 0) ^ (denominator < 0)) {
            result.append("-");
        }
        // 2. 计算整数部分
        long num = Math.abs((long) numerator);  // 使用long避免溢出
        long denom = Math.abs((long) denominator);
        // 整数部分
        result.append(num / denom);
        // 3. 处理小数部分
        long remainder = num % denom; // 余数
        if (remainder == 0) {
            return result.toString(); // 如果余数为0，说明没有小数部分，直接返回结果
        }
        result.append("."); // 小数部分开始
        Map<Long, Integer> map = new HashMap<>(); // 记录余数的位置
        while (remainder != 0) {
            // 如果余数曾出现过，说明进入了循环小数
            if (map.containsKey(remainder)) {
                int index = map.get(remainder);
                result.insert(index, "("); // 在循环开始的位置插入"("
                result.append(")"); // 在循环结束的位置插入")"
                return result.toString();
            }
            map.put(remainder, result.length()); // 记录余数出现的位置
            remainder *= 10; // 乘以10，得到下一位
            result.append(remainder / denom); // 计算小数位
            remainder = remainder % denom; // 更新余数
        }
        return result.toString(); // 没有循环，返回最终结果
    }
```

[452. 用最少数量的箭引爆气球](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/)

贪心，有多少不重合的区间就行，这个是两端不包括的所以不加等号

```java
public int findMinArrowShots(int[][] points) {
        Arrays.sort(points,Comparator.comparingInt(a->a[1]));
        int end=points[0][1];
        int cnt=1;
        for(int i=1;i<points.length;++i)
            if(points[i][0]>end)
            {
                ++cnt;
                end=points[i][1];
            }
        return cnt;
    }
```

[LCR 162. 数字 1 的个数](https://leetcode.cn/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

又来了，按位加法，把每一个位的个数加上去，当前位为1的个数由高位决定，如果当前位为1，高位会少一个，就是高位为0的时候，加上低位个数+1就行

```java
public int digitOneInNumber(int num) {
        int res=0,fact=1;
        int high=num/10,cur=num%10,low=0;
        while(high!=0||cur!=0)
        {
            if(cur==0) res+=high*fact;
            else if(cur==1) res+=high*fact+low+1;
            else res+=(high+1)*fact;
            low+=cur*fact;
            cur=high%10;
            high/=10;
            fact*=10;
        }
        return res;
    }
```

[312. 戳气球](https://leetcode.cn/problems/burst-balloons/)

动态规划，从小的区间逐步扩展到大的区间，这个dp递推还是比较新的，第一次碰到

**关键洞察**：当戳破气球`i`时，获得的硬币为`nums[i-1] * nums[i] * nums[i+1]`。但戳破顺序会影响相邻关系，因此需要逆向思考——考虑**最后戳破的气球**。

```java
public int maxCoins(int[] nums) {
        int n = nums.length;
        int[] balloons = new int[n + 2];
        balloons[0] = balloons[n + 1] = 1; // 虚拟边界
        System.arraycopy(nums, 0, balloons, 1, n);
        
        // dp[i][j]表示戳破(i,j)区间内气球的最大收益
        int[][] dp = new int[n + 2][n + 2];
        
        for (int len = 1; len <= n; len++) { // 区间长度
            for (int i = 1; i + len - 1 <= n; i++) { // 区间起点
                int j = i + len - 1; // 区间终点
                for (int k = i; k <= j; k++) { // 最后戳破的气球
                    dp[i][j] = Math.max(dp[i][j],
                        balloons[i - 1] * balloons[k] * balloons[j + 1] 
                        + dp[i][k - 1] + dp[k + 1][j]);
                }
            }
        }
        return dp[1][n];
    }
```

[29. 两数相除](https://leetcode.cn/problems/divide-two-integers/)

位移法相除，有几个点，一个是最小值溢出，一个是要判断符号，一个是扩展成long，不然会超时死循环，位移法，逐渐增加二的倍数

![1756880042766](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1756880042766.png)

```java
class Solution {
    public int divide(int dividend, int divisor) {
        // 特殊情况处理
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE; // 防止溢出
        }

        // 符号处理
        boolean isNegative = (dividend < 0) ^ (divisor < 0); // 符号异或，判断结果是否为负
        long absDividend = Math.abs((long) dividend);
        long absDivisor = Math.abs((long) divisor);

        int result = 0;

        // 位移法计算商
        while (absDividend >= absDivisor) {
            long tempDivisor = absDivisor;
            int multiple = 1;

            // 将除数左移直到接近被除数
            while (absDividend >= (tempDivisor << 1)) {
                tempDivisor <<= 1;
                multiple <<= 1;
            }

            // 减去当前找到的倍数，并累加商
            absDividend -= tempDivisor;
            result += multiple;
        }

        return isNegative ? -result : result;
    }

}
```

[164. 最大间距](https://leetcode.cn/problems/maximum-gap/)

桶排序思想，把所有数据均匀放到桶里面，然后最大间距就是某个桶的最大值和下一个桶最小值的间距

```java
public int maximumGap(int[] nums) {
    // 边界情况处理
    if (nums.length < 2) return 0;
    // 1. 计算数组的最大值和最小值
    int min = Integer.MAX_VALUE;
    int max = Integer.MIN_VALUE;
    for (int num : nums) {
        min = Math.min(min, num);
        max = Math.max(max, num);
    }
    // 所有元素相等的情况 这个很重要不然三个0
    if (min == max) return 0;
    int n = nums.length;
    // 2. 计算桶大小（向上取整）
    int bucketSize = (max - min + n - 2) / (n - 1); // 等价于 ceil((max-min)/(n-1))
    // 3. 计算桶的数量
    int bucketCount = (max - min) / bucketSize + 1;
    // 4. 初始化桶（存储每个桶的最小值和最大值）
    int[] minBucket = new int[bucketCount];
    int[] maxBucket = new int[bucketCount];
    Arrays.fill(minBucket, Integer.MAX_VALUE);
    Arrays.fill(maxBucket, Integer.MIN_VALUE);
    // 5. 将元素分配到桶中
    for (int num : nums) {
        int bucketIndex = (num - min) / bucketSize;
        minBucket[bucketIndex] = Math.min(minBucket[bucketIndex], num);
        maxBucket[bucketIndex] = Math.max(maxBucket[bucketIndex], num);
    }
    // 6. 计算最大间距（相邻桶之间）
    int maxGap = 0;
    int prevMax = maxBucket[0]; // 前一个非空桶的最大值
    for (int i = 1; i < bucketCount; i++) {
        // 跳过空桶
        if (minBucket[i] == Integer.MAX_VALUE) continue;
        // 当前桶的最小值 - 前一个桶的最大值
        maxGap = Math.max(maxGap, minBucket[i] - prevMax);
        prevMax = maxBucket[i];
    }
    
    return maxGap;
}
```

[4. 寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

变种题，寻找两个正序数组的第K小的元素

二分法，时间复杂度log(m+n)

```java
public int findKthSortedArrays(int[] nums1, int[] nums2, int k) {
    return getKth(nums1, 0, nums1.length - 1, 
                 nums2, 0, nums2.length - 1, k);
}

private int getKth(int[] nums1, int start1, int end1,
                  int[] nums2, int start2, int end2, int k) {
    // 确保nums1是较短的数组
    int len1 = end1 - start1 + 1;
    int len2 = end2 - start2 + 1;
    if (len1 > len2) {
        return getKth(nums2, start2, end2, nums1, start1, end1, k);
    }
    // 边界条件1：nums1当前区间已无元素（len1=0）→ 直接从nums2取第k小元素
    // 索引计算：start2 + k - 1（因为nums2是正序，前k个元素的最后一个就是第k小）
    if (len1 == 0) {
        return nums2[start2 + k - 1];
    }
    // 边界条件2：k=1（寻找第1小元素）→ 直接返回两个数组当前区间的第一个元素的最小值
    // 原理：正序数组的第一个元素是当前区间最小，两者最小即为全局第1小
    if (k == 1) {
        return Math.min(nums1[start1], nums2[start2]);
    }
    
    // 比较两个数组的第k/2个元素
    int i = start1 + Math.min(len1, k / 2) - 1;
    int j = start2 + Math.min(len2, k / 2) - 1;
    if (nums1[i] > nums2[j]) {
        // 排除nums2的前j-start2+1个元素
        return getKth(nums1, start1, end1,
                     nums2, j + 1, end2, 
                     k - (j - start2 + 1));
    } else {
        // 排除nums1的前i-start1+1个元素
        return getKth(nums1, i + 1, end1,
                     nums2, start2, end2,
                     k - (i - start1 + 1));
    }
}
```

[面试题 16.25. LRU 缓存](https://leetcode.cn/problems/lru-cache-lcci/)

又来了LRU，有几个记忆点要记住，双向链表，头尾两个指针初始化，四个函数，一个加到最前面，一个删除当前节点，一个移动到最前面（删除当前节点+加到最前面），删除最后节点（删除尾节点前一个节点然后删除hm表中的数据）

```java
class LRUCache {
    class Node {
        int key, val;
        Node prev, next;
        Node() {}
        Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }

    private final int capacity;
    private final Map<Integer, Node> map;
    private final Node head, tail;
    private int size;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new HashMap<>();
        this.head = new Node();
        this.tail = new Node();
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        
        Node node = map.get(key);
        moveToHead(node);
        return node.val;
    }

    public void put(int key, int value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            node.val = value;
            moveToHead(node);
        } else {
            if (size == capacity) {
                removeTail();
                size--;
            }
            Node newNode = new Node(key, value);
            addToHead(newNode);
            map.put(key, newNode);
            size++;
        }
    }

    private void addToHead(Node node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(Node node) {
        removeNode(node);
        addToHead(node);
    }

    private void removeTail() {
        Node last = tail.prev;
        removeNode(last);
        map.remove(last.key);
    }
}
```

[836. 矩形重叠](https://leetcode.cn/problems/rectangle-overlap/)

矩形四条边底下两个取最大值，上面两个取最小值

```java
public boolean isRectangleOverlap(int[] rec1, int[] rec2) {
        int left=Math.max(rec1[0],rec2[0]);
        int bottom=Math.max(rec1[1],rec2[1]);
        int right=Math.min(rec1[2],rec2[2]);
        int top=Math.min(rec1[3],rec2[3]);
        int h=top-bottom,w=right-left;
        if(h<=0||w<=0) return false;
        else return true;
    }
```

[67. 二进制求和](https://leetcode.cn/problems/add-binary/)

求和方法就和正常数学计算一样，从最低位开始加，然后还有进位也要算进去

```java
 public String addBinary(String a, String b) {
        int add=0;
        int idxa=a.length()-1,idxb=b.length()-1;
        StringBuilder sb=new StringBuilder();
        while(idxa>=0||idxb>=0||add!=0)
        {
            int sum=add;
            if(idxa>=0&&a.charAt(idxa--)=='1')++sum;
            if(idxb>=0&&b.charAt(idxb--)=='1')++sum;
            add=sum/2;
            sb.insert(0,(char)(sum%2+'0'));
        }
        return sb.toString();
    }
```

[698. 划分为k个相等的子集](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)

回溯加剪枝

```java
	static boolean[] vis; // 全局访问标记数组
    public boolean canPartitionKSubsets(int[] nums, int k) {
        vis = new boolean[nums.length]; // 初始化访问数组
        Arrays.sort(nums);              // 升序排序数组
        int sum = 0;
        for(int num : nums) sum += num; // 计算数组总和
        if(sum % k != 0) return false;  // 无法整除直接返回
        // 开始回溯：从末尾开始尝试（因数组已升序排序）
        return track(nums, sum/k, k, nums.length-1, 0);
    }

    private static boolean track(int[] nums, int target, int k, int begin, int cursum) {
        if(k == 1) return true;         // 只剩1组必然成功
        if(cursum == target)            // 当前组满足条件
            return track(nums, target, k-1, nums.length-1, 0); // 重置搜索
        for(int i = begin; i >= 0; i--) { // 从后往前遍历（因已排序）
            if(vis[i]) continue;         // 跳过已用元素
            if(cursum + nums[i] > target) continue; // 剪枝：超过目标值
            vis[i] = true;               // 标记使用
            if(track(nums, target, k, i-1, cursum + nums[i]))
                return true;             // 递归搜索
            vis[i] = false;              // 回溯
            // 跳过相同元素（重要优化）
            while(i > 0 && nums[i] == nums[i-1]) i--;
        }
        return false;
    }
```

[581. 最短无序连续子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

找到两个不是升序的边界就可以

从左到右找，找到最后一个破坏升序的地方就是右边界，从右往左找，找到最后一个破坏升序的地方就是左边界

```java
public int findUnsortedSubarray(int[] nums) {
    int n = nums.length;
    
    // 正向遍历：找右边界
    int right = -1;
    int max = nums[0]; // 记录当前最大值
    for (int i = 1; i < n; i++) {
        if (nums[i] >= max) max = nums[i]; // 正常升序时更新max
        else right = i; // 出现降序时更新右边界
    }
    
    // 反向遍历：找左边界
    int left = -1;
    int min = nums[n-1]; // 记录当前最小值
    for (int i = n-2; i >= 0; i--) {
        if (nums[i] <= min) min = nums[i]; // 正常降序时更新min
        else left = i; // 出现升序时更新左边界
    }
    
    return (left == -1 || right == -1) ? 0 : right - left + 1; // 计算无序区间长度
}
```

[133. 克隆图](https://leetcode.cn/problems/clone-graph/)

深广搜再建立一遍就好了

```java
HashMap<Integer,Node> hm;
    public Node cloneGraph(Node node) {
        if(node==null) return null;
        hm=new HashMap<>();
        Node nodecopy=new Node(node.val);
        hm.put(node.val,nodecopy);
        dfs(node,nodecopy);
        return nodecopy;
    }

    void dfs(Node node,Node newnode)
    {
        for(Node n:node.neighbors)
        {
            if(!hm.containsKey(n.val))
            {
                Node temp=new Node(n.val);
                hm.put(n.val,temp);
                dfs(n,temp);
            }
            newnode.neighbors.add(hm.get(n.val));
        }
    }
```

[986. 区间列表的交集](https://leetcode.cn/problems/interval-list-intersections/)

双指针，左边界是两个区间左边界较大值，右边界是两个区间右边界较小值，哪个右边界小，哪个指针就往后移动一格

```java
public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        int i=0,j=0;
        ArrayList<int[]> al=new ArrayList<>();
        while(i<firstList.length&&j<secondList.length)
        {
            int left=Math.max(firstList[i][0],secondList[j][0]);
            int right;
            if(firstList[i][1]>secondList[j][1])
            {
                right=secondList[j][1];
                ++j;
            }else{
                right=firstList[i][1];
                ++i;
            }
            if(right>=left)
                al.add(new int[]{left,right});
        }
        int[][] res=new int[al.size()][2];
        for(int x=0;x<al.size();++x)
            res[x]=al.get(x);
        return res;
    }
```

[405. 数字转换为十六进制数](https://leetcode.cn/problems/convert-a-number-to-hexadecimal/)

注意一下无符号右移，如果是两条杠的话指的意思是有符号的右移，如果是负数会在左侧补1陷入死循环

注意一下最后还要翻转。

```java
public String toHex(int num) {
        if (num == 0) return "0";
        StringBuilder sb = new StringBuilder();
        while (num != 0) {
            int hex = num & 0xF;
            sb.append(hex < 10 ? (char)(hex + '0') : (char)(hex - 10 + 'a'));
            num >>>= 4;  // 注意是无符号右移
        }
        return sb.reverse().toString();
    }
```

![1757135617797](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1757135617797.png)

### 求某一个数组的小和  [快手为何频繁考察这道Leetcode里没有的题](https://mp.weixin.qq.com/s/rMsbcUf9ZPhvfRoyZGW6HA)

![1757136194822](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1757136194822.png)

```java
public class SmallSum {
    public static long getSmallSum(int[] arr) {
        if (arr == null || arr.length < 2) return 0;
        return process(arr, 0, arr.length - 1);
    }
    
    private static long process(int[] arr, int l, int r) {
        if (l == r) return 0;                            // 递归基：单元素无小和
        int mid = l + ((r - l) >> 1);                    // 防溢出取中点
        return process(arr, l, mid)                      // 左半区小和
             + process(arr, mid + 1, r)                 // 右半区小和  
             + merge(arr, l, mid, r);                   // 合并产生的小和
    }
    
    private static long merge(int[] arr, int l, int mid, int r) {
        int[] help = new int[r - l + 1];                 // 辅助数组
        int i = 0, p1 = l, p2 = mid + 1;
        long res = 0;                                    // 本次合并小和
        
        while (p1 <= mid && p2 <= r) {
            if (arr[p1] <= arr[p2]) {                   // 左组元素≤右组时
                res += (long) arr[p1] * (r - p2 + 1);   // 核心：累加小和
                help[i++] = arr[p1++];                   // 拷贝左组
            } else {
                help[i++] = arr[p2++];                   // 拷贝右组（无小和）
            }
        }
        
        while (p1 <= mid) help[i++] = arr[p1++];         // 剩余左组
        while (p2 <= r) help[i++] = arr[p2++];           // 剩余右组
        System.arraycopy(help, 0, arr, l, help.length);  // 回写原数组
        return res;
    }
}
```

[LCR 131. 砍竹子 I](https://leetcode.cn/problems/jian-sheng-zi-lcof/)

和数字乘积问题一样，数学可证都分成3是最大的，不过还有动态规划算法可以做

可以省略一半的遍历，只有两个情况要考虑，一个是当前较小的值保留不砍，剩下一半较大的砍，或者两段都不砍

```java
public int cuttingBamboo(int bamboo_len) {
        if(bamboo_len<2) return 0;
        int dp[]=new int[bamboo_len+1];
        for(int i=2;i<=bamboo_len;++i)
            for(int j=1;j<=i/2;++j)
                dp[i]=Math.max(dp[i],Math.max(j*dp[i-j],j*(i-j)));
        return dp[bamboo_len];
    }
```

[99. 恢复二叉搜索树](https://leetcode.cn/problems/recover-binary-search-tree/)

核心是找到第一个降序pre和最后一个降序root

**在BST恢复问题中，必须完整遍历整棵树才能确保找到所有错误节点**。不能提前返回

```java
TreeNode pre,n1,n2;
    public void recoverTree(TreeNode root) {
        ftravel(root);
        int temp=n1.val;
        n1.val=n2.val;
        n2.val=temp;
    }

    void ftravel(TreeNode root)
    {
        if(root.left!=null) ftravel(root.left);
        if(pre!=null&&root.val<pre.val)
        {
            if(n1==null)
                n1=pre;
            n2=root;
        }
        pre=root;
        if(root.right!=null) ftravel(root.right);
    }
```

[137. 只出现一次的数字 II](https://leetcode.cn/problems/single-number-ii/)

使用变量记录出现的次数，这是出现了三次

```java
public int singleNumber(int[] nums) {
        // ones和twos分别记录出现一次和两次的位
        int ones = 0, twos = 0;
        for (int num : nums) {
            // 更新twos：当前位是1的数位(它之前已经出现在ones中)
            twos |= ones & num;
            // 更新ones：保存当前位是1但还没有出现两次的数位
            ones ^= num;
            // common_bit_mask保存ones和twos中都为1的位，这表示这些位上的数字出现了3次
            int common_bit_mask = ~(ones & twos);
            // 清除ones和twos中已经出现3次的位
            ones &= common_bit_mask;
            twos &= common_bit_mask;
        }
        // ones保留了只出现一次的数字
        return ones;
    }
```

[30. 串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)

滑动窗口，除了滑动窗口还有很多要思考的细节

```java
public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        int wordLength = words[0].length(); // 每个单词的长度
        int wordCount = words.length; // 单词总数
        int totalLength = wordLength * wordCount; // 需要匹配的总长度
        // 统计每个单词在 words 中出现的次数
        Map<String, Integer> wordMap = new HashMap<>();
        for (String word : words) {
            wordMap.put(word, wordMap.getOrDefault(word, 0) + 1);
        }
        // 遍历 s 中的每个可能的起点（按单词长度滑动），注意是wordlength的长度可以找到所有可能
        for (int i = 0; i < wordLength; i++) {
            int left = i;
            int right = i;
            Map<String, Integer> currentMap = new HashMap<>();
            int matchedWords = 0;   
            // 滑动窗口
            while (right + wordLength <= s.length()) {
                // 取出当前窗口的单词
                String currentWord = s.substring(right, right + wordLength);
                right += wordLength;//别忘记加上了
                if (wordMap.containsKey(currentWord)) {
                    currentMap.put(currentWord, currentMap.getOrDefault(currentWord, 0) + 1);
                    matchedWords++;
                    // 如果当前单词次数超出需要的次数，调整左边界
                    while (currentMap.get(currentWord) > wordMap.get(currentWord)) {
                        String leftWord = s.substring(left, left + wordLength);
                        currentMap.put(leftWord, currentMap.get(leftWord) - 1);
                        matchedWords--;
                        left += wordLength;
                    }
                    // 如果匹配到所有单词
                    if (matchedWords == wordCount) {
                        res.add(left);
                    }
                } else {
                    // 如果遇到不在 words 里的单词，直接重置窗口
                    currentMap.clear();
                    matchedWords = 0;
                    left = right;
                }
            }
        }
        return res;
    }
```

[1293. 网格中的最短路径](https://leetcode.cn/problems/shortest-path-in-a-grid-with-obstacles-elimination/)

BFS，剪枝，注意剩余次数存的是visited数组，这样不同次数的相同节点可以再次入队

```java
public int shortestPath(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;  // 获取网格的行数和列数
        // 如果允许跳过的障碍物数量k大于等于从起点到终点的最短路径所需的障碍物数量，则直接返回最短路径
        if (k >= m + n - 3) return m + n - 2;
        // 初始化队列q，用于存储每一步的当前位置和剩余的k值
        Deque<int[]> q = new ArrayDeque<>();
        q.offer(new int[] {0, 0, k});  // 将起点添加到队列中
        boolean[][][] vis = new boolean[m][n][k + 1];  // 3D数组，记录每个位置和每个剩余k值是否已访问
        vis[0][0][k] = true;  // 起点位置已访问，剩余的k值为k
        int ans = 0;  // 记录当前步数
        // 方向数组，用于表示上下左右四个方向的移动
        int[] dirs = {-1, 0, 1, 0, -1};
        // 使用广度优先搜索（BFS），直到队列为空或找到终点
        while (!q.isEmpty()) {
            ans++;  // 每次遍历一层，步数加1
            for (int i = q.size(); i > 0; i--) {  // 遍历当前队列中的所有节点
                int[] p = q.poll();  // 获取队列中的当前节点，p[0]是x坐标，p[1]是y坐标，p[2]是剩余的k值
                k = p[2];  // 更新当前剩余的k值
                // 遍历四个方向
                for (int j = 0; j < 4; j++) {
                    int x = p[0] + dirs[j];  // 计算当前方向的x坐标
                    int y = p[1] + dirs[j + 1];  // 计算当前方向的y坐标
                    // 如果当前位置有效（不越界）
                    if (x >= 0 && x < m && y >= 0 && y < n) {
                        // 如果到达终点，返回当前步数
                        if (x == m - 1 && y == n - 1) {
                            return ans;
                        }
                        // 如果当前位置是空地并且没有访问过，加入队列
                        if (grid[x][y] == 0 && !vis[x][y][k]) {
                            q.offer(new int[] {x, y, k});  // 将新的状态加入队列
                            vis[x][y][k] = true;  // 标记为已访问
                        }
                        // 如果当前位置是障碍物且剩余的k值大于0，且没有访问过，允许跳过障碍
                        else if (grid[x][y] == 1 && k > 0 && !vis[x][y][k - 1]) {
                            q.offer(new int[] {x, y, k - 1});  // 跳过障碍，k值减1
                            vis[x][y][k - 1] = true;  // 标记为已访问
                        }
                    }
                }
            }
        }
        return -1;  // 如果无法到达终点，返回-1
    }
```

[1206. 设计跳表](https://leetcode.cn/problems/design-skiplist/)

```java
class Skiplist {
    // 常量定义
    private static final int MAX_LEVEL = 32;   // 跳表的最大层数限制
    private static final double P = 0.25;      // 节点晋升到更高层的概率
    private static final Random RANDOM = new Random(); // 随机数生成器
    
    // 跳表数据结构
    private final Node head = new Node(-1, MAX_LEVEL); // 头节点（哨兵节点），值为-1，拥有最大层数
    private int level = 0;                      // 当前跳表实际使用的最大层数

    // 跳表节点内部类
    static class Node {
        int val;        // 节点存储的值
        Node[] next;    // 每层的后继指针数组
        
        Node(int val, int level) {
            this.val = val;
            this.next = new Node[level]; // 根据层数创建指针数组
        }
    }

    // 构造函数
    public Skiplist() {
        // 初始化时不需要特殊操作
    }

    /**
     * 搜索目标值是否存在于跳表中
     * @param target 要搜索的值
     * @return 是否存在
     */
    public boolean search(int target) {
        Node curr = head; // 从头节点开始搜索
        // 从最高层开始向下搜索
        for (int i = level - 1; i >= 0; --i) {
            // 找到当前层最接近target的节点
            curr = findClosest(curr, i, target);
            // 检查下一个节点是否等于target
            if (curr.next[i] != null && curr.next[i].val == target) {
                return true;
            }
        }
        return false;
    }

    /**
     * 向跳表中添加一个新值
     * @param num 要添加的值
     */
    public void add(int num) {
        Node curr = head;
        int lv = randomLevel();  // 随机生成新节点的层数
        Node node = new Node(num, lv); // 创建新节点
        level = Math.max(level, lv);   // 更新当前最大层数
        
        // 从最高层开始处理插入
        for (int i = level - 1; i >= 0; --i) {
            curr = findClosest(curr, i, num);
            // 只在小于新节点层数的层级插入
            if (i < lv) {
                node.next[i] = curr.next[i]; // 新节点接管后继
                curr.next[i] = node;         // 前驱指向新节点
            }
        }
    }

    /**
     * 从跳表中删除一个值
     * @param num 要删除的值
     * @return 是否成功删除
     */
    public boolean erase(int num) {
        Node curr = head;
        boolean deleted = false;
        // 从最高层开始查找并删除
        for (int i = level - 1; i >= 0; --i) {
            curr = findClosest(curr, i, num);
            // 如果找到目标节点则删除
            if (curr.next[i] != null && curr.next[i].val == num) {
                curr.next[i] = curr.next[i].next[i]; // 跳过目标节点
                deleted = true;
            }
        }
        // 清理空层（如果高层已经没有节点）
        while (level > 1 && head.next[level - 1] == null) {
            --level;
        }
        return deleted;
    }

    /**
     * 辅助方法：找到level层中，小于target的最大节点
     * @param curr 当前节点
     * @param level 要搜索的层级
     * @param target 目标值
     * @return 最接近的节点
     */
    private Node findClosest(Node curr, int level, int target) {
        // 在当前层级向右移动，直到找到大于等于target的节点
        while (curr.next[level] != null && curr.next[level].val < target) {
            curr = curr.next[level];
        }
        return curr;
    }

    /**
     * 随机生成节点层数（按概率P递增）
     * @return 节点的随机层数
     */
    private static int randomLevel() {
        int level = 1;
        // 以概率P增加层数
        while (level < MAX_LEVEL && RANDOM.nextDouble() < P) {
            ++level;
        }
        return level;
    }
}
```

[451. 根据字符出现频率排序](https://leetcode.cn/problems/sort-characters-by-frequency/)

选择数据结构，选择hashmap统计字符频率或者使用二维数组 [128] [2]来统计，再写一个排序规则就好

```java
public String frequencySort(String s) {
        // 1. 统计字符频率（支持Unicode）
        Map<Character, Integer> freqMap = new HashMap<>();
        for (char c : s.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }
        
        // 2. 按频率排序（频率相同按字符自然序）
        List<Character> chars = new ArrayList<>(freqMap.keySet());
        chars.sort((a, b) -> {
            int freqCompare = freqMap.get(b) - freqMap.get(a);
            return freqCompare != 0 ? freqCompare : a - b;
        });
        
        // 3. 构建结果字符串
        StringBuilder sb = new StringBuilder();
        for (char c : chars) {
            sb.append(String.valueOf(c).repeat(freqMap.get(c)));
        }
        return sb.toString();
    }
```

二维数组哈希统计

```java
public String frequencySort(String s) {
    // 创建一个128x2的数组，用于统计字符频率
    // freqs[i][0]存储字符的ASCII码，freqs[i][1]存储对应字符的出现次数
    int[][] freqs = new int[128][2];
    // 初始化数组，将每个字符的ASCII码存入第一列
    for(int i = 0; i < 128; i++) {
        freqs[i][0] = i;  // freqs[i][0] = ASCII码值
    }
    // 统计每个字符的出现次数
    for(char ch: s.toCharArray()) {
        freqs[ch][1]++;  // freqs[ch][1]存储字符ch的出现次数
    }
    // 对数组进行排序：
    // 1. 首先按出现次数降序排列（b[1]-a[1]）
    // 2. 如果出现次数相同，则按ASCII码升序排列（a[0]-b[0]）
    Arrays.sort(freqs, (a,b)->{
        return a[1]!=b[1] ? b[1]-a[1] : a[0]-b[0];
    });
    // 构建结果字符串
    StringBuilder sb = new StringBuilder();
    for(int i = 0; i < 128; i++){
        char c = (char)freqs[i][0];  // 获取字符
        int k = freqs[i][1];         // 获取该字符的出现次数
        while(k-->0) sb.append(c);   // 按出现次数追加字符
    }
    return sb.toString();
}
```

[889. 根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)

找到规律，后序的倒数第二个值就是右子树的子节点，也就是前序左子树最后一个节点的下一个值

```java
public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        return build(preorder,postorder,0,preorder.length-1,0,postorder.length-1);
    }

    TreeNode build(int[] preorder, int[] postorder,int prel,int prer,int postl,int postr)
    {
        if(prel>prer) return null;
        TreeNode now=new TreeNode(preorder[prel]);
        if(prel==prer) return now;
        int leftr=postorder[postr-1];
        int i;
        for(i=prel+1;i<=prer;++i)
            if(preorder[i]==leftr) break;
        int leftlen=i-prel-1;
        now.left=build(preorder,postorder,prel+1,i-1,postl,postl+leftlen-1);
        now.right=build(preorder,postorder,i,prer,postl+leftlen,postr-1);
        return now;
    }
```

[1171. 从链表中删去总和值为零的连续节点](https://leetcode.cn/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)

```java
public ListNode removeZeroSumSublists(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        Map<Integer, ListNode> prefixSumMap = new HashMap<>();
        int prefixSum = 0;
        prefixSumMap.put(0, dummy);  // 虚拟头节点处理从头开始消除的情况
        // 第一次遍历：记录前缀和与节点的映射
        for (ListNode curr = dummy; curr != null; curr = curr.next) {
            prefixSum += curr.val;
            prefixSumMap.put(prefixSum, curr);  // 相同前缀和会覆盖，保留最后出现的位置
        }
        // 第二次遍历：跳过和为0的区间
        prefixSum = 0;
        for (ListNode curr = dummy; curr != null; curr = curr.next) {
            prefixSum += curr.val;
            curr.next = prefixSumMap.get(prefixSum).next;  // 直接跳到相同前缀和的下一个节点
        }
        return dummy.next;
    }
```

[928 · 最多有两个不同字符的最长子串 - LintCode](https://www.lintcode.com/problem/928/)

滑动窗口词频统计秒了

```java
public int lengthOfLongestSubstringTwoDistinct(String s) {
        HashMap<Character,Integer> hm=new HashMap<>();
        char[]str=s.toCharArray();
        int maxlen=0;
        int left=0,right=0;
        for(;right<str.length;++right)
        {
            char c=str[right];
            if(hm.containsKey(c))
                hm.put(c,hm.get(c)+1);
            else{
                while(hm.size()>=2)
                {
                    char l=str[left++];
                    int cnt=hm.get(l);
                    if(cnt==1) hm.remove(l);
                    else hm.put(l,cnt-1);
                }
                hm.put(c,1);
            }
            //注意最后再比较一下maxlen，不然a这种用例通不过
            maxlen=Math.max(maxlen,right-left+1);
        }
        return maxlen;
    }
```

[849 · 基础计算器 III - LintCode](https://www.lintcode.com/problem/849/)

```java
	Deque<Integer> nums = new ArrayDeque<>();  // 数字栈
    Deque<Character> ops = new ArrayDeque<>();  // 运算符栈

    public int calculate(String s) {
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == ' ') continue;  // 跳过空格
            if (Character.isDigit(c)) {  // 处理数字
                int num = 0;
                while (i < s.length() && Character.isDigit(s.charAt(i))) {
                    num = num * 10 + (s.charAt(i++) - '0');
                }
                i--;  // 回退多读的字符
                nums.push(num);
            } else if (c == '(') {  // 左括号直接入栈
                ops.push(c);
            } else if (c == ')') {  // 右括号触发计算
                while (ops.peek() != '(') calc();
                ops.pop();  // 弹出左括号
            } else {  // 处理运算符
                while (!ops.isEmpty() && priority(ops.peek()) >= priority(c)) {
                    calc();
                }
                ops.push(c);
            }
        }
        while (!ops.isEmpty()) calc();  // 处理剩余运算
        return nums.pop();
    }

    // 执行栈顶运算
    private void calc() {
        int b = nums.pop(), a = nums.pop();
        char op = ops.pop();
        nums.push(
                op == '+' ? a + b :
                        op == '-' ? a - b :
                                op == '*' ? a * b :
                                        a / b  // 默认整除
        );
    }

    // 定义运算符优先级
    private int priority(char op) {
        return op == '*' || op == '/' ? 2 :
                op == '+' || op == '-' ? 1 :
                        0;  // 括号优先级最低
    }
```

[LCR 190. 加密运算](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

![1757417233969](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1757417233969.png)

不使用四则运算完成加法，算法解析是这样的

```java
public int encryptionCalculate(int dataA, int dataB) {
        while(dataB != 0) { // 当进位为 0 时跳出
            int c = (dataA & dataB) << 1;  // c = 进位
            dataA ^= dataB; // dataA = 非进位和
            dataB = c; // dataB = 进位
        }
        return dataA;
    }
```

[LCR 180. 文件组合](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)

滑动窗口，左和右分别是数字边界，当相等的时候存完了千万别忘记左移一格

```java
public int[][] fileCombination(int target) {
        int l=1,r=2;
        ArrayList<int[]> res=new ArrayList<>();
        int sum=l+r;
        while(l<r)
        {
            if(sum<target)
                sum+= ++r;
            else if(sum>target)
                sum-= l++;
            else{
                int []t=new int[r-l+1];
                for(int i=l;i<=r;++i)
                    t[i-l]=i;
                res.add(t);
                sum-= l++;//这个别忘了
            }
        }
        return res.toArray(new int[res.size()][]);
    }
```

[321. 拼接最大数](https://leetcode.cn/problems/create-maximum-number/)

```java
public int[] maxNumber(int[] nums1, int[] nums2, int k) {
    int m = nums1.length, n = nums2.length;
    int[] result = new int[k];
    // 遍历所有可能的分配方案（nums1取i个，nums2取k-i个）
    for (int i = Math.max(0, k - n); i <= Math.min(k, m); i++) {
        // 从nums1中取i个数字组成最大子序列
        int[] sub1 = getMaxSequence(nums1, i);
        // 从nums2中取k-i个数字组成最大子序列
        int[] sub2 = getMaxSequence(nums2, k - i);
        // 合并两个子序列
        int[] merged = mergeSequences(sub1, sub2);
        // 保留最大的合并结果
        if (compareSequences(merged, 0, result, 0) > 0) {
            result = merged;
        }
    }
    return result;
}

// 从数组中选出k个数字组成最大子序列（单调栈实现）
private int[] getMaxSequence(int[] nums, int k) {
    int[] stack = new int[k];
    int top = -1; // 栈顶指针
    int canRemove = nums.length - k; // 可移除的数字数量
    for (int num : nums) {
        // 维护单调递减栈（栈顶元素小于当前元素时弹出）
        while (top >= 0 && stack[top] < num && canRemove > 0) {
            top--;
            canRemove--;
        }
        if (top < k - 1) {
            stack[++top] = num;
        } else {
            canRemove--; // 栈已满，跳过当前数字
        }
    }
    return stack;
}

//    private int[] getMaxSubsequence(int[] nums, int k) {
//        Deque<Integer> deque = new ArrayDeque<>();
//        int canRemove = nums.length - k; // 还可以移除的数字数量
//        for (int num : nums) {
//            // 当队列不为空，且队尾数字小于当前数字，且还能移除数字时
//            while (!deque.isEmpty() && deque.peekLast() < num && canRemove > 0) {
//                deque.removeLast(); // 移除队尾较小的数字
//                canRemove--;
//            }
//            // 如果队列中数字不足k个，加入当前数字
//            if (deque.size() < k) {
//                deque.addLast(num);
//            }
//            // 队列已满时，跳过当前数字（但要减少可移除数量）
//            else {
//                canRemove--;
//            }
//        }
//        // 将Deque转为数组返回
//        int[] result = new int[k];
//        int idx = 0;
//        for (int num : deque) {
//            result[idx++] = num;
//        }
//        return result;
//    }

// 合并两个子序列（类似归并排序）
private int[] mergeSequences(int[] seq1, int[] seq2) {
    int m = seq1.length, n = seq2.length;
    int[] merged = new int[m + n];
    int i = 0, j = 0, idx = 0;
    while (i < m && j < n) {
        // 选择当前更大的数字（比较字典序）
        merged[idx++] = compareSequences(seq1, i, seq2, j) > 0 ? 
                        seq1[i++] : seq2[j++];
    }
    // 处理剩余元素
    while (i < m) merged[idx++] = seq1[i++];
    while (j < n) merged[idx++] = seq2[j++];
    return merged;
}

// 比较两个序列的字典序
private int compareSequences(int[] seq1, int i, int[] seq2, int j) {
    while (i < seq1.length && j < seq2.length) {
        int diff = seq1[i] - seq2[j];
        if (diff != 0) return diff;
        i++; j++;
    }
    return (seq1.length - i) - (seq2.length - j); // 较长的序列更大
}
```

[722. 删除注释](https://leetcode.cn/problems/remove-comments/)

一个字符一个字符读进来，遇到行注释跳过剩余部分，遇到块注释往后找结束标记，如果不是在块注释并且当前行不空加入这行的信息

```java
public List<String> removeComments(String[] source) {
        List<String> result = new ArrayList<>();
        boolean inBlockComment = false; // 是否在块注释中
        StringBuilder currentLine = new StringBuilder();
        
        for (String line : source) {
            int i = 0;
            int n = line.length();
            while (i < n) {
                if (!inBlockComment) {
                    // 处理行注释
                    if (i + 1 < n && line.charAt(i) == '/' && line.charAt(i + 1) == '/') {
                        break; // 跳过行剩余部分
                    }
                    // 处理块注释开始
                    else if (i + 1 < n && line.charAt(i) == '/' && line.charAt(i + 1) == '*') {
                        inBlockComment = true;
                        i += 2;
                    }
                    // 普通字符
                    else {
                        currentLine.append(line.charAt(i++));
                    }
                } else {
                    // 在块注释中，寻找结束标记
                    if (i + 1 < n && line.charAt(i) == '*' && line.charAt(i + 1) == '/') {
                        inBlockComment = false;
                        i += 2;
                    } else {
                        i++;
                    }
                }
            }
            
            // 如果不是在块注释中且当前行不为空，添加到结果
            if (!inBlockComment && currentLine.length() > 0) {
                result.add(currentLine.toString());
                currentLine = new StringBuilder(); // 重置
            }
        }
        
        return result;
    }
```

[381. O(1) 时间插入、删除和获取随机元素 - 允许重复](https://leetcode.cn/problems/insert-delete-getrandom-o1-duplicates-allowed/)

一个动态数组，一个map存值和对应的下标集合，还有一个随机数要生成，取size随机值在动态数组访问就行

```java
class RandomizedCollection {
        List<Integer> nums; // 用于存储所有元素的列表，可以包含重复元素
        Map<Integer, Set<Integer>> indices; // 用于记录每个元素在 nums 中出现的索引，元素值 -> 索引集合
        Random random; // 用于生成随机数，获取随机元素
        public RandomizedCollection() {
            nums = new ArrayList<>(); // 初始化 nums 列表，用于存储元素
            indices = new HashMap<>(); // 初始化 indices 映射，用于存储每个元素值与其索引的关系
            random = new Random(); // 初始化随机数生成器
        }

        // 插入一个值到集合，返回是否是第一次插入该值
        public boolean insert(int val) {
            // first 判断是否是第一次插入该值，如果是首次插入返回 true，否则返回 false
            boolean first = !indices.containsKey(val);
            // 如果 val 不存在，则需要在 indices 中为它初始化一个 HashSet
            // 使用 computeIfAbsent 确保每个值都能有一个对应的索引集合
            indices.computeIfAbsent(val, k -> new HashSet<>()).add(nums.size());
            // 将 val 插入到 nums 的末尾
            nums.add(val);
            // 返回是否是首次插入
            return first;
        }

        // 删除一个值的一个实例，返回是否成功（即值是否存在）
        public boolean remove(int val) {
            // 从 indices 中获取 val 对应的索引集合
            Set<Integer> set = indices.get(val);
            // 如果 set 为 null 或者 set 为空，说明该值不存在，返回 false
            if (set == null || set.isEmpty()) return false;
            // 1) 从 set 中选一个索引位置（可以是任意一个）
            int removeIdx = set.iterator().next();
            // 获取 nums 中最后一个元素的索引和该值
            int lastIdx = nums.size() - 1;
            int lastVal = nums.get(lastIdx);
            // 2) 将最后一个元素移到 removeIdx 位置
            nums.set(removeIdx, lastVal);
            // 3) 关键操作：首先从 val 的索引集合中移除 removeIdx
            set.remove(removeIdx);
            // 4) 更新 lastVal 的索引集合
            Set<Integer> lastSet = indices.get(lastVal);
            lastSet.add(removeIdx); // 如果 lastVal 不是删除的元素，它的索引需要添加到 removeIdx
            lastSet.remove(lastIdx); // 删除 lastIdx 对应的旧位置，因为它已经被移到 removeIdx
            // 5) 如果 val 的索引集合为空，说明 val 已经没有剩余的实例了，移除该元素的记录
            if (set.isEmpty()) {
                indices.remove(val);
            }
            // 6) 从 nums 中删除最后一个元素（即我们交换过来的元素的原位置）
            nums.remove(lastIdx);
            return true;
        }
    
        // 获取一个随机元素
        public int getRandom() {
            // 从 nums 中随机获取一个索引并返回对应的值
            return nums.get(random.nextInt(nums.size()));
        }
    }
```

[求解立方根_牛客题霸_牛客网](https://www.nowcoder.com/practice/caf35ae421194a1090c22fe223357dca)

二分查找注意right的初始化

```java
static double lifanggen(double x)
    {
        if(x<0) return -lifanggen(-x);
        if(x==0||x==1) return x;
        double left=0,right=Math.max(1,x),mid=0;
        while(right-left>0.01)
        {
            mid=left+(right-left)/2;
            double mult=mid*mid*mid;
            if(mult>x)
                right=mid;
            else if(mult<x)
                left=mid;
            else return mid;
        }
        return Math.round(mid * 10.0) / 10.0;
    }
```

[214. 最短回文串](https://leetcode.cn/problems/shortest-palindrome/)

主要使用了kmp算法，对于回文串，我们给的策略是逆向遍历，如果最前面的下标和最后面的下标值相等那两边都往中间移一格

```java
// 计算最短回文串
    public String shortestPalindrome(String s) { 
        int n = s.length();
        int[] next = getNext(s);  // 获取 next 数组
        int best = 0;  // 记录最长匹配前缀的长度
        // 从后往前遍历，尝试找到最大匹配的前缀
        for(int i = n - 1; i >= 0; i--){
            if(best > 0 && s.charAt(best) != s.charAt(i)){
                best = next[best - 1];  // 如果不匹配，跳到 next 数组里记录的下一个位置
            }
            if(s.charAt(i) == s.charAt(best)){
                best++;  // 如果匹配，扩展 best
            }
        }
        // best 记录了最长匹配的前缀长度，反转剩余部分并拼接到原字符串前
        StringBuilder ans = new StringBuilder(s.substring(best)).reverse();
        ans.append(s);  // 拼接回原字符串
        return ans.toString();
    }

    // 构建 next 数组
    public int[] getNext(String s){
        int n = s.length();
        int[] next = new int[n];
        int j = -1;  // 初始值改为 -1
        for (int i = 0; i < n; i++) {  // 从 0 开始遍历
            // 当前字符与匹配位置的字符不相等
            while (j >= 0 && s.charAt(i) != s.charAt(j)) {
                j = next[j];  // 跳到 next 数组里记录的前缀位置
            }
            // 当前字符与匹配位置的字符相等
            if (s.charAt(i) == s.charAt(j)) {
                j++;  // 增加匹配长度
            }
            next[i] = j;  // 更新 next 数组的值
        }
        return next;
    }
```

[559. N 叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-n-ary-tree/)

BFS很容易了，别忘记还有递归的方式

```java
public int maxDepth(Node root) {
        if (root == null) return 0;
        int depth = 0;
        if (root.children != null){
            for (Node child : root.children){
                depth = Math.max(depth, maxDepth(child));
            }
        }
        return depth + 1; //中节点
    }
```

[57. 插入区间](https://leetcode.cn/problems/insert-interval/)

贪心策略，除了重叠情况还有三种，已经加过新区间，在新区间左边，在新区间右边，重叠的时候动态选择当前新区间范围

```java
public int[][] insert(int[][] intervals, int[] newInterval) {
        // 处理空输入情况
        if (intervals.length == 0) {
            return new int[][]{newInterval};
        }
        List<int[]> result = new ArrayList<>();
        boolean isMerged = false; // 标记newInterval是否已合并
        for (int[] curr : intervals) {
            if (isMerged) {
                // 如果已经合并过，直接添加剩余区间
                result.add(curr);
            }
            // 当前区间在newInterval左侧且无重叠
            else if (curr[1] < newInterval[0]) {
                result.add(curr);
            }
            // 当前区间在newInterval右侧且无重叠
            else if (curr[0] > newInterval[1]) {
                result.add(newInterval);
                result.add(curr);
                isMerged = true;
            }
            // 有重叠需要合并
            else {
                newInterval[0] = Math.min(curr[0], newInterval[0]);
                newInterval[1] = Math.max(curr[1], newInterval[1]);
            }
        }
        // 如果遍历完仍未合并（newInterval在最右侧）
        if (!isMerged) 
            result.add(newInterval);
        return result.toArray(new int[result.size()][]);
    }
```

[140. 单词拆分 II](https://leetcode.cn/problems/word-break-ii/)

回溯，哈希表查当前字串是不是包含在里面

```java
HashSet<String> hs;
    List<String> res=new ArrayList<>();
    StringBuilder sb=new StringBuilder();
    public List<String> wordBreak(String s, List<String> wordDict) {
        hs=new HashSet<>(wordDict);
        travel(s,0);
        return res;
    }

    void travel(String s,int nowidx)
    {
        if(nowidx==s.length())
        {
            sb.deleteCharAt(sb.length()-1);
            res.add(sb.toString());
            return;
        }
        for(int i=nowidx+1;i<=s.length();++i)
        {
            String t=s.substring(nowidx,i);
            if(hs.contains(t))
            {
                sb.append(t);
                sb.append(' ');
                travel(s,i);
                if(i==s.length())
                    sb.setLength(sb.length()-t.length());
                else sb.setLength(sb.length()-t.length()-1);
            }
        }
    }
```

[273. 整数转换英文表示](https://leetcode.cn/problems/integer-to-english-words/)

```java
// 定义个位、十到十九、十位、以及千的单位
    String[] singles = {"", "One", "Two", "Three", "Four", "Five", "Six", "Seven", "Eight", "Nine"};
    String[] teens = {"Ten", "Eleven", "Twelve", "Thirteen", "Fourteen", "Fifteen", "Sixteen", "Seventeen", "Eighteen", "Nineteen"};
    String[] tens = {"", "Ten", "Twenty", "Thirty", "Forty", "Fifty", "Sixty", "Seventy", "Eighty", "Ninety"};
    String[] thousands = {"", "Thousand", "Million", "Billion"}; // 仅支持到十亿
    
    public String numberToWords(int num) {
        // 特殊情况：0的处理
        if (num == 0) {
            return "Zero";
        }
        // 用于拼接结果的 StringBuffer
        StringBuffer sb = new StringBuffer();
        // 按单位从大到小处理，单位分别是十亿、百万、千、和个位
        for (int i = 3, unit = 1000000000; i >= 0; i--, unit /= 1000) {
            // 获取当前单位的数值（例如，百万位上的数）
            int curNum = num / unit;
            if (curNum != 0) { // 如果当前单位的值不为 0
                num -= curNum * unit; // 减去已经处理的部分
                StringBuffer curr = new StringBuffer();
                // 递归处理当前块（小于 1000 的部分）
                recursion(curr, curNum);
                // 添加当前块的单位
                curr.append(thousands[i]).append(" ");
                // 拼接到最终结果中
                sb.append(curr);
            }
        }
        // 去除多余空格并返回
        return sb.toString().trim();
    }
    
    // 递归处理每个三位数块（小于 1000 的数字）
    public void recursion(StringBuffer curr, int num) {
        if (num == 0) {
            return; // 如果是 0，直接返回
        } else if (num < 10) {
            // 如果是个位，直接从 singles 中取值
            curr.append(singles[num]).append(" ");
        } else if (num < 20) {
            // 如果是 10 到 19，直接从 teens 中取值
            curr.append(teens[num - 10]).append(" ");
        } else if (num < 100) {
            // 如果是 20 到 99，处理十位，再递归处理个位
            curr.append(tens[num / 10]).append(" ");
            recursion(curr, num % 10);
        } else {
            // 如果是 100 到 999，处理百位，再递归处理余下的部分
            curr.append(singles[num / 100]).append(" Hundred ");
            recursion(curr, num % 100);
        }
    }
```

[767. 重构字符串](https://leetcode.cn/problems/reorganize-string/)

哈希表存字符频率，优先队列排序最大频率，主要是构建字符的时候要贪心，取最大的两个元素，然后频率减一再放进去

```java
public String reorganizeString(String s) {
        HashMap<Character,Integer> hm=new HashMap<>();
        char[] str=s.toCharArray();
        for(char c:str)
            hm.put(c, hm.getOrDefault(c,0)+1);
        PriorityQueue<int[]> pq=new PriorityQueue<>((a,b)->b[1]-a[1]);
        for(Map.Entry<Character,Integer> en:hm.entrySet())
            pq.add(new int[]{en.getKey(),en.getValue()});
        if(pq.peek()[1]>s.length()/2+1) return "";
        StringBuilder sb=new StringBuilder();
        while(pq.size()>=2)
        {
            int[] num1=pq.poll();
            int num2[]=pq.poll();
            sb.append((char)num1[0]);
            sb.append((char)num2[0]);
            if(num1[1]!=1)
                pq.add(new int[]{num1[0],num1[1]-1});
            if(num2[1]!=1)
                pq.add(new int[]{num2[0],num2[1]-1});
        }
        if(pq.size()!=0)
        {
            int[] now=pq.poll();
            if(now[1]>1) return "";
            sb.append((char)now[0]);
        }
        return sb.toString();
    }
```

[875. 爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

```java
public int minEatingSpeed(int[] piles, int h) {
        // 最小可能速度（每小时至少吃1根香蕉）
        int low = 1;
        // 最大可能速度（每小时最多需要吃掉最大堆的香蕉数）
        int high = 0;
        // 遍历所有香蕉堆，找到最大的堆作为初始上限
        for (int pile : piles) {
            high = Math.max(high, pile);
        }
        // 初始化结果为最大可能速度（最坏情况）
        int k = high;
        // 二分查找最佳速度
        while (low < high) {
            // 计算中间速度（防止整数溢出）
            int speed = (high - low) / 2 + low;
            // 计算以当前速度吃完所有香蕉需要的时间
            long time = getTime(piles, speed);
            if (time <= h) {
                // 如果时间充足，尝试更小的速度
                k = speed;       // 更新当前最佳速度
                high = speed;    // 调整上限
            } else {
                // 如果时间不足，需要更快的速度
                low = speed + 1; // 调整下限
            }
        }
        return k;
    }

    /**
     * 计算以给定速度吃完所有香蕉需要的时间
     * @param piles 香蕉堆数组
     * @param speed 每小时吃的香蕉数
     * @return 总耗时（向上取整）
     */
    public long getTime(int[] piles, int speed) {
        long time = 0;
        for (int pile : piles) {
            // 计算当前堆的耗时（使用向上取整技巧）
            // 等同于 Math.ceil(pile / (double)speed)
            int curTime = (pile + speed - 1) / speed;
            time += curTime;
        }
        return time;
    }
```

[猿辅导、美团、字节面试题——双栈排序](https://mp.weixin.qq.com/s/g_AqwsSEUwlRSevnStPkEA)

双栈排序，类似插入排序，大于当前元素的移回主栈，副栈的所有元素都是有序的从栈顶到栈底递减顺序

```java
public static void stackSort(Stack<Integer> mainStack) {
        Deque<Integer> tempStack = new ArrayDeque<>();
        while (!mainStack.isEmpty()) {
            // 1. 从主栈弹出当前元素
            int current = mainStack.pop();
            // 2. 将辅助栈中所有大于current的元素移回主栈
            while (!tempStack.isEmpty() && tempStack.peek() > current) {
                mainStack.push(tempStack.pop());
            }
            // 3. 将current插入到正确位置
            tempStack.push(current);
        }
        // 4. 将排序结果转移回主栈
        while (!tempStack.isEmpty()) {
            mainStack.push(tempStack.pop());
        }
    }
```

[421. 数组中两个数的最大异或值](https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/)

```java
public int findMaximumXOR(int[] nums) {
        int res = 0;  // 存储最终的结果，初始化为0
        HashSet<Integer> set = new HashSet<>();  // 存储经过处理的数字的高位部分，避免重复计算
        int max = 0;  // 记录数组中的最大值
        // 1. 找出数组中的最大值，最大值用来确定最高有效位
        for (int n : nums) {
            max = Math.max(max, n);  // 获取nums数组中的最大值
        }
        // 2. 计算最高有效位的位数（从0开始计数）
        // Integer.numberOfLeadingZeros(max) 返回 max 的二进制表示中前导0的个数
        // 因此，max 的最高有效位的位置就是 31 - leadingZeros(max)
        int high = 31 - Integer.numberOfLeadingZeros(max);  // 获取 max 的最高有效位的索引
        // 3. 从最高位到最低位逐位确定异或值
        // 逐位确定最大异或值：我们尝试从最高位开始，逐位判断是否可以将当前位设为1。
        for (int i = high; i >= 0; i--) {
            // 3.1 尝试将当前位设为1，构建期望的目标值
            // 通过将 res 和当前位的值（1 << i）按位“或”运算，得到当前位设置为1的期望结果
            int want = res | (1 << i);  // 当前位的目标值，res 保持已经确定的部分，当前位尝试设置为 1
            set.clear();  // 清空 set，准备存储新的数字的高位部分
            // 3.2 处理每个数字
            for (int n : nums) {
                // 3.2.1 保留数字的高位部分（i 位及以上的位）
                // 通过右移操作将数字 n 移到 i 位以下（清除低位），然后再左移回去，保留高位部分
                int maskedNum = (n >> i) << i;  // 取数字 n 的高 i 位，并将低位清零
                set.add(maskedNum);  // 将高位部分存储到 set 中
                // 3.2.2 检查是否存在满足 a ^ b = want 的数字对
                // 如果存在某个数字与当前数字异或后的结果等于目标值 want，那么可以确定当前位是 1
                // 这样就能更新 res，跳出内层循环
                if (set.contains(maskedNum ^ want)) {
                    res = want;  // 如果找到了符合条件的数字对，就更新 res
                    break;  // 退出内层循环，当前位可以确定为 1
                }
            }
        }
        return res;  // 返回最大异或值
    }
```

[LCR 185. 统计结果概率](https://leetcode.cn/problems/nge-tou-zi-de-dian-shu-lcof/)

非常巧妙，通过类似斐波那契数列的一层层向上找概率可以得到答案

```java
public double[] statisticsProbability(int num) {
    // 1. 初始化一个单个骰子的概率。每个点数（1到6）的概率都是1/6
    double pre[] = {1 / 6d, 1 / 6d, 1 / 6d, 1 / 6d, 1 / 6d, 1 / 6d}; // pre 数组表示骰子1的点数概率
    // 2. 从第二个骰子开始，逐个添加骰子，直到 num 个骰子
    for (int i = 2; i <= num; i++) {
        // 3. 计算当前 num 个骰子时，所有点数的概率
        // 数组 tmp 存储的是掷 i 个骰子的所有可能的点数概率（点数范围从 i 到 6*i）
        double tmp[] = new double[5 * i + 1];  // i 个骰子的点数从 i 到 6*i，所以需要一个大小为 5*i + 1 的数组
        // 4. 对于每个已经存在的点数概率（pre 数组中的值），计算它和新掷的一个骰子的所有可能结果的组合
        for (int j = 0; j < pre.length; j++) {
            for (int x = 0; x < 6; x++) {
                // 5. 更新当前点数 j+x 的概率：每个点数 j 经过增加一个骰子后，可以变成 j + 1 到 j + 6 之间的任意点数
                tmp[j + x] += pre[j] / 6;  // 计算更新后的概率：pre[j] 是原来 j 点数的概率，/6 是考虑新骰子的每一个点数的概率
            }
        }
        // 6. 更新 pre 数组，准备进行下一轮的计算（加入下一个骰子）
        pre = tmp;  // tmp 存储了 i 个骰子的点数概率，赋值给 pre 以备下次迭代
    }
    // 7. 返回最终的概率数组 pre，包含 num 个骰子的每个点数的概率
    return pre;  // pre 数组包含 num 个骰子后，每个点数的概率
}

```

[1146. 快照数组](https://leetcode.cn/problems/snapshot-array/)

设计一个什么样的数据结构可以达到这个效果呢？建一个List<int[]>[] snapshots; 这是第一次碰到这个数据结构

```java
class SnapshotArray {
    List<int[]>[] arr;//建立length大小的List数组里面对应下标存对应快照和存的值
    int snap_id;//当前snapid
    public SnapshotArray(int length) {
        this.arr = new List[length];
        this.snap_id = 0;
        for (int i = 0; i < length; i++) {
            arr[i] = new ArrayList<int[]>();//对应每个下标的存对应快照和存的值
        }
    }
    
    public void set(int index, int val) {
        arr[index].add(new int[]{snap_id, val});//在对应下标动态数组存修改值
    }
    
    public int snap() {
        return snap_id ++;
    }
    
    public int get(int index, int snap_id) {
        List<int[]> list = arr[index];
        if (list.size() == 0) {//说明没有被修改过
            return 0;
        }
        int left = -1, right = list.size() - 1;//为什么left从-1开始，有可能下标0的快照都比要查的快照版本高说明那个版本还没有被修改
        while (left < right) {
            int mid = (left + right + 1) / 2;//记得加1
            if (list.get(mid)[0] <= snap_id) {
                left = mid;
            } else {
                right = mid - 1;
            }
        }
        if (left == -1) {
            return 0;
        }
        return list.get(left)[1];
    }
}
```

[LCR 155. 将二叉搜索树转化为排序的双向链表](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

原地改动，看一眼就好

```java
Node pre,head;
    public Node treeToDoublyList(Node root) {
        if(root==null) return null;
        travel(root);
        head.left=pre;
        pre.right=head;
        return head;
    }

    void travel(Node root)
    {
        if(root==null) return;
        travel(root.left);
        if(pre==null)
            head=root;
        else{
            pre.right=root;
            root.left=pre;
        }
        pre=root;
        travel(root.right);
    }
```

[1044. 最长重复子串](https://leetcode.cn/problems/longest-duplicate-substring/)

难的想死，受不了了

```java
import java.util.*;

public class Solution {
    // 存储每个位置的哈希值，用于快速判断子串的哈希值
    private long[] h;
    // 存储每个位置的幂值 P^i
    private long[] p;
    // P 是哈希基数，选择一个大质数以减少哈希冲突
    private static final int P = 13131;

    // 判断是否存在长度为 len 的重复子串
    public int exist(String s, int len) {
        // 使用 HashSet 来存储已出现过的子串哈希值
        Set<Long> set = new HashSet<>();
        // 遍历字符串的所有长度为 len 的子串
        for (int i = 0; i + len <= s.length(); i++) {
            // 计算当前子串 s[i..i+len-1] 的哈希值
            long t = h[i + len] - h[i] * p[len];
            // 如果没有找到重复的哈希值，就将当前哈希值加入集合
            if (!set.contains(t)) {
                set.add(t);
            } else {
                // 如果找到了重复的哈希值，说明存在重复子串，返回该子串的起始索引
                return i;
            }
        }
        // 如果没有找到重复子串，返回 -1
        return -1;
    }

    public String longestDupSubstring(String s) {
        // 初始化哈希值数组和幂值数组
        h = new long[s.length() + 1];
        p = new long[s.length() + 1];
        p[0] = 1;  // p[0] 表示 P 的 0 次幂，初始为 1
        // 计算每个字符的哈希值和幂值
        for (int i = 0; i < s.length(); i++) {
            p[i + 1] = p[i] * P;  // 更新 p[i] = P^i
            h[i + 1] = h[i] * P + s.charAt(i);  // 更新哈希值：h[i + 1] = h[i] * P + s[i]
        }
        // 使用二分查找来查找最长的重复子串长度
        int right = s.length(), left = 0;
        int start = -1;  // 保存最长重复子串的起始位置
        // 二分查找：查找最大重复子串长度
        while (left <= right) {
            int mid = (right + left) / 2;  // 当前子串长度 mid
            // 调用 exist 方法判断是否存在长度为 mid 的重复子串
            int flag = exist(s, mid);
            if (flag != -1) {  // 如果找到了重复子串
                left = mid + 1;  // 尝试更长的子串
                start = flag;  // 更新最长重复子串的起始位置
            } else {
                right = mid - 1;  // 尝试更短的子串
            }
        }
        // 如果找到了重复子串，返回该子串，否则返回空字符串
        return start == -1 ? "" : s.substring(start, start + right);
    }
}

```

[895. 最大频率栈](https://leetcode.cn/problems/maximum-frequency-stack/)

主要是三个，hm对应数字频率哈希，group对应频率和元素集合哈希，以及最大频率值

```java
class FreqStack {
    // 记录每个数字的出现频率
    // key: 数字, value: 该数字当前的出现次数
    HashMap<Integer, Integer> freq;
    
    // 记录相同频率的数字集合
    // key: 频率次数, value: 该频率下的数字栈（使用双端队列模拟栈）
    // 注意：使用Deque的push/pop方法实现栈的LIFO特性
    HashMap<Integer, Deque<Integer>> group;
    
    // 记录当前最大频率值
    int maxFreq;

    // 构造函数：初始化数据结构
    public FreqStack() {
        freq = new HashMap<>();  // 初始化频率表
        group = new HashMap<>(); // 初始化分组表
        maxFreq = 0;             // 初始化最大频率为0
    }

    // 压栈操作
    public void push(int val) {
        // 1. 获取并更新当前数字的频率
        // getOrDefault(val, 0)：如果val不存在则返回0
        int fre = freq.getOrDefault(val, 0) + 1;
        freq.put(val, fre);  // 更新频率表
        
        // 2. 更新最大频率
        maxFreq = Math.max(fre, maxFreq);
        
        // 3. 将数字加入对应频率的栈
        // computeIfAbsent：如果不存在该频率的栈则新建
        // 使用ArrayDeque作为栈实现（比Stack性能更好）
        group.computeIfAbsent(fre, z -> new ArrayDeque<>()).push(val);
    }

    // 弹栈操作
    public int pop() {
        // 1. 获取当前最大频率对应的栈顶元素
        // 注意：这里直接使用pop()方法，会同时移除该元素
        int x = group.get(maxFreq).pop();
        
        // 2. 更新该元素的频率
        freq.put(x, freq.get(x) - 1);
        
        // 3. 检查并更新maxFreq
        // 循环条件：当前maxFreq对应的栈为空
        // 需要递减maxFreq直到找到非空栈或降到0
        while (maxFreq > 0 && group.get(maxFreq).isEmpty()) {
            maxFreq--;
        }
        
        return x;  // 返回弹出的元素
    }
}
```

[918. 环形子数组的最大和](https://leetcode.cn/problems/maximum-sum-circular-subarray/)

```java
public int maxSubarraySumCircular(int[] nums) {
    // 初始化变量：
    // totalSum - 数组所有元素的总和
    // maxSum - 最大子数组和（非环形情况）
    // minSum - 最小子数组和（用于计算环形情况）
    // currentMax - 当前连续子数组的最大和
    // currentMin - 当前连续子数组的最小和
    int totalSum = 0, maxSum = nums[0], minSum = nums[0];
    int currentMax = 0, currentMin = 0;
    
    // 遍历数组中的每个元素
    for (int num : nums) {
        // 计算当前连续子数组的最大和：
        // 比较"将当前数字加入之前的子数组"和"从当前数字重新开始"两种情况
        currentMax = Math.max(currentMax + num, num);
        // 更新全局最大子数组和
        maxSum = Math.max(maxSum, currentMax);
        // 计算当前连续子数组的最小和：
        // 比较"将当前数字加入之前的子数组"和"从当前数字重新开始"两种情况
        currentMin = Math.min(currentMin + num, num);
        // 更新全局最小子数组和
        minSum = Math.min(minSum, currentMin);
        // 累加计算数组总和
        totalSum += num;
    }
    
    // 特殊情况处理：
    // 如果数组全为负数（maxSum < 0），则直接返回maxSum
    // 否则，比较两种情况：
    // 1. 常规情况的最大子数组和（maxSum）
    // 2. 环形情况的最大子数组和（totalSum - minSum）
    return (maxSum > 0) ? Math.max(maxSum, totalSum - minSum) : maxSum;
}
```

[面试字节前必看的高频题——木头切割问题](https://mp.weixin.qq.com/s/FQma0bdAWbzLMmCKhZRk7w)

二分法找就好了，1到max长度

```java
/**
 * 计算能切割出至少k段的最大长度
 * @param woods 木材长度数组
 * @param k 需要切割的段数
 * @return 最大切割长度（无法满足时返回0）
 */
public static int maxCutLength(int[] woods, int k) {
    // 边界检查：无效输入直接返回0
    if (woods == null || woods.length == 0 || k <= 0) return 0;
    
    // 初始化二分查找边界
    int left = 1;            // 最小切割长度至少为1
    int right = 0;           // 最大可能长度为木材最大值
    
    // 确定右边界（所有木材中的最大长度）
    for (int wood : woods) {
        right = Math.max(right, wood);
    }
    
    // 二分查找核心逻辑
    while (left < right) {
        // 注意+1防止死循环（向上取整）
        int mid = left + (right - left + 1) / 2;
        
        // 检查当前长度是否能切出足够段数
        if (canCut(woods, mid, k)) {
            left = mid;      // 满足条件，尝试更大的长度
        } else {
            right = mid - 1; // 不满足，减小长度
        }
    }
    
    // 最终验证：确保left确实满足条件
    return canCut(woods, left, k) ? left : 0;
}

/**
 * 检查是否能以指定长度切割出至少k段
 * @param woods 木材数组
 * @param length 尝试的切割长度
 * @param k 目标段数
 * @return 是否满足要求
 */
private static boolean canCut(int[] woods, int length, int k) {
    int count = 0;
    for (int wood : woods) {
        count += wood / length;
        // 提前终止优化：已满足条件立即返回
        if (count >= k) return true;
    }
    return count >= k;
}
```

[1233. 删除子文件夹](https://leetcode.cn/problems/remove-sub-folders-from-the-filesystem/)

排序一下，当前文件夹如果是前一个保留的文件夹的前缀的话就是它的子文件夹

```java
public List<String> removeSubfolders(String[] folder) {
        // 1. 将文件夹按字典序排序
        Arrays.sort(folder);
        List<String> result = new ArrayList<>();
        // 初始时添加第一个文件夹
        result.add(folder[0]);
        for (int i = 1; i < folder.length; i++) {
            String prev = result.get(result.size() - 1);
            String curr = folder[i];
            // 2. 检查当前文件夹是否是前一个保留文件夹的子文件夹
            if (!curr.startsWith(prev + "/")) {
                result.add(curr);
            }
        }
        return result;
    }
```

[480. 滑动窗口中位数](https://leetcode.cn/problems/sliding-window-median/)

使用两个堆来完成，用例大会超时但是为了理解就背这个版本的吧

```java
public double[] medianSlidingWindow(int[] nums, int k) {
        // 小根堆（存储较大的一半元素）
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        // 大根堆（存储较小的一半元素）
        // 使用自定义比较器避免整数溢出问题
        PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>((a, b) -> a < b ? 1 : -1);
        int n = nums.length;
        double[] ans = new double[n - k + 1]; // 结果数组
        // 初始化第一个窗口
        for (int i = 0; i < k; i++) {
            maxHeap.add(nums[i]); // 先将所有元素加入大根堆
        }
        // 平衡两个堆，使大根堆比小根堆最多多1个元素
        for (int i = 0; i < k / 2; i++) {
            minHeap.add(maxHeap.poll()); // 将大根堆的多余元素转移到小根堆
        }
        // 计算第一个窗口的中位数
        if (k % 2 == 0) {
            // 偶数个元素：取两个堆顶的平均值
            ans[0] = ((long)minHeap.peek() + maxHeap.peek()) / 2.0;
        } else {
            // 奇数个元素：取大根堆的堆顶
            ans[0] = maxHeap.peek();
        }
        // 滑动窗口处理后续元素
        for (int i = k; i < n; i++) {
            int del = nums[i - k]; // 要移除的元素（窗口最左边）
            int add = nums[i];     // 要添加的元素（窗口最右边）
            // 移除窗口最左边的元素
            if (!maxHeap.isEmpty() && del <= maxHeap.peek()) {
                maxHeap.remove(del); // 如果在大根堆中则移除
            } else {
                minHeap.remove(del); // 否则在小根堆中移除
            }
            // 添加新元素到合适的堆
            if (!maxHeap.isEmpty() && add <= maxHeap.peek()) {
                maxHeap.add(add); // 如果小于等于大根堆顶，加入大根堆
            } else {
                minHeap.add(add); // 否则加入小根堆
            }
            // 平衡两个堆的大小
            int t = maxHeap.size() - minHeap.size();
            if (t > 1) {
                // 大根堆元素过多，转移一个到小根堆
                minHeap.add(maxHeap.poll());
            } else if (t < 0) {
                // 小根堆元素过多，转移一个到大根堆
                maxHeap.add(minHeap.poll());
            }
            // 计算当前窗口的中位数
            if (k % 2 == 0) {
                ans[i - k + 1] = ((long)minHeap.peek() + maxHeap.peek()) / 2.0;
            } else {
                ans[i - k + 1] = maxHeap.peek();
            }
        }
        return ans;
    }
```

[1027. 最长等差数列](https://leetcode.cn/problems/longest-arithmetic-subsequence/)

最长等差子数组长度，动态规划，看用例只用到了500以内的，那么两个数之间的差值基本上为-500到500之间，所以设置1001个数，然后在算插值的时候统一下标

```java
public int longestArithSeqLength(int[] nums) {
        int dp[][]=new int[nums.length][1001];
        int res=0;
        for(int i=1;i<nums.length;++i)
        {
            for(int j=0;j<i;++j)
            {
                int interval=nums[i]-nums[j]+500;
                dp[i][interval]=dp[j][interval]+1;
                res=Math.max(res,dp[i][interval]);
            }
        }
        return res+1;
    }
```

[1658. 将 x 减到 0 的最小操作数](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/)

逆向思维，因为取的是两边的值，那么我们直接算出总和然后减去目标值就是中间剩下的长度，用滑动窗口算出最长长度然后总长度减去就好。

```Java
public int minOperations(int[] nums, int x) {
        // 初始化滑动窗口的左右指针
        int left = 0, right = 0;
        // 计算数组总和
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        // 目标值：总和减去x，即需要找到的子数组和
        int target = sum - x;
        // 记录满足条件的最大子数组长度
        int maxLength = Integer.MIN_VALUE;
        // 当前窗口的和
        int windowSum = 0;
        // 滑动窗口遍历数组
        while (right < nums.length) {
            // 扩展右边界，将当前元素加入窗口
            windowSum += nums[right];
            right++;
            // 当窗口和超过目标值时，收缩左边界
            while (windowSum > target && left < right) {
                windowSum -= nums[left];
                left++;
            }
            // 如果窗口和等于目标值，更新最大长度
            if (windowSum == target) {
                maxLength = Math.max(maxLength, right - left);
            }
        }
        // 如果没有找到满足条件的子数组，返回-1
        // 否则返回需要删除的元素个数（总长度减去子数组长度）
        return maxLength == Integer.MIN_VALUE ? -1 : nums.length - maxLength;
    }
```

[1280. 学生们参加各科测试的次数](https://leetcode.cn/problems/students-and-examinations/)

内连接，逗号连接，至展示满足条件的项，左连接，显示前一个表的所有项，右边没有的显示空，full join显示全部，没有填null，cross join笛卡儿积，self join 自连接起别名就好，natural join自动匹配连接

```sql
select Students.student_id, student_name,Subjects.subject_name,count(Examinations.subject_name) as attended_exams
from Students 
cross join Subjects
left join Examinations
on Students.student_id = Examinations.student_id 
and Subjects.subject_name = Examinations.subject_name
group by student_id,subject_name
order by student_id,subject_name
```

[703. 数据流中的第 K 大元素](https://leetcode.cn/problems/kth-largest-element-in-a-stream/)

建立一个最小堆，然后维护堆的大小是k就可以，堆顶就是返回值

```java
class KthLargest {

        PriorityQueue<Integer> pq;
        int ca;
        public KthLargest(int k, int[] nums) {
            pq=new PriorityQueue<>();
            ca=k;
            for(int num:nums)
                add(num);
        }

        public int add(int val) {
            pq.add(val);
            if(pq.size()>ca)
                pq.poll();
            return pq.peek();
        }
    }
```

[1109. 航班预订统计](https://leetcode.cn/problems/corporate-flight-bookings/)

差分数组，第一次见到这种结构

![1758194375256](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1758194375256.png)

```java
public int[] corpFlightBookings(int[][] bookings, int n) {
    // 初始化差分数组（多开1位方便处理r+1）
    int[] diff = new int[n + 2];  // diff[0]未使用，diff[1..n]有效
    // 处理每个预订记录
    for (int[] booking : bookings) {
        int l = booking[0];      // 起始航班
        int r = booking[1];      // 结束航班
        int inc = booking[2];    // 预订座位数
        // 在l处标记增加
        diff[l] += inc;
        // 在r+1处标记减少（如果不超过边界）
        if (r + 1 <= n) {
            diff[r + 1] -= inc;
        }
    }
    // 通过差分数组计算原数组
    int[] answer = new int[n];
    answer[0] = diff[1];  // 第一个航班没有前驱
    for (int i = 1; i < n; i++) {
        answer[i] = answer[i - 1] + diff[i + 1];
    }
    return answer;
}
```

[LCR 160. 数据流中的中位数](https://leetcode.cn/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

要注意的是每次加入新元素不是只加入最大堆就好的，要和最大堆堆顶比较是放最大堆还是最小堆，然后平衡，让最大堆始终大于最小堆

```java
class MedianFinder {
    PriorityQueue<Integer> maxheap, minheap; // 存储较大的一半（升序）
    int count; // 统计添加的数字个数

    public MedianFinder() {
        maxheap = new PriorityQueue<>((a,b)-> b-a); // 大顶堆
        minheap = new PriorityQueue<>(); // 小顶堆
        count = 0;
    }

    public void addNum(int num) {
        // 保证maxheap.peek() <= minheap.peek()
        if (maxheap.isEmpty() || num <= maxheap.peek()) {
            maxheap.offer(num); // 小值放入maxheap
        } else {
            minheap.offer(num); // 大值放入minheap
        }

        // 严格平衡：maxheap的大小最多比minheap大1
        if (maxheap.size() > minheap.size() + 1) {
            minheap.offer(maxheap.poll());
        } else if (minheap.size() > maxheap.size()) {
            maxheap.offer(minheap.poll());
        }
        count++; // 更新数字个数
    }

    public double findMedian() {
        // 如果个数是偶数，返回两个堆顶元素的平均值；否则返回maxheap的堆顶
        if (count % 2 == 0) {
            return (maxheap.peek() + minheap.peek()) / 2.0;
        } else {
            return (double) maxheap.peek();
        }
    }
}

```

[求区间最小数乘区间和的最大值](https://mp.weixin.qq.com/s/UFv7pt_djjZoK_gzUBrRXA)

前缀和，算区间和，单调递增栈，每次遇到更小的元素就可以计算栈顶当前元素左边比他小的距离和右边的距离了，左边下标是比他小的元素，当前的i是右边比它小的元素，先弹出栈顶，距离就是当前下标-新的栈顶-1，就是前缀和相减

```java
public int maxValue(int[] nums) {
            // 获取数组长度
            int n = nums.length;
            // 创建单调栈，用于存储索引
            Deque<Integer> stack = new ArrayDeque<>();
            // 创建前缀和数组，用于快速计算区间和
            int[] sum = new int[n + 1];
            for (int i = 0; i < n; i++) {
                sum[i + 1] = sum[i] + nums[i];  // sum[i]表示前i个数的和
            }
            int max = 0;  // 初始化最大值为0
            // 遍历数组
            for (int i = 0; i < n; i++) {
                // 保证栈中元素是单调递增的
                // 如果栈顶元素大于当前元素，则需要计算栈顶元素的贡献并弹出栈顶元素
                while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                    // 弹出栈顶元素作为区间最小值
                    int minValueIndex = stack.pop();
                    // 计算该最小值的左右边界
                    int left = stack.isEmpty() ? -1 : stack.peek();
                    int right = i;
                    // 计算区间和，区间为[左边界+1, 右边界-1]，利用前缀和数组计算
                    int currentSum = sum[right] - sum[left + 1];
                    // 更新最大值
                    max = Math.max(max, nums[minValueIndex] * currentSum);
                }
                // 当前元素入栈
                stack.push(i);
            }
            // 处理栈中剩余的元素
            while (!stack.isEmpty()) {
                int minValueIndex = stack.pop();
                int left = stack.isEmpty() ? -1 : stack.peek();
                int right = n;  // 右边界为数组的末尾
                // 计算区间和
                int currentSum = sum[right] - sum[left + 1];
                // 更新最大值
                max = Math.max(max, nums[minValueIndex] * currentSum);
            }
            // 返回最大值
            return max;
        }
```

[1438. 绝对差不超过限制的最长连续子数组](https://leetcode.cn/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/)

双单调栈加滑动窗口，两个单调栈分别存最大值和最小值，如果超过阈值，left++

```Java
public int longestSubarray(int[] nums, int limit) {
        Deque<Integer> maxDeque = new ArrayDeque<>(); // 单调递减（队首最大）
        Deque<Integer> minDeque = new ArrayDeque<>();  // 单调递增（队首最小）
        int left = 0, res = 0;
        
        for (int right = 0; right < nums.length; right++) {
            // 维护maxDeque的单调性
            while (!maxDeque.isEmpty() && nums[right] > maxDeque.peekLast()) {
                maxDeque.pollLast();
            }
            maxDeque.offerLast(nums[right]);
            
            // 维护minDeque的单调性
            while (!minDeque.isEmpty() && nums[right] < minDeque.peekLast()) {
                minDeque.pollLast();
            }
            minDeque.offerLast(nums[right]);
            
            // 检查窗口合法性
            while (maxDeque.peekFirst() - minDeque.peekFirst() > limit) {
                if (nums[left] == maxDeque.peekFirst()) maxDeque.pollFirst();
                if (nums[left] == minDeque.peekFirst()) minDeque.pollFirst();
                left++;
            }
            
            res = Math.max(res, right - left + 1);
        }
        return res;
    }
```

[237. 删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/)

金蝉脱壳法，不知道前驱节点的各个信息，那么只需要把后面所有节点的值往前移一个就可以了

```Java
public void deleteNode(ListNode node) {
        ListNode ne=node.next;
        while(ne.next!=null)
        {
            node.val=ne.val;
            node=node.next;
            ne=ne.next;
        }
        node.val=ne.val;
        node.next=null;
    }
```

[859 · 最大栈 - LintCode](https://www.lintcode.com/problem/859/)

```java
class MaxStack {
        private Deque<Integer> stack;      // 主栈存储所有元素
        private Deque<Integer> maxStack;   // 单调栈存储当前最大值
        public MaxStack() {
            stack = new LinkedList<>();
            maxStack = new LinkedList<>();
        }
        public void push(int x) {
            stack.push(x);
            // 维护单调栈（非严格递减）
            if (maxStack.isEmpty() || x >= maxStack.peek()) {
                maxStack.push(x);
            }
        }
        public int pop() {
            int val = stack.pop();
            // 同步移除单调栈中的对应值
            if (val == maxStack.peek()) {
                maxStack.pop();
            }
            return val;
        }
        public int top() {
            return stack.peek();
        }
        public int peekMax() {
            return maxStack.peek();
        }
        public int popMax() {
            int max = maxStack.pop();
            Deque<Integer> temp = new ArrayDeque<>();
            // 从主栈中找出最大值
            while (stack.peek() != max) {
                temp.push(stack.pop());
            }
            stack.pop(); // 移除最大值
            // 将临时栈元素重新压回主栈
            while (!temp.isEmpty()) {
                push(temp.pop()); // 重新计算单调栈
            }
            return max;
        }
    }
```

[Python中文数字(str)转换成阿拉伯数字(int) - 简书](https://www.jianshu.com/p/ab16e5af5c32)

```java
import java.util.HashMap;
import java.util.Map;

public class ChineseNumberConverter {
    private static final Map<Character, Integer> digitMap = new HashMap<>();
    private static final Map<Character, Integer> unitMap = new HashMap<>();
    
    static {
        // 初始化数字映射
        digitMap.put('零', 0);
        digitMap.put('一', 1);
        digitMap.put('二', 2);
        digitMap.put('两', 2);
        digitMap.put('三', 3);
        digitMap.put('四', 4);
        digitMap.put('五', 5);
        digitMap.put('六', 6);
        digitMap.put('七', 7);
        digitMap.put('八', 8);
        digitMap.put('九', 9);
        
        // 初始化单位映射
        unitMap.put('十', 10);
        unitMap.put('百', 100);
        unitMap.put('千', 1000);
        unitMap.put('万', 10000);
        unitMap.put('亿', 100000000);
    }

    public static int chineseToArabic(String chineseNum) {
        int total = 0;
        int current = 0;
        int prevUnit = 1;
        
        // 从右向左处理字符串
        for (int i = chineseNum.length() - 1; i >= 0; i--) {
            char c = chineseNum.charAt(i);
            
            if (digitMap.containsKey(c)) {
                current += digitMap.get(c);
            } else if (unitMap.containsKey(c)) {
                int unit = unitMap.get(c);
                if (unit > prevUnit) {
                    total += current * unit;
                    current = 0;
                } else {
                    current *= unit;
                }
                prevUnit = unit;
            }
        }
        
        return total + current;
    }

    // 测试用例
    public static void main(String[] args) {
        String[] testCases = {
            "一百二十三", "一万二千三百四十五",
            "一亿二千三百四十五万六千七百八十九",
            "三千零五", "两百", "十", "二十五"
        };
        
        int[] expected = {
            123, 12345, 123456789, 3005, 200, 10, 25
        };
        
        for (int i = 0; i < testCases.length; i++) {
            int result = chineseToArabic(testCases[i]);
            System.out.printf("输入：%s | 输出：%d | %s\n",
                testCases[i], result,
                result == expected[i] ? "正确" : "错误");
        }
    }
}
```

[面试题 16.06. 最小差](https://leetcode.cn/problems/smallest-difference-lcci/)

注意边界条件转成long就好

```java
public int smallestDifference(int[] a, int[] b) {
        Arrays.sort(a);
        Arrays.sort(b);
        int l=0,r=0;
        long res=Integer.MAX_VALUE;
        while(l<a.length&&r<b.length)
        {
            if(a[l]>b[r])
                res=Math.min((long)a[l]-b[r++],res);
            else res=Math.min((long)b[r]-a[l++],res);
        }
        return (int)res;
    }
```

[873. 最长的斐波那契子序列的长度](https://leetcode.cn/problems/length-of-longest-fibonacci-subsequence/)

这个递推关系比较新奇，就是以i和j为最后两个数字的最长的长度

```java
ublic int lenLongestFibSubseq(int[] arr) {
        int n = arr.length, ans = 0;  // n为数组的长度，ans用于存储最终的结果，初始化为0。
        // 使用 HashMap 存储数组中每个元素的索引
        Map<Integer, Integer> idxMap = new HashMap<>(n);
        for (int i = 0; i < n; i++) {
            idxMap.put(arr[i], i);  // 将数组元素作为键，索引作为值存储
        }
        // dp[i][j]表示以 arr[i] 和 arr[j] 为前两个数的最长斐波那契子序列的长度
        int[][] dp = new int[n][n];
        // 遍历所有可能的 arr[i] 和 arr[j] 作为前两个元素的组合
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                dp[i][j] = Math.max(dp[i][j], 2);  // 最短的斐波那契子序列长度为2（由arr[i]和arr[j]组成）
                // 计算 arr[i] 和 arr[j] 的和，作为下一个可能的斐波那契数
                int nxt = arr[i] + arr[j];
                // 如果目标值 n 是数组中存在的数
                if (idxMap.containsKey(nxt)) {
                    int k = idxMap.get(nxt);  // 获取目标值的索引
                    // 更新 dp[j][k]，表示以 arr[j] 和 arr[k] 为前两个元素的最长斐波那契子序列的长度
                    dp[j][k] = dp[i][j] + 1;
                    // 更新答案，保留最大值
                    ans = Math.max(ans, dp[j][k]);
                }
            }
        }
        // 返回最终结果
        return ans;
    }
```

[382. 链表随机节点](https://leetcode.cn/problems/linked-list-random-node/)

米奇妙妙屋的随机算法，每一次getrandom都需要O(n)的时间，需要遍历一次整个链表，假设有四个节点刚好选了第三个的概率就是第一个第二个无所谓1*1 *(1/3) *(3/4)还是四分之一，前后会相互抵消，取第一个节点的概率是(1/2) *(2/3) *(3/4)还是1/4

```java
public class Solution {
        private ListNode head;
        private Random random;
        public Solution(ListNode head) {
            this.head = head;  // 初始化链表头
            this.random = new Random();  // 用于生成随机数
        }
        public int getRandom() {
            ListNode current = head;
            int result = current.val;  // 初始选择第一个节点
            int index = 1;  // 从第一个节点开始，index 表示当前是第几个节点
            while (current != null) {
                // 随机生成一个小于 index 的整数
                if (random.nextInt(index) == 0) {
                    result = current.val;  // 以 1/index 的概率选择当前节点
                }
                current = current.next;  // 遍历下一个节点
                index++;  // 递增节点索引
            }
            return result;  // 返回最终选中的节点值
        }
    }
```

[781. 森林中的兔子](https://leetcode.cn/problems/rabbits-in-forest/)

数学题，找到规律就好，四个人报出了3就可以抵消掉加四个人，再多一个人就是另外一个颜色了

```java
public int numRabbits(int[] answers) {
        HashMap<Integer,Integer> hm=new HashMap<>();
        for(int ans:answers)
            hm.put(ans,hm.getOrDefault(ans,0)+1);
        int res=0;
        for(int k:hm.keySet())
        {
            while(hm.get(k)>k+1)
            {
                res+=k+1;
                hm.put(k,hm.get(k)-(k+1));
            }
            if(hm.get(k)>0) res+=k+1;
        }
        return res;
    }
```

[652. 寻找重复的子树](https://leetcode.cn/problems/find-duplicate-subtrees/)

不要使用硬递归的方式，可以采用序列化左右子树然后比较，如果出现过两次就加入set

```java
// 用于存储子树的序列化结果
    private Map<String, Integer> map = new HashMap<>();
    // 用于存储重复的子树
    private Set<TreeNode> res = new HashSet<>();
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        // 从根节点开始遍历树
        serialize(root);
        // 返回重复的子树
        return new ArrayList<>(res);
    }
    // 使用递归方法进行序列化和查找重复子树
    private String serialize(TreeNode node) {
        if (node == null) {
            return "#";  // null 节点的标识符
        }
        // 序列化当前子树
        String left = serialize(node.left);  // 左子树序列化
        String right = serialize(node.right);  // 右子树序列化
        // 当前节点的序列化形式
        String subtree = left + "," + right + "," + node.val;
        // 检查该子树是否已出现过
        map.put(subtree, map.getOrDefault(subtree, 0) + 1);
        // 如果该子树出现了两次，说明是重复的，添加到结果中
        if (map.get(subtree) == 2) {
            res.add(node);
        }
        return subtree;  // 返回当前节点的序列化结果
    }
```

[870. 优势洗牌](https://leetcode.cn/problems/advantage-shuffle/)

哈希映射想出来了，但是缺点是没有想出来怎么处理相同值的问题Hashmap会只存一个值那么我们可以存一个下标列表这样就好了，贪心策略，优先匹配最大的打败他，否则用最小的满足，这个函数需要注意一下：indices.remove(0)

```java
public int[] advantageCount(int[] nums1, int[] nums2) {
        // 存储 nums2 每个值的所有索引
        HashMap<Integer, List<Integer>> hm = new HashMap<>();
        for (int i = 0; i < nums2.length; ++i) {
            hm.computeIfAbsent(nums2[i], k -> new ArrayList<>()).add(i);
        }
        // 对 nums1 和 nums2 排序
        Arrays.sort(nums1);
        Arrays.sort(nums2);
        int l = 0, r = nums1.length - 1;
        int[] res = new int[nums1.length];
        // 遍历 nums2 从大到小，优先用 nums1 的大数打败 nums2 的大数
        for (int idx2 = nums2.length - 1; idx2 >= 0; --idx2) {
            int val2 = nums2[idx2];
            List<Integer> indices = hm.get(val2);
            if (nums1[r] > val2) {  // 如果 nums1 的最大值大于 nums2 当前值
                // 使用 nums1[r] 这个最大值来打败 nums2[idx2]
                res[indices.remove(0)] = nums1[r--];
            } else {  // 否则，使用 nums1[l] 来填补
                res[indices.remove(0)] = nums1[l++];
            }
        }
        return res;
    }
```

[376. 摆动序列](https://leetcode.cn/problems/wiggle-subsequence/)

动态规划

```java
public int wiggleMaxLength(int[] nums) {
        int dp[][]=new int[nums.length][2];
        dp[0][0]=dp[0][1]=1;//0表示到这个数是递增的，1表示到这个数是递减的
        int res=1;
        for(int i=1;i<nums.length;++i)
        {
            for(int j=0;j<i;++j)
            {
                if(nums[i]>nums[j])
                {
                    dp[i][0]=Math.max(dp[i][0],dp[j][1]+1);
                    res=Math.max(res,dp[i][0]);
                }else if(nums[i]<nums[j])
                {
                    dp[i][1]=Math.max(dp[i][1],dp[j][0]+1);
                    res=Math.max(res,dp[i][1]);
                }
            }
        }
        return res;
    }
```

贪心算法，从第一个就可以开始计算了

```java
public int wiggleMaxLength(int[] nums) {
    // 处理特殊情况：数组长度小于2时直接返回长度
    if (nums.length < 2) {
        return nums.length;
    }
    int len; // 记录最长摆动序列长度
    int pre = nums[1] - nums[0]; // 前一个差值
    // 初始化长度：如果前两个数相同则为1，否则为2
    if (pre == 0) {
        len = 1;
    } else {
        len = 2;
    }
    // 从第三个元素开始遍历
    for (int i = 2; i < nums.length; i++) {
        // 如果当前元素与前一个元素相同，跳过
        if (nums[i] == nums[i - 1]) {
            continue;
        }
        int temp = nums[i] - nums[i - 1]; // 当前差值
        // 判断是否形成摆动（满足以下任一条件）：
        // 1. 前一个差值为正且当前为负
        // 2. 前一个差值为负且当前为正
        // 3. 前一个差值为0且当前不为0（处理初始情况）
        if ((pre > 0 && temp < 0) || 
            (pre < 0 && temp > 0) || 
            (pre == 0 && temp != 0)) {
            len++; // 摆动序列长度加1
            pre = temp; // 更新前一个差值
        }
        // 不满足摆动条件则继续循环
    }
    return len;
}
```

[365. 水壶问题](https://leetcode.cn/problems/water-and-jug-problem/)

贝组定理

![1758443292246](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1758443292246.png)

```java
public int gcd(int x, int y) {
        int remainder = x % y;
        while (remainder != 0) {
            x = y;
            y = remainder;
            remainder = x % y;
        }
        return y;
    }

    public boolean canMeasureWater(int x, int y, int target) {
        return target<=x+y&&target%gcd(x,y)==0;
    }
```

[877. 石子游戏](https://leetcode.cn/problems/stone-game/)

```java
public boolean stoneGame(int[] piles) {
    // 获取石子堆的数量
    int n = piles.length;
    // 创建动态规划表 dp[i][j] 表示在子数组 piles[i..j] 中，
    // 当前玩家能比对手多获得的最大分数
    int[][] dp = new int[n][n];
    // 基础情况：当只有一个石子堆时，玩家只能拿这个石子
    // 所以 dp[i][i] = piles[i]
    for (int i = 0; i < n; i++) {
        dp[i][i] = piles[i];
    }
    // 外层循环控制子数组的长度，从2开始到n
    // 因为长度为1的情况已经初始化过了
    for (int len = 2; len <= n; len++) {   
        // 内层循环控制子数组的起始位置i
        // j是子数组的结束位置，计算为i + len - 1
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            // 状态转移方程：
            // 当前玩家有两种选择：
            // 1. 拿左边的石子 piles[i]，那么对手会在子数组[i+1..j]中采取最优策略
            //    所以当前玩家比对手多的分数为 piles[i] - dp[i+1][j]
            // 2. 拿右边的石子 piles[j]，那么对手会在子数组[i..j-1]中采取最优策略
            //    所以当前玩家比对手多的分数为 piles[j] - dp[i][j-1]
            // 取这两种选择中的最大值作为dp[i][j]的值
            dp[i][j] = Math.max(piles[i] - dp[i+1][j], piles[j] - dp[i][j-1]);
        }
    }
    // 最终结果存储在dp[0][n-1]中，表示在整个数组[0..n-1]中
    // 先手玩家比后手玩家多的分数
    // 如果这个值大于0，说明先手玩家能获胜
    return dp[0][n-1] > 0;
}
```

[915. 分割数组](https://leetcode.cn/problems/partition-array-into-disjoint-intervals/)

前后缀和的思想，算出最大值前缀和最小值后缀，找到两个中间的地方最大值前缀小于最小值后缀的地方在加一就是对应长度

```java
public int partitionDisjoint(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int n = nums.length;
        int[] leftMax = new int[n];
        leftMax[0] = nums[0];
        for (int i = 1;i < n;i ++) {
            leftMax[i] = Math.max(leftMax[i - 1], nums[i]);
        }

        int[] rightMin = new int[n];
        rightMin[n - 1] = nums[n - 1];
        for (int i = n - 2;i >= 0;i --) {
            rightMin[i] = Math.min(rightMin[i + 1], nums[i]);
        }
        
        for (int i = 0;i < n - 1;i ++) {
            if (leftMax[i] <= rightMin[i + 1]) return i + 1;
        }
        return n;//不会发生
    }
```

[373. 查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)

为什么这样考虑，先把第一个数组的前K个元素加进来，每次加入了队首之后再把i,j+1加入，因为对于i来说已经把i+1,i+2,...和j=0的情况都加进去了，这样只要考虑不停的加入j+1就可以了

```java
public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        // 优先队列，存储的是 (nums1[i], nums2[j], j) ，其中 i 和 j 分别是 nums1 和 nums2 的索引
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]]));

        // 初始化时将 nums1 的前 k 个元素和 nums2[0] 进行配对加入最小堆
        for (int i = 0; i < Math.min(k, nums1.length); i++) {
            pq.offer(new int[]{i, 0});  // i 是 nums1 的索引，0 是 nums2 的索引
        }
        // 迭代 k 次，每次从最小堆中取出最小的配对，并且加入 res
        while (k-- > 0 && !pq.isEmpty()) {
            int[] cur = pq.poll();
            int i = cur[0], j = cur[1];
            res.add(Arrays.asList(nums1[i], nums2[j]));

            // 如果还有更多的配对，可以尝试将 (nums1[i], nums2[j+1]) 加入堆中
            if (j + 1 < nums2.length) {
                pq.offer(new int[]{i, j + 1});
            }
        }

        return res;
    }
```

[1013. 将数组分成和相等的三个部分](https://leetcode.cn/problems/partition-array-into-three-parts-with-equal-sum/)

早停策略，先判断能不能被三整除，然后找到两个分割点就可以返回true

[147. 对链表进行插入排序](https://leetcode.cn/problems/insertion-sort-list/)

插入排序链表有几个思考点，一个是截断两个地方，一个是已经排序的，一个是还未排序的 23ms

```Java
public ListNode insertionSortList(ListNode head) {
        ListNode dummy=new ListNode();
        dummy.next=head;
        ListNode ne=head.next;
        head.next=null;
        while(ne!=null)
        {
            ListNode temp=ne;
            ne=ne.next;
            ListNode pre=dummy,cur=dummy.next;
            while(cur!=null&&temp.val>cur.val)
            {
                cur=cur.next;
                pre=pre.next;
            }
            pre.next=temp;
            temp.next=cur;
        }
        return dummy.next;
    }
```

第二种是混在一起，有很强的优化，记录当前头和尾，直接和尾比较可以省时然后修改指针有点费劲2ms

```java
 public ListNode insertionSortList(ListNode head) {
        ListNode before = new ListNode();
        before.next = head;
        ListNode prev = before, curr = head.next;
        ListNode last = prev.next;
        while(curr != null){
            if(curr.val >= last.val){
                last = last.next;
                curr = curr.next;
                continue;
            }
            while(prev.next.val< curr.val){
                prev = prev.next;
            }
            last.next = curr.next;
            curr.next = prev.next;
            prev.next = curr;
            prev = before;
            curr = last.next;
        }
        return before.next;
    }
```

[786. 第 K 个最小的质数分数](https://leetcode.cn/problems/k-th-smallest-prime-fraction/)

二分查找的方法，从0-1之间找到第K大的最大近似值

```java
public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        // 数组长度
        int n = arr.length;
        // 初始化二分查找的左右边界
        // 因为分数值在0到1之间（arr是严格递增的正整数数组）
        double left = 0, right = 1.0;
        // 开始二分查找
        while (left < right) {
            // 计算中间值（猜测的分数值）
            double mid = (left + right) / 2;
            // 记录当前小于mid的最大分数及其对应的分子分母索引
            double maxFraction = 0.0;
            int numeratorIdx = 0, denominatorIdx = 0;
            // 统计小于mid的分数个数
            int count = 0;
            // j指针从1开始，表示分母的索引
            int j = 1;
            // 遍历所有可能的分子（arr[i]）
            for (int i = 0; i < n - 1; i++) {
                // 找到满足arr[i]/arr[j] < mid的最小j
                // 因为数组有序，j可以单调递增
                while (j < n && arr[i] >= mid * arr[j]) {
                    j++;
                }
                // 统计当前分子arr[i]对应的小于mid的分数个数
                count += (n - j);
                // 如果j已经到达数组末尾，可以提前终止
                if (j == n) break;
                // 计算当前分数值
                double fraction = (double) arr[i] / arr[j];
                // 更新最大分数
                if (fraction > maxFraction) {
                    numeratorIdx = i;
                    denominatorIdx = j;
                    maxFraction = fraction;
                }
            }
            // 根据统计结果调整搜索范围
            if (count == k) {
                // 正好找到k个小于mid的分数，返回最大的那个
                return new int[]{arr[numeratorIdx], arr[denominatorIdx]};
            } else if (count > k) {
                // 太多分数小于mid，需要缩小右边界
                right = mid;
            } else {
                // 不够k个分数小于mid，需要增大左边界
                left = mid;
            }
        }
        // 理论上不会执行到这里（因为k保证有效）
        return new int[]{};
    }
```

和另一个373. 查找和最小的 K 对数字一样加入队列，维护一个K大的队列

```java
public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        PriorityQueue<int[]> pq=new PriorityQueue<>((a,b)->
                (double)arr[a[0]]/arr[a[1]]-(double)arr[b[0]]/arr[b[1]]>0?1:-1);
        int n=arr.length-Math.min(arr.length,k);
        for(int i=arr.length-1;i>=n;--i)
            pq.add(new int[]{0,i});
        while(--k>0&&!pq.isEmpty())
        {
            int temp[]=pq.poll();
            int x=temp[0],y=temp[1];
            if(x<arr.length-1)
                pq.add(new int[]{x+1,y});
        }
        return new int[]{arr[pq.peek()[0]],arr[pq.peek()[1]]};
    }
```

[726. 原子的数量](https://leetcode.cn/problems/number-of-atoms/)

```java
class Solution {
    // 全局变量，记录递归处理时的当前位置
    public static int where = 0;
    public static String countOfAtoms(String str) {
        // 重置全局变量
        where = 0;
        // 调用递归函数处理化学式
        TreeMap<String, Integer> map = f(str.toCharArray(), 0);
        // 构建结果字符串
        StringBuilder ans = new StringBuilder();
        // 遍历有序的原子名称
        for (String key : map.keySet()) {
            int cnt = map.get(key);
            ans.append(key);
            // 如果原子数量大于1才添加数字
            if (cnt > 1) {
                ans.append(cnt);
            }
        }
        return ans.toString();
    }

    /**
     * 递归解析化学式
     * @param s 化学式字符数组
     * @param i 当前处理位置
     * @return 当前层级的原子计数
     */
    public static TreeMap<String, Integer> f(char[] s, int i) {
        int cnt = 0; // 记录当前数字
        TreeMap<String, Integer> ans = new TreeMap<>(); // 当前层级的原子计数
        StringBuilder name = new StringBuilder(); // 当前原子名称
        TreeMap<String, Integer> pre = null; // 记录括号内的原子计数
        // 遍历化学式，直到遇到右括号或字符串结束
        while (i < s.length && s[i] != ')') {
            // 遇到大写字母或左括号
            if (s[i] >= 'A' && s[i] <= 'Z' || s[i] == '(') {
                // 处理之前积累的原子或括号内容
                fill(ans, name, pre, cnt);
                // 重置临时变量
                name.setLength(0);
                pre = null;
                cnt = 0;
                // 如果是大写字母，开始记录原子名称
                if (s[i] >= 'A' && s[i] <= 'Z') {
                    name.append(s[i++]);
                } 
                // 如果是左括号，递归处理括号内的内容
                else {
                    pre = f(s, i + 1);
                    i = where + 1; // 跳到右括号后的位置
                }
            } 
            // 遇到小写字母，继续记录原子名称
            else if (s[i] >= 'a' && s[i] <= 'z') {
                name.append(s[i++]);
            }
            // 遇到数字，计算完整数字
            else {
                cnt = cnt * 10 + s[i++] - '0';
            }
        }
        // 处理最后积累的原子或括号内容
        fill(ans, name, pre, cnt);
        // 更新全局位置变量
        where = i;
        return ans;
    }

    /**
     * 将当前原子或括号内容合并到结果中
     * @param ans 结果集
     * @param name 原子名称
     * @param pre 括号内的原子计数
     * @param cnt 当前数字
     */
    public static void fill(TreeMap<String, Integer> ans, StringBuilder name, 
                          TreeMap<String, Integer> pre, int cnt) {
        // 如果有原子名称或括号内容需要处理
        if (name.length() > 0 || pre != null) {
            // 默认数量为1
            cnt = cnt == 0 ? 1 : cnt;
            // 处理原子名称
            if (name.length() > 0) {
                String key = name.toString();
                ans.put(key, ans.getOrDefault(key, 0) + cnt);
            } 
            // 处理括号内容
            else {
                // 将括号内的原子计数乘以倍数后合并到结果中
                for (String key : pre.keySet()) {
                    ans.put(key, ans.getOrDefault(key, 0) + pre.get(key) * cnt);
                }
            }
        }
    }
}
```

[1049. 最后一块石头的重量 II](https://leetcode.cn/problems/last-stone-weight-ii/)

分成两半，找到最大下标用一半乘以2就是裂开的数量，如果总和是奇数的话要多加一

```java
public int lastStoneWeightII(int[] stones) {
        int sum=0;
        for(int num:stones)
            sum+=num;
        boolean dp[]=new boolean[sum/2+1];
        int n=dp.length-1;
        dp[0]=true;
        for(int stone:stones)
        {
            if(stone<=n)
            {
                for(int i=n;i>=stone;--i)
                    if(dp[i-stone])
                        dp[i]=true;
            }
        }
        int half=n;
        for(;half>=0;--half)
            if(dp[half])break;
        if(sum%2==0)
            return (n-half)*2;
        else return (n-half)*2+1;
    }
```

[331. 验证二叉树的前序序列化](https://leetcode.cn/problems/verify-preorder-serialization-of-a-binary-tree/)

模拟左右节点再走一遍，我真是太聪明了

```java
String[] nodes;
    int idx;
    public boolean isValidSerialization(String preorder) {
        nodes=preorder.split(",");
        return travel()&&idx==nodes.length;//idx==nodes.length不可少会出现9##1这种
    }

    boolean travel()
    {
        if(idx>=nodes.length) return false;
        String now=nodes[idx++];
        if(now.equals("#")) return true;
//        boolean left=travel();
//        boolean right=travel();
//        return left&&right;
        return travel()&&travel();
    }
```

[187. 重复的DNA序列](https://leetcode.cn/problems/repeated-dna-sequences/)

当然可以选择hashset去重，但是一个更佳的方案是用hashmap存次数，仅在次数为2的时候存

```java
public List<String> findRepeatedDnaSequences(String s) {
        List<String> res=new ArrayList<>();
        if(s.length()<10) return res;
        HashMap<String,Integer> hm=new HashMap<>();
        for(int i=10;i<=s.length();++i)
        {
            String temp=s.substring(i-10,i);
            int cnt=hm.getOrDefault(temp,0)+1;
            hm.put(temp,cnt);
            if(cnt==2) res.add(temp);
        }
        return res;
    }
```

[621. 任务调度器](https://leetcode.cn/problems/task-scheduler/)

别再傻傻模拟了，还模拟不明白，看到数学推导赶紧理解吧

```java
public int leastInterval(char[] tasks, int n) {
    // 步骤1：统计每个任务的出现次数
    // 使用长度为26的数组（对应26个大写字母）来记录每个任务的执行次数
    int[] count = new int[26];
    for (char task : tasks) {
        // 将字符转换为数组索引（'A' -> 0, 'B' -> 1, ..., 'Z' -> 25）
        count[task - 'A']++;
    }
    // 步骤2：对任务计数进行排序（升序排列）
    // 排序后，数组最后面的元素就是出现次数最多的任务
    Arrays.sort(count);
    // 步骤3：找出出现次数最多的任务及其出现次数
    int maxHight = count[25]; // 最多出现的次数
    int maxCount = 0; // 有多少个任务出现了maxHight次
    // 从后往前遍历已排序的数组，统计出现maxHight次的任务数量
    for (int i = 25; i >= 0; i--) {
        if (count[i] == maxHight) {
            maxCount++;
        } else {
            break; // 因为已排序，一旦不等于就可以提前终止
        }
    }
    // 步骤4：计算最小时间
    // 公式推导：
    // 1. (maxHight - 1) * (n + 1): 每个完整周期需要的时间
    // 2. + maxCount: 最后一个周期的任务数
    // 3. Math.max(..., tasks.length): 确保不会比任务总数还小
    return Math.max(tasks.length, (maxHight - 1) * (n + 1) + maxCount);
}
```

[632. 最小区间](https://leetcode.cn/problems/smallest-range-covering-elements-from-k-lists/)

区间数统计，类似词频统计，然后把K个窗口集合到一个列表中，按照值大小排序（注意用列表然后再排序要维护相对位置的，不能用优先队列），同时存区间号，left和right动态维护满足条件的长度，选择最短长度就好

```java
public int[] smallestRange(List<List<Integer>> nums) {
        // 获取列表的数量
        int k = nums.size();
        // 步骤1：创建一个列表来存储所有元素及其所属的列表索引
        // 每个元素表示为[数值, 列表索引]
        List<int[]> elements = new ArrayList<>();
        // 将所有列表中的元素添加到合并列表中
        for (int i = 0; i < k; i++) {
            for (int num : nums.get(i)) {
                // 存储元素值和它所属的列表索引
                elements.add(new int[]{num, i});
            }
        }
        // 步骤2：将所有元素按数值大小进行排序（升序）
        // 这样我们可以有效地使用滑动窗口技术
        Collections.sort(elements, Comparator.comparingInt(a -> a[0]));
        // 步骤3：初始化滑动窗口所需的变量
        // count数组用于跟踪当前窗口中来自各个列表的元素数量
        int[] count = new int[k];
        // uniqueLists记录当前窗口中包含的不同列表的数量
        int uniqueLists = 0;
        // 滑动窗口的左指针
        int left = 0;
        // 记录找到的最小范围大小
        int minRange = Integer.MAX_VALUE;
        // 存储最终结果的范围[start, end]
        int[] result = new int[2];
        // 步骤4：使用右指针扩展窗口
        for (int right = 0; right < elements.size(); right++) {
            // 当前元素的数值
            int currentValue = elements.get(right)[0];
            // 当前元素所属的列表索引
            int currentListIndex = elements.get(right)[1];
            // 将当前元素加入窗口
            count[currentListIndex]++;
            // 如果这是当前窗口中来自该列表的第一个元素
            // 增加uniqueLists计数器
            if (count[currentListIndex] == 1) {
                uniqueLists++;
            }
            // 步骤5：尝试从左侧收缩窗口，同时保持窗口有效性
            // 有效性指窗口中包含来自每个列表的至少一个元素
            while (uniqueLists == k && left <= right) {
                // 窗口最左侧元素的数值
                int leftValue = elements.get(left)[0];
                // 计算当前窗口的范围大小
                int currentRange = currentValue - leftValue;
                // 步骤6：如果我们找到更小的范围，就更新结果
                // 根据题目要求：范围[a,b]比[c,d]小当且仅当：
                // (b-a < d-c) 或者 (b-a == d-c 且 a < c)
                if (currentRange < minRange) {
                    minRange = currentRange;
                    result[0] = leftValue;   // 范围的起始值
                    result[1] = currentValue; // 范围的结束值
                }
                // 从窗口左侧移除元素
                int leftListIndex = elements.get(left)[1]; // 最左侧元素所属的列表索引
                // 从窗口中移除最左侧元素
                count[leftListIndex]--;
                // 如果窗口中不再包含来自该列表的元素
                // 减少uniqueLists计数器
                if (count[leftListIndex] == 0) {
                    uniqueLists--;
                }
                // 将左指针向右移动
                left++;
            }
        }
        // 步骤7：返回找到的最小范围
        return result;
    }
```

[814. 二叉树剪枝](https://leetcode.cn/problems/binary-tree-pruning/)

后序遍历，先算左孩子右孩子再看是不是都为空然后当前值还是0，是的话返回null

```java
public TreeNode pruneTree(TreeNode root) {
        return Cut(root);
    }

    TreeNode Cut(TreeNode root)
    {
        if(root==null) return root;
        root.left=Cut(root.left);
        root.right=Cut(root.right);
        if(root.left==null&&root.right==null&&root.val==0)
            return null;
        return root;
    }
```

[1339. 分裂二叉树的最大乘积](https://leetcode.cn/problems/maximum-product-of-splitted-binary-tree/)

先把树改造成总和树，然后总和就是root的值，用全局变量进行一次树的遍历就可以了用总和减去当前节点的值就是剩余的值，相乘然后取最大值

[399. 除法求值](https://leetcode.cn/problems/evaluate-division/)

除法把它当作一个图，a/b当作从a到b的代价，然后结果就是用每个代价相乘就好了，对于图来说有一个小细节就是加入到map中的时候一定要两边都加

```java
public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        Map<String, Map<String, Double>> graph = new HashMap<>();

        // 构建图
        for (int i = 0; i < equations.size(); i++) {
            String u = equations.get(i).get(0);
            String v = equations.get(i).get(1);
            double value = values[i];

            graph.putIfAbsent(u, new HashMap<>());
            graph.putIfAbsent(v, new HashMap<>());

            graph.get(u).put(v, value);
            graph.get(v).put(u, 1.0 / value);//别忘了两边都加
        }

        double[] results = new double[queries.size()];
        // 对每个查询进行DFS搜索
        for (int i = 0; i < queries.size(); i++) {
            String from = queries.get(i).get(0);
            String to = queries.get(i).get(1);
            if (!graph.containsKey(from) || !graph.containsKey(to)) {
                results[i] = -1.0;
            } else {
                Set<String> visited = new HashSet<>();
                results[i] = dfs(graph, from, to, 1.0, visited);
            }
        }
        return results;
    }

    private double dfs(Map<String, Map<String, Double>> graph, String curr, String target, double value, Set<String> visited) {
        if (curr.equals(target)) return value;
        visited.add(curr);
        Map<String, Double> neighbors = graph.get(curr);
        for (String neighbor : neighbors.keySet()) {
            if (!visited.contains(neighbor)) {
                double result = dfs(graph, neighbor, target, value * neighbors.get(neighbor), visited);
                if (result != -1.0) return result;
            }
        }

        return -1.0;
    }
```

[911 · 最大子数组之和为k - LintCode](https://www.lintcode.com/problem/911/description)

前缀和数组加上哈希表存对应前缀和以及最早下标，我们

```java
public int maxSubArrayLen(int[] nums, int k) {
        // 创建哈希表存储前缀和及其最早出现的位置
        // key: 前缀和，value: 该前缀和第一次出现的索引位置
        HashMap<Integer, Integer> prefixSumMap = new HashMap<>();
        int n = nums.length;
        // 前缀和数组，sum[i]表示前i个元素的和
        int[] prefixSum = new int[n + 1];
        int maxLength = 0; // 记录最大子数组长度
        // 初始化：和为k的子数组可能从第一个元素开始
        prefixSumMap.put(0, 0);
        // 遍历计算前缀和
        for(int i = 1; i <= n; i++) {
            // 计算前i个元素的和
            prefixSum[i] = prefixSum[i - 1] + nums[i - 1];
            // 检查当前前缀和是否等于某个目标值
            // 如果存在sum[j] = sum[i] - k，那么子数组[j+1, i]的和就是k
            if(prefixSumMap.containsKey(prefixSum[i]-k)) {
                // 更新最大长度：i - prefixSumMap.get(sum[i])
                maxLength = Math.max(maxLength, i - prefixSumMap.get(prefixSum[i]-k));
            }
            // 没有更早的下标的话。将当前前缀和作为key存入map，值为当前索引i
            if(!prefixSumMap.containsKey(prefixSum[i])) {
                prefixSumMap.put(prefixSum[i], i);
            }
        }
        return maxLength;
    }
```

[633. 平方数之和](https://leetcode.cn/problems/sum-of-square-numbers/)

双指针，就是先把c转换成long然后求根号，左指针是0，右指针是c的根号，然后使用双指针，如果大了右指针减一，小了左指针减一，注意要换成long类型

```java
public boolean judgeSquareSum(int c) {
        long a = 0, b = (long) Math.sqrt(c);
        while (a <= b) {
            long cur = a * a + b * b;
            if (cur == c) {
                return true;
            } else if (cur > c) {
                b--;
            } else {
                a++;
            }
        }
        return false;
    }
```

费马平方和，了解就行，使用双指针方法就好

![1758770562251](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1758770562251.png)

```java
public class Solution {
    public boolean judgeSquareSum(int c) {
        for (int i = 2, cnt = 0; i * i <= c; i++, cnt = 0) {
            while (c % i == 0 && ++cnt > 0) c /= i;
            if (i % 4 == 3 && cnt % 2 != 0) return false;
        }
        return c % 4 != 3;
    }
}
```

[820. 单词的压缩编码](https://leetcode.cn/problems/short-encoding-of-words/)

```java
public int minimumLengthEncoding(String[] words) {
        // 步骤1：按单词长度降序排序
        Arrays.sort(words, (a, b) -> b.length() - a.length());
        // 步骤2：构建字典树
        TrieNode root = new TrieNode();
        int totalLength = 0;
        for (String word : words) {
            TrieNode node = root;
            boolean isNewWord = false; 
            // 反向插入单词（处理后缀）
            for (int i = word.length() - 1; i >= 0; i--) {
                char c = word.charAt(i);
                if (node.children[c - 'a'] == null) {
                    isNewWord = true;
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            // 如果是新单词（创建了新路径）
            if (isNewWord) {
                totalLength += word.length() + 1; // +1 for '#'
            }
        }
        return totalLength;
    }
    
    // 字典树节点定义
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
    }
```

[1209. 删除字符串中的所有相邻重复项 II](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string-ii/)

双指针非常巧妙，计数数组去计算当前的计数，arr数组拷贝相应的指针字符，每个字符都会拷贝但是slow会回溯把相同多余的字符全部覆盖掉

```java
public String removeDuplicates(String s, int k) {
        // 将字符串转换为字符数组以便原地修改
        char[] arr = s.toCharArray();
        // 创建计数数组，记录每个位置字符的连续出现次数
        int[] count = new int[s.length()];
        // 使用双指针法：
        // slow - 指向当前有效字符的位置
        // fast - 遍历原始字符的位置
        int slow = 0;
        int fast = 0;
        // 遍历整个字符数组
        for (int i = 0; i < arr.length; i++) {
            // 将fast指针的字符复制到slow位置
            arr[slow] = arr[fast];
            // 计算当前字符的连续出现次数
            if (slow > 0 && arr[slow] == arr[slow - 1]) {
                // 如果当前字符与前一个相同，则计数+1
                count[slow] = count[slow - 1] + 1;
            } else {
                // 否则重置计数为1
                count[slow] = 1;
            }
            // 检查是否达到k个连续相同字符
            if (count[slow] == k) {
                // 如果达到k个，则将slow指针回退k-1个位置
                // 相当于删除这k个相同字符
                slow = slow - k + 1;
            } else {
                // 否则正常移动slow指针
                slow++;
            }
            // fast指针始终向前移动
            fast++;
        }
        // 根据slow指针的位置创建结果字符串
        return new String(arr, 0, slow);
    }
```

[151. 反转字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/)

最快方法，全部使用库函数，分割的时候中间可能会有多余空格，它会变成空串计算进去，遇到别加入就行

```java
public String reverseWords(String s) {
        StringBuilder sb=new StringBuilder(s.strip()).reverse();
        String[] strs=sb.toString().split(" ");
        StringBuilder res=new StringBuilder();
        for(String str:strs)
        {
            if(str.equals(""))continue;
            res.append(new StringBuilder(str).reverse()).append(' ');
        }
        res.deleteCharAt(res.length()-1);
        return res.toString();
    }
```

[212. 单词搜索 II](https://leetcode.cn/problems/word-search-ii/)

前缀树记录所有的节点，注意在建树的时候有一个小细节就是只有节点不存在才去建新的节点，不然出错找半天

```java
List<String> res=new ArrayList<>();

    public List<String> findWords(char[][] board, String[] words) {
        TrieNode root=BuildTrie(words);
        for(int i=0;i< board.length;++i)
            for(int j=0;j<board[0].length;++j)
                dfs(board,i,j,root);
        return res;
    }

    public void dfs(char[][] board, int i, int j, TrieNode p) {
        // 边界检查：如果越界或已经访问过该位置，直接返回
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return;
        char c = board[i][j];
        // 如果当前字符已经访问过或者不在前缀树中，停止搜索
        if (c == '#' || p.children[c - 'a'] == null) return;

        p = p.children[c - 'a'];  // 移动到前缀树的下一个节点
        // 如果当前节点包含一个完整的单词，将其加入结果列表
        if (p.word != null) {
            res.add(p.word);  // 找到一个完整单词
            p.word = null;  // 防止重复加入
        }

        // 标记当前棋盘位置为访问过（#）
        board[i][j] = '#';
        // 继续向四个方向进行深度优先搜索
        dfs(board, i - 1, j, p);  // 上
        dfs(board, i + 1, j, p);  // 下
        dfs(board, i, j + 1, p);  // 右
        dfs(board, i, j - 1, p);  // 左
        // 回溯：恢复棋盘当前位置的字符
        board[i][j] = c;
    }


    TrieNode BuildTrie(String[] words)
    {
        TrieNode root=new TrieNode();
        for(String s:words)
        {
            TrieNode cur=root;
            for(int i=0;i<s.length();++i)
            {
                if(cur.children[s.charAt(i)-'a']==null)//这里千万别搞错
                    cur.children[s.charAt(i)-'a']=new TrieNode();
                cur=cur.children[s.charAt(i)-'a'];
            }
            cur.word=s;
        }
        return root;
    }
    class TrieNode{
        TrieNode children[]=new TrieNode[26];
        String word;
    }
```

[132. 分割回文串 II](https://leetcode.cn/problems/palindrome-partitioning-ii/)

我本身有一个思路，就是dp记录回文判定，然后使用贪心算法从左到右优先找到最长的回文然后移动指针，但其实有bug   例如用例： 正确分割是a，aabaa而不是aaa b aa![1758853076157](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1758853076157.png)

正确思路，dp直接记录从i到j的分割次数，使用中心扩散法去处理回文串

```java
// 动态规划 + 中心扩散法
    public int minCut(String s) {
        // 如果字符串为空或长度为1，则无需切割
        if (s == null || s.length() <= 1) {
            return 0;
        }
        
        // 1. dp[i] 表示 0 到 i 之间最少需要的切割次数
        int[] dp = new int[s.length()];
        
        // 2. 初始化 dp 数组，默认每个位置的切割次数为 i，表示最坏的情况
        Arrays.fill(dp, dp.length - 1);

        // 3. 使用中心扩散法处理每个字符的回文串
        for (int i = 0; i < s.length(); i++) {
            // 回文串是奇数长度的情况（一个中心点）
            minCutCnt(s, dp, i, i);
            // 回文串是偶数长度的情况（两个中心点）
            minCutCnt(s, dp, i, i + 1);
        }

        // 4. 返回 dp 数组中最后一个位置的最少切割次数，即字符串整体的最小切割次数
        return dp[dp.length - 1];
    }

    // 计算从 i 到 j 的回文子串，并更新 dp 数组
    private void minCutCnt(String s, int[] dp, int i, int j) {
        // 5. 如果 i >= 0 且 j < s.length()，并且 s[i] 和 s[j] 相等，则继续扩散
        while (i >= 0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
            // 6. 如果 i == 0，表示从头开始，切割次数是 0
            // 否则，dp[i-1] 表示 i 之前部分的最小切割次数，再加 1 就是从 i 到 j 这一段的切割
            dp[j] = Math.min(dp[j], (i == 0 ? -1 : dp[i - 1]) + 1);
            // 7. 扩展中心，继续比较 i-1 和 j+1
            i--;
            j++;
        }
    }
```

[387. 字符串中的第一个唯一字符](https://leetcode.cn/problems/first-unique-character-in-a-string/)

只包含小写字符，使用数组哈希，一个记录次数一个记录下标，然后从0到len-1遍历次数是1的直接返回下标

[539. 最小时间差](https://leetcode.cn/problems/minimum-time-difference/)

把所有时间全部转化成分钟然后排序，然后相减，最后还需要用第一个加上24*60减去最后一个算

```java
public int findMinDifference(List<String> timePoints) {
        if(timePoints.size()>1440){
            return 0;
        }
        int[] minitues=new int[timePoints.size()];
        for(int i=0;i<timePoints.size();i++){
            String time=timePoints.get(i);
            int hour=Integer.parseInt(time.substring(0,2));
            int minitue=Integer.parseInt(time.substring(3));
            minitues[i]=hour*60+minitue;
        }
        Arrays.sort(minitues);
        int minDiff=Integer.MAX_VALUE;
        int n=minitues.length;
        for(int i=1;i<n;i++){
            int diff=minitues[i]-minitues[i-1];
            minDiff=Math.min(minDiff,diff);
        }
        int circularDiff=minitues[0]+1440-minitues[n-1];
        minDiff=Math.min(minDiff,circularDiff);
        return minDiff;
    }
```

[473. 火柴拼正方形](https://leetcode.cn/problems/matchsticks-to-square/)

没想到是用回溯做的，这个剪枝条件比较难想：if(i>0&&sq[i]==sq[i-1])continue;前一个和后一个边相等的时候要减枝

```java
public boolean makesquare(int[] matchsticks) {
        int sum=0,max=Integer.MIN_VALUE;
        for(int n:matchsticks)
        {
            sum+=n;
            max=Math.max(max,n);
        }
        if(sum%4!=0) return false;
        int target=sum/4;
        if(max>target) return false;
        Arrays.sort(matchsticks);
        reverse(matchsticks,0,matchsticks.length-1);
        return travel(matchsticks,new int[4],0,target);
    }

    boolean travel(int nums[],int []sq,int idx,int target)
    {
        if(idx==nums.length)
            return sq[0]==sq[1]&&sq[1]==sq[2]&&sq[2]==sq[3];
        for(int i=0;i<4;++i)
        {
            if(sq[i]+nums[idx]>target) continue;
            if(i>0&&sq[i]==sq[i-1])continue;
            sq[i]+=nums[idx];
            if(travel(nums,sq,idx+1,target))
                return true;
            sq[i]-=nums[idx];
        }
        return false;
    }
    void reverse(int num[],int i,int j)
    {
        while(i<j)
        {
            int temp=num[i];
            num[i]=num[j];
            num[j]=temp;
            ++i;--j;
        }
    }
```

[796. 旋转字符串](https://leetcode.cn/problems/rotate-string/)

方法一移动串

```java
public boolean rotateString(String s, String goal) {
        StringBuilder sb=new StringBuilder(goal);
        for(int i=0;i<goal.length();++i)
        {
            if(s.equals(sb.toString()))
                return true;
            char last=sb.charAt(sb.length()-1);
            sb.setLength(sb.length()-1);
            sb.insert(0,last);
        }
        return false;
    }
```

方法二，拼接两个用contains就好，很抽象没想到

```java
public boolean rotateString(String s, String goal) {
        return s.length() == goal.length() && (goal + goal).contains(s);
    }
```

[1011. 在 D 天内送达包裹的能力](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/)

二分查找，左边界是元素最大值，右边界是总和，然后计算函数计算的是这个承载能力所要的天数与实际天数来反映

```java
public int shipWithinDays(int[] weights, int days) {
        int right=0,left=Integer.MIN_VALUE;
        for(int n:weights)
        {
            right+=n;
            left=Math.max(left,n);
        }
        while(left<right)
        {
            int mid=(left+right)/2;
            if(cal(weights,days,mid))
                right=mid;
            else left=mid+1;
        }
        return left;
    }

    boolean cal(int[] weights, int days, int capacity) {
        int currentLoad = 0;
        int requiredDays = 1; // 至少需要1天
        for (int w : weights) {
            if (currentLoad + w > capacity) {
                requiredDays++;
                currentLoad = 0;
            }
            currentLoad += w;
        }
        return requiredDays <= days;
    }
```

[面试题 08.02. 迷路的机器人](https://leetcode.cn/problems/robot-in-a-grid-lcci/)

只有一个地方，就是

```Java
List<List<Integer>> res=new LinkedList<>();
    public List<List<Integer>> pathWithObstacles(int[][] obstacleGrid) {
        trace(obstacleGrid,0,0);
        return res;
    }

    int dir[][]={{1,0},{0,1}};
    boolean trace(int[][] obstacleGrid,int i,int j)
    {
        if(i<0||j<0||i>=obstacleGrid.length||j>=obstacleGrid[0].length) return false;
        if(obstacleGrid[i][j]==1)return false;
        if(i==obstacleGrid.length-1&&j==obstacleGrid[0].length-1)
        {
            res.add(Arrays.asList(new Integer[]{i,j}));
            return true;
        }
        for(int x=0;x<2;++x)
        {
            obstacleGrid[i][j]=1;
            res.add(Arrays.asList(new Integer[]{i,j}));
            if(trace(obstacleGrid,i+dir[x][0],j+dir[x][1]))
                return true;
            res.remove(res.size()-1);
           //这里不需要重新置为0因为是一直往下走的走过了没有走通说明已经走不通了，相当于有向图，无向图才需要重新标记
        }
        return false;
    }
```

[1424. 对角线遍历 II](https://leetcode.cn/problems/diagonal-traverse-ii/)

非常好的方法，重点是有一个非常棒的规律是i+j的和是在同一个分组里面，从上到下依次加入到不同分组里面，然后最后要逆向遍历得到答案

```java
public int[] findDiagonalOrder(List<List<Integer>> nums) {
        // 1. 初始化变量
        int len = 0; // 记录所有元素的总数
        List<List<Integer>> list = new ArrayList<>(); // 存储按对角线分组的元素
        int rowIdx = 0; // 当前行的索引
        // 2. 遍历每一行
        for (List<Integer> num : nums) {
            // 2.1 累加当前行的元素数量到总长度
            len += num.size();
            // 2.2 遍历当前行的每个元素
            for (int colIdx = 0; colIdx < num.size(); colIdx++) {
                // 2.2.1 计算当前元素所在的对角线索引（i+j相同为同一对角线）
                int idx = rowIdx + colIdx;
                // 2.2.2 确保list有足够的空间存储当前对角线的元素
                while (list.size() <= idx) {
                    list.add(new ArrayList<>());
                }
                // 2.2.3 将当前元素添加到对应的对角线分组中
                list.get(idx).add(num.get(colIdx));
            }
            // 2.3 移动到下一行
            rowIdx++;
        }
        // 3. 创建结果数组
        int[] res = new int[len];
        int resIdx = 0; // 结果数组的当前索引
        // 4. 遍历所有对角线分组
        for (int i = 0; i < list.size(); i++) {
            // 4.1 逆序遍历当前对角线分组中的元素（实现对角线顺序）
            for (int j = list.get(i).size() - 1; j >= 0; j--) {
                res[resIdx++] = list.get(i).get(j);
            }
        }
        // 5. 返回最终结果
        return res;
    }
```

[241. 为运算表达式设计优先级](https://leetcode.cn/problems/different-ways-to-add-parentheses/)

DFS分块处理，用运算符号来分割两边的数字，如果结果集为空，说明范围内全是数字，解析数字就好了，是代码量最小的解法

```java
char[] cs;
    public List<Integer> diffWaysToCompute(String s) {
        // 将输入字符串转换为字符数组，方便处理
        cs = s.toCharArray();
        // 调用深度优先搜索方法，处理整个字符串
        return dfs(0, cs.length - 1);
    }
    List<Integer> dfs(int l, int r) {
        List<Integer> ans = new ArrayList<>();
        // 遍历当前区间内的所有字符
        for (int i = l; i <= r; i++) {
            // 跳过数字字符，只处理运算符
            if (cs[i] >= '0' && cs[i] <= '9') continue;
            // 递归计算运算符左侧子表达式的所有可能结果
            List<Integer> leftResults = dfs(l, i - 1);
            // 递归计算运算符右侧子表达式的所有可能结果
            List<Integer> rightResults = dfs(i + 1, r);
            // 组合左右两侧的结果，进行当前运算符的计算
            for (int a : leftResults) {
                for (int b : rightResults) {
                    int cur = 0;
                    // 根据运算符类型计算结果
                    if (cs[i] == '+') cur = a + b;
                    else if (cs[i] == '-') cur = a - b;
                    else if (cs[i] == '*') cur = a * b;
                    // 将计算结果加入当前结果列表
                    ans.add(cur);
                }
            }
        }
        // 如果当前区间没有运算符（即纯数字），注意哈，这段代码是在for循环外面的，说明所有字符都是数字所以跳过了
        if (ans.isEmpty()) {
            int cur = 0;
            // 将数字字符串转换为整数值
            for (int i = l; i <= r; i++) {
                cur = cur * 10 + (cs[i] - '0');
            }
            ans.add(cur);
        }
        return ans;
    }
```

[393. UTF-8 编码验证](https://leetcode.cn/problems/utf-8-validation/)

直接和相关的整数与就行，不用转成字符串

```java
public boolean validUtf8(int[] data) {
        int cnt = 0;
        for (int i : data) {
            if(cnt > 0) {
                // i的二进制数  为10xxxxxx
                if((i & 0xC0) == 0x80) {
                    cnt--;
                } else {
                    return false;
                }
            } else if((i & 0xE0) == 0xC0) {
                // i的二进制数为 11xxxxxx
                cnt = 1;
            } else if((i & 0xF0) == 0xE0) {
                // i的二进制数为 111xxxxx
                cnt = 2;
            } else if((i & 0xF8) == 0xF0) {
                // i的二进制数为 1111xxxx
                cnt = 3;
            } else if((i & 0x80) != 0) {
                // i不是1字节数据 0xxxxxxx
                return false;
            }
        }
        return cnt == 0;
    }
```

[面试题 08.05. 递归乘法](https://leetcode.cn/problems/recursive-mulitply-lcci/)

按位乘就好

```java
public int multiply(int A, int B) {
        int sum=0,move=0;
        while(B!=0)
        {
            if((B&1)==1)
                sum+=A<<move;
            B>>=1;
            ++move;
        }
        return sum;
    }
```

[860 · 不同岛屿的个数 - LintCode](https://www.lintcode.com/problem/860)

这道题不是求岛屿总数，而是不同岛屿的数量，要考虑形状，所以我们可以序列化岛屿的所有相对坐标

```java
public int numberofDistinctIslands(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        Set<String> shapes = new HashSet<>();
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    StringBuilder shape = new StringBuilder();
                    dfs(grid, i, j, i, j, shape);
                    shapes.add(shape.toString());
                }
            }
        }
        return shapes.size();
    }

    void dfs(int[][] grid, int i, int j, int baseX, int baseY, StringBuilder shape) {
        if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] != 1) {
            return;
        }
        // 记录相对坐标（相对于基准点）
        shape.append((i - baseX)).append(',').append((j - baseY)).append('|');
        grid[i][j] = 0; // 标记为已访问
        // 四个方向搜索（顺序固定以确保形状一致性）
        dfs(grid, i + 1, j, baseX, baseY, shape);
        dfs(grid, i - 1, j, baseX, baseY, shape);
        dfs(grid, i, j + 1, baseX, baseY, shape);
        dfs(grid, i, j - 1, baseX, baseY, shape);
    }
```

[785. 判断二分图](https://leetcode.cn/problems/is-graph-bipartite/)

没访问过的设为类别1，然后另一个类别设为-1，终止条件是如果已经被标记过了就和理论上比较一下就好因为之前算过这个节点的其他是不是满足

```java
public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] colors = new int[n];
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0) { // 未访问过
                if (!dfs(graph, i, colors,1)) return false;
            }
        }
        return true;
    }

    public boolean dfs(int[][] graph, int i, int[] colors,int color) {
        if (colors[i] == 0)
            colors[i]=color;
        else return colors[i]==color;
        for (int neighbor : graph[i]) { // 遍历邻居
            if (!dfs(graph, neighbor, colors,color==1?-1:1)) return false;
        }
        return true;
    }
```

[1031. 两个非重叠子数组的最大和](https://leetcode.cn/problems/maximum-sum-of-two-non-overlapping-subarrays/)

```java
public int maxSumTwoNoOverlap(int[] nums, int firstLen, int secondLen) {
    int n = nums.length;
    // 1. 构建前缀和数组：s[i]表示nums[0]到nums[i-1]的和
    //    这样s[i] - s[j] = nums[j] + nums[j+1] + ... + nums[i-1]
    int[] s = new int[n + 1];
    for (int i = 0; i < n; ++i) {
        s[i + 1] = s[i] + nums[i];
    }
    
    int ans = 0; // 存储最终结果
    
    // 2. 情况1：firstLen长度的子数组在secondLen长度的子数组左边
    //    遍历所有可能的secondLen子数组起始位置
    for (int i = firstLen, t = 0; i + secondLen - 1 < n; ++i) {
        // t记录在当前位置i之前，所有长度为firstLen的子数组的最大和
        // s[i] - s[i - firstLen]：以i-1结尾的长度为firstLen的子数组和
        t = Math.max(t, s[i] - s[i - firstLen]);
        // 当前secondLen子数组的和：s[i + secondLen] - s[i]
        // 加上左边firstLen子数组的最大和t
        ans = Math.max(ans, t + s[i + secondLen] - s[i]);
    }
    
    // 3. 情况2：secondLen长度的子数组在firstLen长度的子数组左边
    //    遍历所有可能的firstLen子数组起始位置
    for (int i = secondLen, t = 0; i + firstLen - 1 < n; ++i) {
        // t记录在当前位置i之前，所有长度为secondLen的子数组的最大和
        t = Math.max(t, s[i] - s[i - secondLen]);
        // 当前firstLen子数组的和：s[i + firstLen] - s[i]
        // 加上左边secondLen子数组的最大和t
        ans = Math.max(ans, t + s[i + firstLen] - s[i]);
    }
    
    return ans;
}
```

[89. 格雷编码](https://leetcode.cn/problems/gray-code/)

背吧，它的详细公式是

```java
public List<Integer> grayCode(int n) {
    // 1. 初始化结果列表，格雷码序列始终以0开始
    List<Integer> result = new ArrayList<>();
    result.add(0);  // 0位格雷码序列：[0]

    // 2. 逐位构建格雷码
    for (int i = 0; i < n; i++) {
        // 3. 记录当前列表大小（用于镜像操作）
        int size = result.size();
        
        // 4. 镜像反射：从后往前遍历当前列表
        for (int j = size - 1; j >= 0; j--) {
            // 5. 在镜像部分的前面加一个1（即最高位）
            //    1 << i 表示在第i位（从0开始）设置1
            //    result.get(j) | (1 << i) 将第i位置为1
            int newCode = result.get(j) | (1 << i);
            result.add(newCode);
        }
    }
    
    return result;
}
```

[LCR 184. 设计自助结算系统](https://leetcode.cn/problems/dui-lie-de-zui-da-zhi-lcof/)

维护两个队列，一个普通队列一个单调队列

[1532 · 序列化和反序列N叉树 - LintCode](https://www.lintcode.com/problem/1532/)

又把自己绕晕了

```java
public String serialize(DirectedGraphNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        // 移除最后一个多余的逗号
        if (sb.length() > 0) sb.setLength(sb.length() - 1);
        return sb.toString();
    }

    /**
     * 递归序列化辅助方法
     */
    private void serializeHelper(DirectedGraphNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#,");
            return;
        }
        // 1. 添加当前节点标签
        sb.append(node.label).append(",");
        // 2. 递归序列化所有子节点
        for (DirectedGraphNode child : node.neighbors) {
            serializeHelper(child, sb);
        }
        // 3. 添加子树结束标记，这个千万别忘了，不是只有null才添加子树标记
        sb.append("#,");
    }

    /**
     * 反序列化字符串为多叉树
     */
    public DirectedGraphNode deserialize(String data) {
        if (data.isEmpty()) return null;
        String[] tokens = data.split(",");
        idx = 0; // 重置索引
        return deserializeHelper(tokens);
    }

    // 全局索引，用于反序列化时跟踪当前处理位置
    private int idx;
    /**
     * 递归反序列化辅助方法
     */
    private DirectedGraphNode deserializeHelper(String[] tokens) {
        // 1. 处理空节点
        if (idx >= tokens.length || tokens[idx].equals("#")) {
            idx++; // 跳过结束标记
            return null;
        }
        // 2. 创建当前节点
        DirectedGraphNode node = new DirectedGraphNode(Integer.parseInt(tokens[idx++]));
        // 3. 递归处理所有子节点，直到遇到结束标记
        while (idx < tokens.length && !tokens[idx].equals("#")) {
            node.neighbors.add(deserializeHelper(tokens));
        }
        // 4. 跳过当前子树的结束标记
        if (idx < tokens.length && tokens[idx].equals("#")) {
            idx++;
        }
        return node;
    }
```

[1493. 删掉一个元素以后全为 1 的最长子数组](https://leetcode.cn/problems/longest-subarray-of-1s-after-deleting-one-element/)

滑动窗口，相当于维护最多一个零的窗口，求最大窗口长度

```java
public int longestSubarray(int[] nums) {
    int left = 0;
    int zeroCount = 0;
    int maxLen = 0;
    for (int right = 0; right < nums.length; right++) {
        // 统计0的数量
        if (nums[right] == 0) zeroCount++;
        // 维护窗口内最多1个0
        while (zeroCount > 1) {
            if (nums[left] == 0) zeroCount--;
            left++;
        }
        // 计算当前有效窗口长度（需要减1，因为必须删除1个元素）
        maxLen = Math.max(maxLen, right - left);
    }
    return maxLen;
}
```

[449. 序列化和反序列化二叉搜索树](https://leetcode.cn/problems/serialize-and-deserialize-bst/)

记住了，序列化和反序列化只能使用前序遍历，只有前序保留了节点之间的父子关系，没有多余的操作

```java
 public String serialize(TreeNode root) {
        StringBuilder sb=new StringBuilder();
        serialize(root,sb);
        return sb.deleteCharAt(sb.length()-1).toString();
    }

    void serialize(TreeNode root,StringBuilder sb)
    {
        if(root==null){
            sb.append("#,");
            return;
        }
        sb.append(root.val).append(',');
        serialize(root.left,sb);
        serialize(root.right,sb);
    }
    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        return deserialize(data.split(","));
    }

    int idx;
    TreeNode deserialize(String[] data)
    {
        if(idx==data.length||data[idx].equals("#"))
        {
            ++idx;
            return null;
        }
        TreeNode node=new TreeNode(Integer.parseInt(data[idx++]));
        TreeNode left=deserialize(data);
        TreeNode right=deserialize(data);
        node.left=left;
        node.right=right;
        return node;
    }
```

[1353. 最多可以参加的会议数目](https://leetcode.cn/problems/maximum-number-of-events-that-can-be-attended/)

一点点的增加天数，维护一个优先队列，存的是结束时间，结束时间越早越先去开

```java
public int maxEvents(int[][] events) {
        Arrays.sort(events, Comparator.comparingInt(a -> a[0]));
        PriorityQueue<Integer> pq=new PriorityQueue<>();
        int res=0,idx=0,day=1;
        while(idx<events.length||!pq.isEmpty())
        {
            while(idx<events.length&&events[idx][0]==day)
            {
                pq.add(events[idx][1]);
                ++idx;
            }
            while(!pq.isEmpty()&&pq.peek()<day)
                pq.poll();
            if(!pq.isEmpty())
            {
                ++res;
                pq.poll();
            }
            ++day;
        }
        return res;
    }
```

[LCR 173. 点名](https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/)

可以直接遍历，只要和i、不相等就说明是i缺失了

思路二，二分查找，只要mid和mid相等，就肯定在右边。不然在左边，返回left

[951. 翻转等价二叉树](https://leetcode.cn/problems/flip-equivalent-binary-trees/)

把两种情况都包括了就行

```java
public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        if(root1 == null || root2 == null) {
        	return root1 == root2;
        }
        if(root1.val != root2.val) {
        	return false;
        }

        return (flipEquiv(root1.left, root2.left) && flipEquiv(root1.right, root2.right)) 
        		|| (flipEquiv(root1.right, root2.left) && flipEquiv(root1.left, root2.right));
    }

//不怕麻烦还有底下这种更加深入的
public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        if(root1==null&&root2==null) return true;
        if(root1==null||root2==null) return false;
        if(root1.val!= root2.val) return false;
        if(root1.left==null)
        {
            if(root2.left==null)
                return flipEquiv(root1.left,root2.left)&&flipEquiv(root1.right,root2.right);
            else return flipEquiv(root1.left,root2.right)&&flipEquiv(root1.right,root2.left);
        }else{
            if(root2.left!=null&&root2.left.val==root1.left.val)
                return flipEquiv(root1.left,root2.left)&&flipEquiv(root1.right,root2.right);
            else return flipEquiv(root1.left,root2.right)&&flipEquiv(root1.right,root2.left);
        }
    }
```

[738. 单调递增的数字](https://leetcode.cn/problems/monotone-increasing-digits/)

从后往前找，找到最后一个乱序的地方，就是前一个数字比当前数字要大，前面数字减一，当前数字到最后都得改成9

[LCR 137. 模糊搜索验证](https://leetcode.cn/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

正则表达式匹配，主要是初始化的问题，就是第二个字符都是'*'的时候要连续置为true；

```java
public boolean articleMatch(String s, String p) {
        boolean dp[][]=new boolean[s.length()+1][p.length()+1];
        dp[0][0]=true;
        for(int i=2;i<=p.length();i+=2)
            if(p.charAt(i-1)=='*')
                dp[0][i]=dp[0][i-2];
            else break;
        for(int i=0;i<s.length();++i)
        {
            for(int j=0;j<p.length();++j)
            {
                if(s.charAt(i)==p.charAt(j)||p.charAt(j)=='.')
                    dp[i+1][j+1]=dp[i][j];
                else if(j+1<p.length()&&p.charAt(j+1)=='*')
                    continue;//这个判断可以不要
                else if(p.charAt(j)=='*')
                {
                    char curchar=p.charAt(j-1);
                    dp[i+1][j+1]=dp[i+1][j-1];
                    if(curchar==s.charAt(i)||curchar=='.')
                        dp[i+1][j+1]|=dp[i][j+1];
                }else dp[i+1][j+1]=false;
            }
        }
        return dp[s.length()][p.length()];
    }
```

还可以写的更加精炼，只有是星号和不是星号两种情况

```java
for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            char pj = p.charAt(j - 1);
            if (pj != '*') {
                // 直接匹配一个字符
                if (p.charAt(j - 1) == '.' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            } else {
                // pj == '*': 两种情况
                // 1) 把 x* 当作空串（零次）
                dp[i][j] = dp[i][j - 2];

                // 2) 至少出现一次：当前字符要能匹配 x（即 p[j-2]）
                char x = p.charAt(j - 2);
                if (x == '.' || x == s.charAt(i - 1)) {
                    dp[i][j] |= dp[i - 1][j];
                }
            }
        }
    }
```

[886. 可能的二分法](https://leetcode.cn/problems/possible-bipartition/)

两种方法，染色法和并查集法

并查集法，并查集加镜像反向点

```java
class Solution {
    // 并查集父节点数组（扩展到2n空间）
    int[] p = new int[4010]; // 题目约束n<=2000，所以2n<=4000
    
    // 路径压缩的查找方法
    int find(int x) {
        if (p[x] != x) {
            p[x] = find(p[x]); // 路径压缩
        }
        return p[x];
    }
    
    // 合并两个集合
    void union(int a, int b) {
        p[find(a)] = find(b); // 将a的根节点指向b的根节点
    }
    
    // 检查两个元素是否同属一个集合
    boolean query(int a, int b) {
        return find(a) == find(b);
    }
    
    public boolean possibleBipartition(int n, int[][] ds) {
        
        for (int i = 1; i <= 2 * n; i++) {
            p[i] = i;
        }
        
        // 处理每对不喜欢关系
        for (int[] info : ds) {
            int a = info[0], b = info[1];
            
            // 如果a和b已经在同一组，说明冲突
            if (query(a, b)) {
                return false;
            }
            
            // 将a与b的镜像（b+n）合并，表示a和b必须在不同组
            union(a, b + n);
            // 将b与a的镜像（a+n）合并，对称操作
            union(b, a + n);
        }
        return true;
    }
}
```

染色法，先建立图然后使用二分图去写

```Java
	ArrayList<ArrayList<Integer>> graph;
    public boolean possibleBipartition(int n, int[][] dislikes) {
        int colors[]=new int[n+1];
        graph=new ArrayList<>();
        for(int i=0;i<=n;++i)
            graph.add(new ArrayList<>());
        for(int []dis:dislikes)
        {
            graph.get(dis[0]).add(dis[1]);
            graph.get(dis[1]).add(dis[0]);
        }
        for (int i = 0; i < n; i++) {
            if (colors[i] == 0) { // 未访问过
                if (!dfs(i, colors,1)) return false;
            }
        }
        return true;
    }

    public boolean dfs(int i, int[] colors,int color) {
        if (colors[i] == 0)
            colors[i]=color;
        else return colors[i]==color;
        for (int neighbor : graph.get(i)) { // 遍历邻居
            if (!dfs(neighbor, colors,color==1?-1:1)) return false;
        }
        return true;
    }
```

[949. 给定数字能组成的最大时间](https://leetcode.cn/problems/largest-time-for-given-digits/)

暴力解法，暴力解出答案

```java
public String largestTimeFromDigits(int[] arr) {
        int ans = -1;
        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 4; j++) {
                if (j == i) {
                    continue;
                }
                int hour = 10 * arr[i] + arr[j];
                if(hour>=24) continue;
                for (int k = 0; k < 4; k++) {
                    if (k == i || k == j) {
                        continue;
                    }
                    int l = 6 - i - j - k;
                    int mins = 10 * arr[k] + arr[l];
                    if (mins < 60) {
                        ans = Math.max(ans, hour * 60 + mins);
                    }
                }
            }
        }
        return ans >= 0 ? String.format("%02d:%02d", ans / 60, ans % 60) : "";
    }
```

[1423. 可获得的最大点数](https://leetcode.cn/problems/maximum-points-you-can-obtain-from-cards/)

可以使用前缀和

```java
public int maxScore(int[] cardPoints, int k) {
        int prefix[]=new int[cardPoints.length+1];
        for(int i=1;i<=cardPoints.length;++i)
            prefix[i]=prefix[i-1]+cardPoints[i-1];
        int restlen=cardPoints.length-k,res=Integer.MIN_VALUE;
        for(int i=restlen;i<=cardPoints.length;++i)
            res=Math.max(res,prefix[cardPoints.length]-(prefix[i]-prefix[i-restlen]));
        return res;
    }
```

可以使用滑动窗口，维护len-k大的窗口然后减就好

[1195. 交替打印字符串](https://leetcode.cn/problems/fizz-buzz-multithreaded/)

信号量机制

```java
class FizzBuzz {
    private int n;

    private Semaphore number = new Semaphore(1);
    private Semaphore fizz = new Semaphore(0);
    private Semaphore buzz = new Semaphore(0);
    private Semaphore fizzbuzz = new Semaphore(0);


    public FizzBuzz(int n) {
        this.n = n;
    }

    // printFizz.run() outputs "fizz".
    public void fizz(Runnable printFizz) throws InterruptedException {
        for (int i = 1; i <= n; i++) {
            if (i % 3 == 0 && i % 5 != 0) {
                fizz.acquire();
                printFizz.run();
                number.release();
            }
        }
    }

    // printBuzz.run() outputs "buzz".
    public void buzz(Runnable printBuzz) throws InterruptedException {
        for (int i = 1; i <= n; i++) {
            if (i % 3 != 0 && i % 5 == 0) {
                buzz.acquire();
                printBuzz.run();
                number.release();
            }
        }
    }

    // printFizzBuzz.run() outputs "fizzbuzz".
    public void fizzbuzz(Runnable printFizzBuzz) throws InterruptedException {
        for (int i = 1; i <= n; i++) {
            if (i % 3 == 0 && i % 5 == 0) {
                fizzbuzz.acquire();
                printFizzBuzz.run();
                number.release();
            }
        }
    }

    // printNumber.accept(x) outputs "x", where x is an integer.
    public void number(IntConsumer printNumber) throws InterruptedException {
        for (int i = 1; i <= n; i++) {
            number.acquire();
            if (i % 3 != 0 && i % 5 != 0) {//开始打印
                printNumber.accept(i);
                number.release();
            } else if (i % 3 == 0 && i % 5 != 0) {//fizz开始打印
                fizz.release();
            } else if (i % 3 != 0 && i % 5 == 0) {//buzz开始打印
                buzz.release();
            } else {
                fizzbuzz.release();//fizzbuzz开始打印
            }
        }
    }
}

```

[1026. 节点与其祖先之间的最大差值](https://leetcode.cn/problems/maximum-difference-between-node-and-ancestor/)

递归的递，最大值最小值一直往下传，传到叶子节点就说明找到一条路径，计算这条路径的最大差值然后返回

```java
int res = Integer.MIN_VALUE;
    public int maxAncestorDiff(TreeNode root) {
        dfs(root,root.val,root.val);
        return res;
    }

    public void dfs(TreeNode root,int max,int min){
        if(root == null){
            res = Math.max(res,max-min);
            return;
        }
        max = Math.max(max,root.val);
        min = Math.min(min,root.val);
        dfs(root.left,max,min);
        dfs(root.right,max,min);
    }
```

[292. Nim 游戏](https://leetcode.cn/problems/nim-game/)

大道至简，只要凑齐了4的倍数+1就可以必赢

```java
return n % 4 != 0;
```

[987. 二叉树的垂序遍历](https://leetcode.cn/problems/vertical-order-traversal-of-a-binary-tree/)

```java
class Solution {
    // 优先队列：用于存储所有节点的坐标和值信息，并自动排序
    // 每个元素是int[3]数组：[列号, 行号, 节点值]
    // 排序规则：先按列号升序，再按行号升序，最后按节点值升序
    PriorityQueue<int[]> q = new PriorityQueue<>((a, b) -> {
        if (a[0] != b[0]) return a[0] - b[0]; // 列号优先
        if (a[1] != b[1]) return a[1] - b[1]; // 行号其次
        return a[2] - b[2]; // 值最后
    });
    
    public List<List<Integer>> verticalTraversal(TreeNode root) {
        // 1. 处理根节点：坐标为(0,0)
        int[] rootInfo = new int[]{0, 0, root.val};
        q.add(rootInfo);
        // 2. 递归遍历整棵树，收集所有节点信息
        dfs(root, rootInfo);
        // 3. 构建结果列表
        List<List<Integer>> ans = new ArrayList<>();
        // 4. 按列分组处理节点
        while (!q.isEmpty()) {
            List<Integer> tmp = new ArrayList<>(); // 当前列的节点值列表
            int[] first = q.peek(); // 获取当前列的第一个节点
            int currentCol = first[0]; // 当前列号
            // 5. 将同一列的所有节点值加入临时列表
            while (!q.isEmpty() && q.peek()[0] == currentCol) {
                tmp.add(q.poll()[2]); // 取出节点值
            }
            ans.add(tmp); // 将当前列的结果加入最终答案
        }
        return ans;
    }
    
    /**
     * 深度优先搜索遍历二叉树
     * @param root 当前节点
     * @param parentInfo 父节点的坐标信息 [列号, 行号, 值]
     */
    void dfs(TreeNode root, int[] parentInfo) {
        // 处理左子节点
        if (root.left != null) {
            // 左子节点坐标：列号-1，行号+1
            int[] leftInfo = new int[]{
                parentInfo[0] - 1, // 列号 = 父列 - 1
                parentInfo[1] + 1, // 行号 = 父行 + 1
                root.left.val      // 节点值
            };
            q.add(leftInfo); // 加入优先队列
            dfs(root.left, leftInfo); // 递归处理左子树
        }
        
        // 处理右子节点
        if (root.right != null) {
            // 右子节点坐标：列号+1，行号+1
            int[] rightInfo = new int[]{
                parentInfo[0] + 1, // 列号 = 父列 + 1
                parentInfo[1] + 1, // 行号 = 父行 + 1
                root.right.val     // 节点值
            };
            q.add(rightInfo); // 加入优先队列
            dfs(root.right, rightInfo); // 递归处理右子树
        }
    }
}
```

[829. 连续整数求和](https://leetcode.cn/problems/consecutive-numbers-sum/)

![1760012833972](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760012833972.png)

```java
class Solution {
    public int consecutiveNumbersSum(int n) {
        int ans = 0; n *= 2;
        for (int k = 1; k * k < n; k++) {
            if (n % k != 0) continue;  //满足（2a+k-1）*k=2n的两个条件
            if ((n / k - (k - 1)) % 2 == 0) ans++;
        }
        return ans;
    }
}
```

[LCR 114. 火星词典](https://leetcode.cn/problems/Jf1JuT/)

难死了，先建立一个图，标记边，入度，是否出现过，然后判断这个图是不是拓扑的

```java
public static String alienOrder(String[] words) {
    // 1. 初始化数据结构
    // graph[i][j]: 是否存在从字符i到字符j的边（即i在j之前）
    boolean[][] graph = new boolean[26][26];
    // inDegree[i]: 字符i的入度（有多少字符必须在i之前）
    int[] inDegree = new int[26];
    // exist[i]: 字符i是否在字典中出现过
    boolean[] exist = new boolean[26];

    // 2. 标记所有在字典中出现的字符
    for (int i = 0; i < words.length; i++) {
        for (char c : words[i].toCharArray()) {
            exist[c - 'a'] = true;
        }
    }

    // 3. 构建图：比较相邻单词，建立字符顺序关系
    for (int i = 0; i < words.length - 1; i++) {
        int j = i + 1;
        char[] word1 = words[i].toCharArray();
        char[] word2 = words[j].toCharArray();
        
        // 找到第一个不同的字符位置
        int cur = 0;
        while (cur < word1.length && cur < word2.length && word1[cur] == word2[cur]) {
            cur++;
        }
        
        // 特殊情况：如果word2是word1的前缀，且word1更长，则非法
        // 例如：["abc", "ab"] → 违反字典序规则
        if (cur < word1.length && cur == word2.length) {
            return "";
        }
        
        // 如果找到不同的字符，建立边的关系
        if (cur < word1.length && cur < word2.length) {
            char from = word1[cur]; // 前面的字符
            char to = word2[cur];   // 后面的字符
            
            // 避免重复添加相同的边
            if (!graph[from - 'a'][to - 'a']) {
                inDegree[to - 'a']++; // 增加目标字符的入度
                graph[from - 'a'][to - 'a'] = true; // 标记边存在
            }
        }
    }

    // 4. 执行拓扑排序
    return topoSort(graph, inDegree, exist);
}

/**
 * 拓扑排序（Kahn算法）
 * @param graph 邻接矩阵表示的图
 * @param inDegree 入度数组
 * @param exist 存在标记数组
 * @return 拓扑排序结果（字母顺序）
 */
private static String topoSort(boolean[][] graph, int[] inDegree, boolean[] exist) {
    StringBuilder order = new StringBuilder(); // 结果字符串
    Queue<Integer> queue = new LinkedList<>(); // BFS队列
    int count = 0; // 统计实际存在的字符数量
    
    // 5. 初始化：统计字符数，将入度为0的字符加入队列
    for (int i = 0; i < 26; i++) {
        if (exist[i]) {
            count++;
            if (inDegree[i] == 0) {
                queue.add(i);
            }
        }
    }
    
    // 6. BFS处理队列
    while (!queue.isEmpty()) {
        int i = queue.poll(); // 取出当前字符
        order.append((char)(i + 'a')); // 添加到结果
        
        // 7. 处理当前字符的所有出边
        for (int j = 0; j < 26; j++) {
            if (graph[i][j]) {
                graph[i][j] = false; // 移除边（避免重复处理）
                inDegree[j]--; // 减少邻居的入度
                
                // 如果邻居入度变为0，加入队列
                if (inDegree[j] == 0) {
                    queue.add(j);
                }
            }
        }
    }
    
    // 8. 检查是否存在环：如果排序的字符数不等于总字符数，说明有环
    return order.length() == count ? order.toString() : "";
}
```

[1115. 交替打印 FooBar](https://leetcode.cn/problems/print-foobar-alternately/)

使用类锁和flag机制

```java
private Object lock = new Object();
    private volatile boolean flag = true;
    public void foo(Runnable printFoo) throws InterruptedException {
        
        for (int i = 0; i < n; i++) {
            synchronized(lock){
                // printFoo.run() outputs "foo". Do not change or remove this line.

                if(flag==false){
                     lock.wait();
                }
                printFoo.run();
                flag=false;
                lock.notify();
            }

        }
    }
```

[面试题 16.26. 计算器](https://leetcode.cn/problems/calculator-lcci/)

全都转成加法来做，碰到乘除直接算了，碰到加就把数放进去，碰到减就把负的数放进去

```java
public int calculate(String s) {
         Deque<Integer> stack= new LinkedList<>();
        int num=0;
        char opt='+';
        for (int i = 0; i < s.length(); i++) {
            char c=s.charAt(i);
            if(Character.isDigit(c))num=num*10+(c-'0');
            if(!Character.isDigit(c)&&c!=' '||i==s.length()-1){
                if(opt=='+')stack.push(num);
                else if(opt=='-')stack.push(-num);
                else if(opt=='*')stack.push(stack.pop()*num);
                else stack.push(stack.pop()/num);
                num=0;
                opt=c;
            }
        }
        int res=0;
        while (!stack.isEmpty()){
            res+=stack.pop();
        }
        return res;
    }
```

类似的写法

```java
	char cursign='+';
    Deque<Integer> nums=new LinkedList<>();
    int nownum=0;
    public int calculate(String s) {
        for(char c:s.toCharArray())
        {
            if(Character.isDigit(c))
                nownum=nownum*10+c-'0';
            if(!Character.isDigit(c)&&c!=' ')
            {
                cal();
                cursign=c;
                nownum=0;
            }
        }
        cal();
        int res=0;
        while(!nums.isEmpty())
            res+=nums.poll();
        return res;
    }

    void cal()
    {
        if(cursign=='+')
            nums.add(nownum);
        else if(cursign=='-')
            nums.add(-nownum);
        else if(cursign=='*')
            nums.add(nums.pollLast()*nownum);
        else nums.add(nums.pollLast()/nownum);
    }
```

[面试题 17.15. 最长单词](https://leetcode.cn/problems/longest-word-lcci/)

先排序，用哈希表查找快一点，用substring对长度限定

```java
public String longestWord(String[] words) {
        // 枚举words[i]：DFS()中枚举分割点检查子串是否在words

        Arrays.sort(words, (a, b) -> a.length() != b.length() ? b.length() - a.length() : a.compareTo(b));
        Set<String> set = new HashSet<>(Arrays.asList(words));
        for (String s : words) {
            if (dfs(s, 0, set))  // 按长度、字典序排序后：首次找到的即所求
                return s;
        }
        return "";
    }

    boolean dfs(String s, int i, Set<String> set) {
        int n = s.length();
        if (i == n)
            return true;

        int k = (i == 0) ? n - 1 : n;//等于零的时候至少要分成两部分
        for (int j = i; j <= k; j++) { // [i,j)
            if (set.contains(s.substring(i, j)) && dfs(s, j, set)) {
                return true;
            }
        }
        return false;
    }
```

自己写的没用哈希表，有点贪心的想法在里面

```java
public String longestWord(String[] words) {
        Arrays.sort(words,(a,b)->a.length()==b.length()?a.compareTo(b):b.length()-a.length());
        for(int i=0;i<words.length;++i)
        {
            String temp=words[i];
            if(dfs(words[i],0,words,i+1))
                return words[i];
        }
        return "";
    }

    boolean dfs(String word,int idx,String[] words,int begin)
    {
        if(idx==word.length()) return true;
        String now=word.substring(idx);
        for(int i=begin;i<words.length;++i)
        {
            if(now.startsWith(words[i]))
                if(dfs(word,idx+words[i].length(),words,begin))
                    return true;
        }
        return false;
    }
```

[983. 最低票价](https://leetcode.cn/problems/minimum-cost-for-tickets/)

```java
public int mincostTickets(int[] days, int[] costs) {
        int dp[]=new int[days.length+1],dur[]={1,7,30};
        Arrays.fill(dp,Integer.MAX_VALUE);
        dp[0]=0;//初始化用的  dp[i]代表的是days[i-1]天之前所有的最小花费
        for(int i=1;i<=days.length;++i)
        {
            for(int j=0;j<dur.length;++j)
            {
                int k=i-1;
                while(k>=0&&days[i-1]-days[k]<dur[j])//注意这里没有等号，比如说七天应该是六天前买的而不是七天前买的，找到第一个超过期限时间的下标的前一个
                    --k;
                dp[i]=Math.min(dp[i],dp[k+1]+costs[j]);//所以这里是 k+1下标找下一个
            }
        }
        return dp[days.length];
    }
```

[1249. 移除无效的括号](https://leetcode.cn/problems/minimum-remove-to-make-valid-parentheses/)

轻微的贪心算法，有点子绕

```java
public String minRemoveToMakeValid(String s) {
        StringBuilder sb=new StringBuilder();
        int left=0;
        for(char c:s.toCharArray())
        {
            if(Character.isLetter(c))
                sb.append(c);
            else if(c=='(')
            {
                sb.append(c);
                ++left;
            }else{
                if(left>0)
                {
                    sb.append(c);
                    --left;
                }
            }
        }//使用贪心去掉多的右括号
        for(int i=sb.length()-1;left>0&&i>=0;)//从右往左去掉多余的左括号
        {
            if(sb.charAt(i)=='(')
            {
                sb.deleteCharAt(i);
                --left;
                if(i==sb.length())//如果删掉的左括号的位置正好是最后一个元素那指针也要左移
                    --i;
            }else --i;
        }
        return sb.toString();
    }
```

[1227. 飞机座位分配概率](https://leetcode.cn/problems/airplane-seat-assignment-probability/)

![1760074657762](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760074657762.png)

```java
public double nthPersonGetsNthSeat(int n) {
        return n == 1 ? 1.0 : 0.5;
    }
```

[791. 自定义字符串排序](https://leetcode.cn/problems/custom-sort-string/)

方法一，自己想的，很慢。先用map设立优先级，然后分两个串，在map中的加入ArrayList，否则加入结果集，把数组根据map排序，加入结果集

方法二，哈希存，根据order集一个个加入，不在里面的再扫描一次加入

```java
public String customSortString(String order, String s) {
        int[] count = new int[128];
        for (char c : s.toCharArray()) {
            count[c]++;
        }

        StringBuilder ans = new StringBuilder();
        // 存在顺序要求的
        for (char c : order.toCharArray()) {
            for (int i = 0; i < count[c]; ++i) {
                ans.append(c);
            }
            count[c] = 0;
        }
        // 不存在顺序要求的
        for (char c = 'a'; c <= 'z'; ++c) {
            for (int i = 0; i < count[c]; ++i) {
                ans.append(c);
            }
        }
        return ans.toString();
    }
```

[116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)

自顶向下，连接next指针，主要是左孩子连接右孩子，右孩子连接下一个next的左孩子

另一个思路，BFS然后每一层单独连接next就行

```java
public Node connect(Node root) {
        if(root==null||root.left==null&&root.right==null) return root;
        root.left.next=root.right;
        if(root.next!=null)
            root.right.next=root.next.left;
        connect(root.left);
        connect(root.right);
        return root;
    }
```

[697. 数组的度](https://leetcode.cn/problems/degree-of-an-array/)

一次遍历就可以完成的方法，就是使用一个哈希，存之前的数字出现的最早下标和出现的次数，如果次数是最大的直接替换最短长度，如果和最大长度相等对比最短长度取最短

```java
public int findShortestSubArray(int[] nums) {
        HashMap<Integer,int[]> hm=new HashMap<>();
        int maxCount = 0;                // 记录最大频数（数组的度）
        int minWindow = 0;               // 记录最短子数组长度
        for(int i=0;i<nums.length;++i)
        {
            int pair[]=hm.get(nums[i]);
            if(pair==null)
            {
                pair=new int[]{i,1};
                hm.put(nums[i],pair);
            }else{
                pair[1]++;
            }
            if(pair[1]>maxCount)
            {
                maxCount=pair[1];
                minWindow=i-pair[0]+1;
            }else if(pair[1]==maxCount)
                minWindow=Math.min(minWindow,i-pair[0]+1);
        }
        return minWindow;
    }
```

[538. 把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)

全局变量sum，遍历顺序右中左就行

```java
public TreeNode convertBST(TreeNode root) {
        travel(root);
        return root;
    }

    int sum=0;
    void travel(TreeNode root)
    {
        if(root==null) return;
        travel(root.right);
        sum+=root.val;
        root.val=sum;
        travel(root.left);
    }
```

[805. 数组的均值分割](https://leetcode.cn/problems/split-array-with-same-average/)

特别难我已经蒙圈了

```java
public boolean splitArraySameAverage(int[] nums) {
        int n = nums.length;      // 数组长度
        int m = n / 2;            // 将数组分成两半，m为前半部分长度
        int sum = 0;              // 数组所有元素的总和
        // 1. 计算总和
        for (int x : nums) {
            sum += x;
        }
        // 2. 预处理左半部分：存储所有可能的(和, 元素个数)组合
        // key: 子集和, value: 对应这个和的所有可能元素个数
        Map<Integer, Set<Integer>> map = new HashMap<>();
        // 3. 遍历左半部分的所有子集（使用位掩码）
        for (int s = 0; s < (1 << m); s++) {//是1左移m位，比如一半是4个数那左移四位是从0000-1111（0-15）
            int tot = 0;  // 当前子集的和
            int cnt = 0;  // 当前子集的元素个数
            // 4. 计算当前位掩码对应的子集和与元素个数
            for (int i = 0; i < m; i++) {
                if (((s >> i) & 1) == 1) {  // 检查第i位是否为1
                    tot += nums[i];
                    cnt++;
                }
            }
            // 5. 将结果存入HashMap
            Set<Integer> set = map.getOrDefault(tot, new HashSet<>());
            set.add(cnt);
            map.put(tot, set);
        }
        // 6. 遍历右半部分的所有子集
        for (int s = 0; s < (1 << (n - m)); s++) {
            int tot = 0;  // 当前子集的和
            int cnt = 0;  // 当前子集的元素个数
            // 7. 计算右半部分子集的和与元素个数
            for (int i = 0; i < (n - m); i++) {
                if (((s >> i) & 1) == 1) {
                    tot += nums[i + m];  // 注意索引偏移
                    cnt++;
                }
            }
            // 8. 检查所有可能的子集大小k
            for (int k = Math.max(1, cnt); k < n; k++) {
                // 9. 数学条件检查：k * sum必须能被n整除
                if (k * sum % n != 0) {
                    continue;
                }
                // 10. 计算左半部分需要的和
                int targetSum = k * sum / n;  // 需要的总目标
                int leftNeeded = targetSum - tot;  // 左半部分需要贡献的和
                // 11. 检查左半部分是否存在这样的子集
                if (!map.containsKey(leftNeeded)) {
                    continue;
                }
                if (!map.get(leftNeeded).contains(k - cnt)) {
                    continue;
                }
                // 12. 找到满足条件的组合
                return true;
            }
        }
        return false;
    }
```

![1760183545368](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760183545368.png)

9的推导

[1024. 视频拼接](https://leetcode.cn/problems/video-stitching/)

```java
public int videoStitching(int[][] clips, int time) {
        Arrays.sort(clips,Comparator.comparingInt(a->a[0]));
        int cnt=0;
        int curlen=0,idx=0,nextlen=0;
        while(curlen<time)
        {
            while(idx<clips.length&&clips[idx][0]<=curlen)
                nextlen=Math.max(nextlen,clips[idx++][1]);
            if(nextlen==curlen) return -1;//如果最长长度没发生变化说明中间断层了
            ++cnt;
            curlen=nextlen;
        }
        return cnt;
    }
```

[面试题 08.11. 硬币](https://leetcode.cn/problems/coin-lcci/)

完全背包问题，先遍历硬币再遍历数组求的是组合数，有一点贪心在里面，反过来求的就是排列数了![1760239500957](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760239500957.png)



```java
public int waysToChange(int n) {
        int dp[]=new int[n+1];
        dp[0]=1;
        int coins[]=new int[]{1,5,10,25};
        for(int coin:coins)
        {
            for(int i=coin;i<=n;++i)
                dp[i]=(dp[i]+dp[i-coin])%1000000007;
        }
        return dp[n];
    }
```

[1156. 单字符重复子串的最大长度](https://leetcode.cn/problems/swap-for-longest-repeated-character-substring/)

这个地方比较难，使用滑动窗口，主要是for循环里面的，要好好思考一下，先是维护窗口里面最大的那个值，再是比较窗口大小和最大值加一大小来缩小左边界

```java
public int maxRepOpt1(String text) {
        int[] cntTotal = new int[26]; // 统计每个字符在字符串中出现的次数
        int[] cnt = new int[26]; // 统计滑动窗口中的字符数
        int res = 0;
        int left = 0;
        int maxCnt = 0; // 窗口内出现的最多字符的计数
        char[] cs = text.toCharArray();
        for(char c : cs){
            cntTotal[c - 'a']++;
        }

        for(int i = 0; i < cs.length; i++){
            cnt[cs[i] - 'a']++;
            if(cntTotal[cs[i] - 'a'] - 1 > maxCnt){ // 若当前字符的剩余可用数量（总数减1）大于 maxCount，则更新 maxCount为当前字符在窗口内的计数。通过此条件确保窗口外还有可用字符用于交换。
                maxCnt = Math.max(maxCnt, cnt[cs[i] - 'a']);
            }

            while(i - left + 1 > maxCnt + 1){ // 窗口长度 (i-left+1)必须满足 ≤ maxCount + 1。若超过，说明窗口内非目标字符超过1个，需收缩左边界。
                cnt[cs[left++] - 'a']--;
            }

            res = Math.max(res, i - left + 1);
        }
        return res;
    }
```

[687. 最长同值路径](https://leetcode.cn/problems/longest-univalue-path/)

选的是一个路径，那么对于当前节点，要么选择左节点要么选择右节点，选最多的，然后确定返回值的含义，返回值表示的是当前值的出现相连的边的数

```java
int max;
    public int longestUnivaluePath(TreeNode root) {
        if(root==null) return 0;
        travel(root);
        return max;
    }

    
    int travel(TreeNode root)
    {
        if(root==null) return 0;
        int l=travel(root.left),r=travel(root.right),cur=0,res=0;
        if(root.left!=null&&root.val==root.left.val)
        {
            res=l+1;
            cur+=l+1;
        }
        if(root.right!=null&&root.val==root.right.val)
        {
            res=Math.max(res,r+1);
            cur+=r+1;
        }
        max=Math.max(max,cur);
        return res;
    }
```

[90. 子集 II](https://leetcode.cn/problems/subsets-ii/)

不要再取相似的变量名了，又把i全部写成idx了 ！ ！ ！ 

```java
for(int i=idx;i<nums.length;++i)
        {
            if(i>0&&nums[i]==nums[i-1]&&!visited[i-1])continue;
            dq.add(nums[i]);
            visited[i]=true;
            travel(nums,i+1,visited);
            dq.removeLast();
            visited[i]=false;
        }
```

[980. 不同路径 III](https://leetcode.cn/problems/unique-paths-iii/)

先找到起始点，然后计算所有步数总和，然后递归找路径，递减步数，当步数等于0的时候就增加1，travel的含义是从这个点开始使用这么多步数能不能刚好走到终点，注意步数初始化要为一，因为最后走到2的时候步数多减了一个1，步数等于0的个数+1

```java
public int uniquePathsIII(int[][] grid) {
        int step=1,x=0,y=0;
        for(int i=0;i<grid.length;++i)
            for(int j=0;j<grid[0].length;++j)
                if(grid[i][j]==0)
                {
                    step++;
                }else if (grid[i][j]==1){
                    x=i;y=j;
                }
        return dfs(grid,x,y,step);
    }

    int dir[][]={{0,1},{0,-1},{1,0},{-1,0}};
    int dfs(int[][] grid,int x,int y,int step)
    {
        if(grid[x][y]==2) return step==0?1:0;
        grid[x][y]=-1;
        int res=0;
        for(int i=0;i<4;++i)
        {
            int nextx=x+dir[i][0],nexty=y+dir[i][1];
            if(nextx<0||nextx>= grid.length||nexty<0||
                    nexty>= grid[0].length||grid[nextx][nexty]==-1)continue;
            res+=dfs(grid,nextx,nexty,step-1);
        }
        grid[x][y]=0;
        return res;
    }
```

[1235. 规划兼职工作](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/)

```java
class Solution {
    public int jobScheduling(int[] st, int[] et, int[] ps) {
        int n = st.length;

        // list 中每个元素形如 [start, end, profit]
        // 之所以先装进 List 是为了便于按结束时间排序
        List<int[]> list = new ArrayList<>();
        for (int i = 0; i < n; i++) list.add(new int[]{st[i], et[i], ps[i]});

        // 按“结束时间”升序排序（加权区间调度的标准套路）
        // 这样从前往后做 DP 时，所有可能与当前区间兼容的前驱区间
        // 一定都在其左侧，便于二分快速查找
        Collections.sort(list, (a, b) -> a[1] - b[1]);

        // f[i] 定义：考虑前 i 个“按结束时间排序后”的工作，能够取得的最大利润
        // 注意这里 f 采用 1-based：第 i 个工作指 list.get(i-1)
        // 预留 +10 只是图省事，防止边界场景下越界（+1 即可，这里沿用原写法）
        int[] f = new int[n + 10];

        // 主循环：i 从 1..n
        for (int i = 1; i <= n; i++) {
            // 取到第 i 个工作（对应 list 的 i-1 下标）
            int[] info = list.get(i - 1);
            int a = info[0]; // start
            int b = info[1]; // end
            int c = info[2]; // profit

            // 1) 不选第 i 个工作：继承前 i-1 个工作的最优值
            f[i] = Math.max(f[i - 1], c); // 这里先用 c 跟 f[i-1] 比，后面还会再尝试拼接前驱

            // 2) 二分查找“与当前工作兼容的、结束时间 <= 当前开始时间 a 的、最靠右的工作下标”
            //    我们在 [0, i-1) 的 list 区间上做二分（list 里是 0-based）
            int l = 0, r = i - 1;
            while (l < r) {
                // 取“上中位数”（向上取中）：mid = floor((l+r+1)/2)
                // 这么做是为了避免死循环，并且在判定条件为 true 时向右收缩，寻找最右满足的位置
                int mid = (l + r + 1) >> 1;

                // 如果第 mid 个工作的结束时间 <= 当前的开始时间 a
                // 说明 mid 这个位置是“可兼容”的，下界可以右移（尝试找更靠右的可兼容）
                if (list.get(mid)[1] <= a) l = mid;
                else r = mid - 1; // 否则说明 mid 不可兼容，右侧也不行，缩到左侧
            }

            // 二分结束后，r == l，可能有两种情况：
            // 情况A：list.get(r)[1] <= a，说明 r 是最右可兼容的下标
            // 情况B：list.get(r)[1] >  a，说明没有任何可兼容的（包括 r=0 也不满足）
            if (r >= 0 && list.get(r)[1] <= a) {
                // 这里为什么用 f[r+1] ?
                // f 是 1-based 的 DP 数组：f[k] 表示“前 k 个工作”的最优值
                // 而 list 是 0-based：下标 r 表示“第 r+1 个工作”
                // 因此“考虑到 r 这个工作为止”的最优值就是 f[r+1]
                // 组合方案：f[r+1]（不超过 a 的最优） + 当前工作利润 c
                f[i] = Math.max(f[i], f[r + 1] + c);
            }
            // 若不满足 <= a，则没有能在它之前拼接的工作，此时“选当前工作”的收益就是 c
            // 但上面的 f[i] 初始已经做过 Math.max(f[i-1], c) 了，故无需额外处理
        }
        return f[n];
    }
}

```

[面试题 16.16. 部分排序](https://leetcode.cn/problems/sub-sort-lcci/)

从前往后找更新最大值如果不是最大值说明当前值是乱序的，前面有比它更大的，end更新到这里

然后从后往前找最小值如果不是最小值说明当前值是乱序的，后面有比它更小的，start更新到这里

```java
public int[] subSort(int[] array) {
        int N = array.length, start = -1, end = -1;
        int min = Integer.MAX_VALUE, max = Integer.MIN_VALUE;
        
        // 从前往后找目标末位，使得从该位到最后，数组保持递增
        for (int i = 0; i < N; i++) {
            if (array[i] >= max) max = array[i];
            else end = i;
        }
        
        // 数组恒递增，说明数组是有序的，直接返回
        if (end == -1) return new int[] {-1, -1};
        
        // 从后往前找目标首位，使得从该位到最前，数组保持递减
        for (int i = end; i >= 0; i--) {
            if (array[i] <= min) min = array[i];
            else start = i;
        }
        return new int[] {start, end};
    }
```

[如何判断一个点是否在三角形内？](https://mp.weixin.qq.com/s/UK7ilkFeEDQeVWvLDHbuKA)

两个方法，海伦公式，如果三角形面积和三个小三角形面积相等说明在里面，如果小于的话说明在外面

![1760416168082](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760416168082.png)

```C++
struct Point {
    double x;
    double y;
};

double getDist(Point p1,Point p2) {
    //两点之间计算距离公式
    return sqrt(pow(p1.x-p2.x,2) + pow(p1.y-p2.y,2));
}
double getArea(Point p1,Point p2,Point p3) {
    double a = getDist(p1, p2);
    double b = getDist(p2, p3);
    double c = getDist(p1, p3);
    double p = (a + b + c) / 2;
    return sqrt(p * (p - a) * (p - b) * (p - c));
}
bool isInTriangle(Point p1,Point p2,Point p3,Point o) {
    double s1 = getArea(p1,p2,o);
    double s2 = getArea(p2,p3,o);
    double s3 = getArea(p3,p1,o);
    double s = getArea(p1,p2,p3);
    return s1+s2+s3 == s; //此处没有用fabs(a-b)<eps比较，是方便大家理解思路
}
```

![1760416267477](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760416267477.png)

![1760416287008](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760416287008.png)

```java
struct Point {
    double x;
    double y;
};
double product(Point p1,Point p2,Point p3) {
    //首先根据坐标计算p1p2和p1p3的向量，然后再计算叉乘
    //p1p2 向量表示为 (p2.x-p1.x,p2.y-p1.y)
    //p1p3 向量表示为 (p3.x-p1.x,p3.y-p1.y)
    return (p2.x-p1.x)*(p3.y-p1.y) - (p2.y-p1.y)*(p3.x-p1.x);
}
bool isInTriangle(Point p1,Point p2,Point p3,Point o) {
    //保证p1，p2，p3是逆时针顺序
    if(product(p1, p2, p3)<0) return isInTriangle(p1,p3,p2,o);
    if(product(p1, p2, o)>0 && product(p2, p3, o)>0 && product(p3, p1, o)>0)
        return true;
    return false;
}
```

[582. 杀掉进程 - 力扣（LeetCode）](https://leetcode.cn/problems/kill-process/description/)

![1760440210820](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760440210820.png)

![1760440228890](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760440228890.png)

```java
public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        // 1. 构建进程树（父进程 -> 子进程列表）
        Map<Integer, List<Integer>> tree = new HashMap<>();
        for (int i = 0; i < ppid.size(); i++) {
            int parent = ppid.get(i);
            int child = pid.get(i);
            tree.putIfAbsent(parent, new ArrayList<>());
            tree.get(parent).add(child);
        }
        
        // 2. BFS 遍历杀死进程及其所有子孙
        List<Integer> result = new ArrayList<>();
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(kill);
        
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            result.add(cur);
            // 如果 cur 有子进程，加入队列
            if (tree.containsKey(cur)) {
                queue.addAll(tree.get(cur));
            }
        }
        
        return result;
    }
```

[280. 摆动排序 - 力扣（LeetCode）](https://leetcode.cn/problems/wiggle-sort/description/)

```java
public void wiggleSort(int[] nums) {
    for (int i = 0; i < nums.length - 1; i++) {
        if (i % 2 == 0) {
            // 偶数索引：需要 nums[i] <= nums[i+1]
            if (nums[i] > nums[i + 1]) {
                swap(nums, i, i + 1);
            }
        } else {
            // 奇数索引：需要 nums[i] >= nums[i+1]
            if (nums[i] < nums[i + 1]) {
                swap(nums, i, i + 1);
            }
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

[324. 摆动排序 II](https://leetcode.cn/problems/wiggle-sort-ii/)

```java
public void wiggleSort(int[] nums) {
        int[] temp=nums.clone();
        Arrays.sort(temp);
        int idx=0,mid=(nums.length+1)/2,i=mid-1,j=nums.length-1;
        while(idx<nums.length)
        {
            nums[idx]=temp[i--];
            if(idx+1< nums.length)
                nums[idx+1]=temp[j--];
            idx+=2;
        }
    }
```

快速选择，三向切分，虚拟索引映射

```java
public void wiggleSort(int[] nums) {
    int n = nums.length;
    
    // 1. 找到中位数（使用快速选择）
    int median = findKthLargest(nums, (n + 1) / 2);
    
    // 2. 三向切分：<median, =median, >median
    int i = 0, left = 0, right = n - 1;
    while (i <= right) {
        if (nums[newIndex(i, n)] > median) {
            swap(nums, newIndex(left++, n), newIndex(i++, n));
        } else if (nums[newIndex(i, n)] < median) {
            swap(nums, newIndex(right--, n), newIndex(i, n));
        } else {
            i++;
        }
    }
}

// 虚拟索引映射函数（关键）
private int newIndex(int index, int n) {
    return (1 + 2 * index) % (n | 1);
}

// 快速选择找第k大的元素
private int findKthLargest(int[] nums, int k) {
    return quickSelect(nums, 0, nums.length - 1, nums.length - k);
}

private int quickSelect(int[] nums, int left, int right, int k) {
    if (left == right) return nums[left];
    
    int pivot = nums[left + (right - left) / 2];
    int i = left, j = right;
    
    while (i <= j) {
        while (i <= j && nums[i] < pivot) i++;
        while (i <= j && nums[j] > pivot) j--;
        if (i <= j) {
            swap(nums, i, j);
            i++;
            j--;
        }
    }
    
    if (k <= j) return quickSelect(nums, left, j, k);
    if (k >= i) return quickSelect(nums, i, right, k);
    return nums[k];
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

[369. 给单链表加一 - 力扣（LeetCode）](https://leetcode.cn/problems/plus-one-linked-list/description/)

![1760499634731](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1760499634731.png)

[856. 括号的分数](https://leetcode.cn/problems/score-of-parentheses/)

碰到左括号就加一个0，碰到右括号取最后两个数，最后一个数×2和1取最大，然后和倒数第二个相加再加到最后

```java
public int scoreOfParentheses(String s) {
        Deque<Integer> nums=new ArrayDeque<>();
        nums.add(0);
        for(char c:s.toCharArray())
        {
            if(c=='(')
                nums.add(0);
            else{
                int cur=nums.pollLast();
                nums.add(nums.pollLast()+Math.max(cur*2,1));
            }
        }
        return nums.getLast();
    }
```

[593. 有效的正方形](https://leetcode.cn/problems/valid-square/)

保证三个角都是等腰直角三角形就行，同时还要确保四个点不一样就是距离不为0

```java
public boolean validSquare(int[] p1, int[] p2, int[] p3, int[] p4) {
        return isok(p1,p2,p3)&&isok(p4,p2,p3)&&isok(p4,p2,p1);
    }

    boolean isok(int[] p1, int[] p2, int[] p3)
    {
        int dis2[]=new int[3];
        dis2[0]=(p1[0]-p2[0])*(p1[0]-p2[0])+(p1[1]-p2[1])*(p1[1]-p2[1]);
        dis2[1]=(p1[0]-p3[0])*(p1[0]-p3[0])+(p1[1]-p3[1])*(p1[1]-p3[1]);
        dis2[2]=(p3[0]-p2[0])*(p3[0]-p2[0])+(p3[1]-p2[1])*(p3[1]-p2[1]);
        Arrays.sort(dis2);
        return dis2[0]==dis2[1]&&dis2[0]+dis2[1]==dis2[2]&&dis2[0]!=0;
    }
```

[743. 网络延迟时间](https://leetcode.cn/problems/network-delay-time/)

使用优先队列和距离数组，当遇到旧的当前节点距离的时候要和距离数组比一下选择是否舍弃

```java
public int networkDelayTime(int[][] times, int n, int k) {
        int t[]=new int[n+1],res=0;
        Arrays.fill(t,Integer.MAX_VALUE);
        t[k]=0;
        ArrayList<ArrayList<int[]>> graph=new ArrayList<>();
        for(int i=0;i<=n;++i)
            graph.add(new ArrayList<>());
        for(int time[]:times)
            graph.get(time[0]).add(new int[]{time[1],time[2]});
        PriorityQueue<int[]> dq=new PriorityQueue<>(Comparator.comparingInt(a->a[1]));
        dq.add(new int[]{k,0});
        while(!dq.isEmpty())
        {
            int nownode[]=dq.poll();
            if(t[nownode[0]]<nownode[1]) continue;//这里要比较一下是否舍弃
            res=Math.max(res,nownode[1]);
            for(int []temp:graph.get(nownode[0]))
            {
                if(t[nownode[0]]+temp[1]<t[temp[0]])
                {
                    t[temp[0]]=t[nownode[0]]+temp[1];
                    dq.add(new int[]{temp[0],t[temp[0]]});
                }
            }
        }
        for(int i=1;i<=n;++i)
        {
            if(t[i]==Integer.MAX_VALUE)
                return -1;
        }
        return res;
    }
```

[1277. 统计全为 1 的正方形子矩阵](https://leetcode.cn/problems/count-square-submatrices-with-all-ones/)

动态规划，求最大正方形然后所有相加，递推公式是左上三边取最小值

```java
public int countSquares(int[][] matrix) {
        int dp[][]=new int[matrix.length+1][matrix[0].length+1];
        int res=0;
        for(int i=1;i<= matrix.length;++i)
            for(int j=1;j<=matrix[0].length;++j)
                if(matrix[i-1][j-1]==1)
                {
                    int next=Math.min(dp[i-1][j-1],Math.min(dp[i-1][j],dp[i][j-1]))+1;
                    dp[i][j]=next;
                    res+=next;
                }
        return res;
    }
```

[LCR 163. 找到第 k 位数字](https://leetcode.cn/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

找到每一位数消耗的个数，然后再减去，注意在求的时候最后k要减一因为是从一开始的，还有一点就是要用long不然越界

```java
public int findKthNumber(int k) {
        int digitLength = 1;      // 当前数字的位数（1,2,3...）
        long count = 9;           // 当前位数的数字总数（9,90,900...）
        long start = 1;           // 当前位数的最小数字（1,10,100...）
        // 1. 确定数字位数
        while (k > digitLength * count) {
            k -= digitLength * count;
            digitLength++;
            count *= 10;
            start *= 10;
        }
        // 2. 定位具体数字
        long targetNum = start + (k - 1) / digitLength;
        // 3. 提取对应位
        int digitIndex = (k - 1) % digitLength;
        return String.valueOf(targetNum).charAt(digitIndex) - '0';
    }
```

[318. 最大单词长度乘积](https://leetcode.cn/problems/maximum-product-of-word-lengths/)

位掩码技术，只有小写字母所以可以用26位来表示一个单词，每个单词都有自己的掩码，如果两个掩码相与得到了0就说明没有字母相同

```java
public int maxProduct(String[] words) {
        int[] marks = new int[words.length];
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            int val = 0;
            for (int j = 0; j < word.length(); j++) {
                val |= 1 << (word.charAt(j) - 'a');//位掩码
            }
            marks[i] = val;
        }
        int maxValue = 0;
        for (int i = 0; i < words.length; i++) {
            for (int j = i + 1; j < words.length; j++) {
                if ((marks[i] & marks[j]) == 0) {
                    maxValue = Math.max(maxValue, words[i].length() * words[j].length());
                }
            }
        }
        return maxValue;
    }
```

[1106. 解析布尔表达式](https://leetcode.cn/problems/parsing-a-boolean-expression/)

```java
public boolean parseBoolExpr(String s) {
            // 使用双栈：nums栈存储操作数和中间结果，ops栈存储运算符
            Deque<Character> nums = new ArrayDeque<>(); // 操作数栈：存储't','f'和分隔符'-'
            Deque<Character> ops = new ArrayDeque<>();  // 运算符栈：存储'|','&','!'
            // 遍历表达式中的每个字符
            for (char c : s.toCharArray()) {
                if (c == ',') {
                    // 忽略逗号，它只用于分隔参数，不影响计算
                    continue;
                }
                if (c == 't' || c == 'f') {
                    // 遇到布尔值，直接压入操作数栈
                    nums.addLast(c);
                }
                if (c == '|' || c == '&' || c == '!') {
                    // 遇到运算符，压入运算符栈
                    ops.addLast(c);
                }
                if (c == '(') {
                    // 遇到左括号，压入特殊标记'-'，用于标识子表达式的开始
                    nums.addLast('-');
                }
                if (c == ')') {
                    // 遇到右括号，计算当前子表达式的结果
                    // 1. 获取当前子表达式对应的运算符
                    char op = ops.pollLast();
                    char cur = ' '; // 初始化当前计算结果
                    // 2. 弹出操作数直到遇到起始标记'-'，并计算子表达式
                    while (!nums.isEmpty() && nums.peekLast() != '-') {
                        char top = nums.pollLast(); // 弹出栈顶操作数
                        if (cur == ' ') {
                            // 第一个操作数，直接赋值
                            cur = top;
                        } else {
                            // 后续操作数，与当前结果进行运算
                            cur = calc(top, cur, op);
                        }
                    }
                    // 3. 处理逻辑非运算符'!'
                    if (op == '!') {
                        cur = (cur == 't') ? 'f' : 't';
                    }
                    // 4. 弹出起始标记'-'，并将计算结果压回栈中
                    nums.pollLast(); // 弹出'-'
                    nums.addLast(cur); // 压入计算结果
                }
            }
            // 最终栈中应只剩一个元素，即最终结果
            return nums.peekLast() == 't';
        }
        private char calc(char a, char b, char op) {
            // 将字符转换为布尔值
            boolean x = (a == 't');
            boolean y = (b == 't');
            boolean ans;
            if (op == '|') {
                // 逻辑或运算
                ans = x || y;
            } else {
                // 逻辑与运算 (op == '&')
                ans = x && y;
            }
            // 将布尔值转换回字符
            return ans ? 't' : 'f';
        }
```

[LCR 168. 丑数](https://leetcode.cn/problems/chou-shu-lcof/)

动态规划，用一个dp数组存当前的丑数，然后递推公式是指针++而不是直接跳到x

```java
public int nthUglyNumber(int n) {
        int nows[]=new int[3];
        int dp[]=new int[n];
        dp[0]=1;
        for(int x=1;x<n;++x)
        {
            int n0=dp[nows[0]]*2,n1=dp[nows[1]]*3,n2=dp[nows[2]]*5;
            dp[x]=Math.min(n1,Math.min(n2,n0));
            if(dp[x]==n0) nows[0]++;
            if(dp[x]==n1) nows[1]++;
            if(dp[x]==n2) nows[2]++;
        }
        return dp[n-1];
    }
```

[653. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/two-sum-iv-input-is-a-bst/)

可以用一个列表存起来有序数组然后两边双指针移动找到目标值

[813. 最大平均值和的分组](https://leetcode.cn/problems/largest-sum-of-averages/)

```java
public double largestSumOfAverages(int[] nums, int k) {
    // 获取数组的长度，n为元素个数
    int n = nums.length;
    
    // 定义前缀和数组，用于快速计算任意子数组的和
    // presum[i] 表示 nums[0] 到 nums[i-1] 的元素之和（前i个元素的和）
    double[] presum = new double[n + 1];
    for(int i = 0; i < n; i++){
        // 前缀和递推公式：前i+1个元素的和 = 前i个元素的和 + 第i个元素（nums[i]）
        presum[i + 1] = presum[i] + nums[i];
    }
    
    // 定义动态规划数组 dp[i][j]
    // 含义：将前i个元素（即nums[0]到nums[i-1]）分成j个非空连续子数组时，最大的平均值之和
    double[][] dp = new double[n + 1][k + 1];
    
    // 初始化：当只分成1个子数组时（j=1）
    // 此时前i个元素的平均值之和就是整个子数组的平均值（因为只有一个子数组）
    for(int i = 1; i <= n; i++){
        // 前i个元素的和为presum[i]，平均值 = 和 / 元素个数i
        dp[i][1] = presum[i] / i;
    }
    
    // 填充dp数组：处理分成j个子数组的情况（j从2到k）
    for(int j = 2; j <= k; j++){
        // 处理前i个元素（i从1到n，需保证能分成j个子数组，即i >= j）
        for(int i = 1; i <= n; i++){
            // 寻找最优划分点x：将前i个元素分成j个子数组时，最后一个子数组是从x到i-1（共i-x个元素）
            // 前x个元素需分成j-1个子数组（因此x至少为j-1，否则无法分成j-1个非空数组）
            // x的范围：[j-1, i-1]（因为最后一个子数组至少1个元素，即i-x >= 1 → x <= i-1）
            for(int x = j - 1; x < i; x++){
                // 状态转移公式：
                // dp[i][j] = 前x个元素分成j-1个子数组的最大和（dp[x][j-1]） + 最后一个子数组（x到i-1）的平均值
                // 最后一个子数组的和为presum[i] - presum[x]，元素个数为i - x，因此平均值为两者之比
                dp[i][j] = Math.max(dp[i][j], dp[x][j - 1] + (presum[i] - presum[x]) / (i - x));
            }
        }
    }
    
    // 最终结果：将整个数组（n个元素）分成k个子数组的最大平均值之和
    return dp[n][k];
}
```

[65. 有效数字](https://leetcode.cn/problems/valid-number/)

写出来的是神仙，我哭了

```java
public boolean isNumber(String s) {
        s = s.trim(); // 去掉前后空格
        int len = s.length();
        boolean[][] dp = new boolean[len + 1][7];
        dp[0][0] = true; // 初始状态有效
        for (int i = 1; i <= len; i++) {
            char c = s.charAt(i - 1);
            for (int j = 0; j < 7; j++) {
                if (!dp[i - 1][j]) continue; // 当前状态无效，跳过
                switch (j) {
                    case 0: // 初始状态
                        if (c == '+' || c == '-') dp[i][1] = true;
                        else if (Character.isDigit(c)) dp[i][1] = true;
                        else if (c == '.') dp[i][2] = true;
                        break;
                    case 1: // 整数部分
                        if (Character.isDigit(c)) dp[i][1] = true;
                        else if (c == '.') dp[i][3] = true;
                        else if (c == 'e' || c == 'E') dp[i][4] = true;
                        break;
                    case 2: // 小数点但无数字
                        if (Character.isDigit(c)) dp[i][3] = true;
                        break;
                    case 3: // 小数部分
                        if (Character.isDigit(c)) dp[i][3] = true;
                        else if (c == 'e' || c == 'E') dp[i][4] = true;
                        break;
                    case 4: // 指数符号
                        if (c == '+' || c == '-') dp[i][4] = true;
                        else if (Character.isDigit(c)) dp[i][5] = true;
                        break;
                    case 5: // 指数部分
                        if (Character.isDigit(c)) dp[i][5] = true;
                        break;
                    case 6: // 尾部空格
                        if (c == ' ') dp[i][6] = true;
                        break;
                }
            }
        }
        // 检查接收状态
        return dp[len][1] || dp[len][3] || dp[len][5] || dp[len][6];
    }
```

[LCP 09. 最小跳跃次数](https://leetcode.cn/problems/zui-xiao-tiao-yue-ci-shu/)

逆序遍历会获得很多方便

```java
class Solution {
    /**
     * 计算从数组起始位置0出发，跳出数组所需的最小跳跃次数
     * 跳跃规则：
     * 1. 从位置i可以向右跳jump[i]步，到达i + jump[i]
     * 2. 从位置i可以向左跳至任意小于i的位置（即0 <= j < i）
     * 目标：找到最少跳跃次数，使位置超出数组范围（>= 数组长度）
     * 
     * @param jump 跳跃步数数组，jump[i]表示位置i的向右跳跃步数
     * @return 最小跳跃次数
     */
    public int minJump(int[] jump) {
        // dp[i] 表示从位置i出发，跳出数组所需的最小跳跃次数
        int[] dp = new int[jump.length];
        int n = jump.length;
        // 初始化：最后一个位置（n-1）的最小跳跃次数
        // 从最后一个位置跳一次，无论是否直接跳出，至少需要1步（若跳出则刚好1步，若未跳出则后续会更新）
        dp[n - 1] = 1;
        // 倒序遍历：从倒数第二个位置开始，直到第0个位置
        // 原因：计算dp[i]时需要用到i右侧位置的dp值（如i+jump[i]的位置），倒序遍历可保证右侧位置已计算完毕
        for (int i = n - 2; i > -1; --i) {
            // 第一步：计算从i向右跳的最小步数
            // 若向右跳jump[i]步后直接超出数组范围（i + jump[i] >= n），则从i出发只需1步
            // 否则，需要先跳到i + jump[i]，再加上从该位置出发的步数（dp[i + jump[i]]），总步数为 dp[i + jump[i]] + 1
            dp[i] = (i + jump[i] >= n) ? 1 : (dp[i + jump[i]] + 1);
            
            // 第二步：更新i右侧位置的dp值（优化过程）
            // 逻辑：从i右侧的位置j（j > i）出发时，可以先向左跳一步到i，再从i出发（步数为dp[i]），总步数为 dp[i] + 1
            // 若这个总步数比j原本的dp[j]更小，则更新dp[j]
            // 循环终止条件：当遇到某个j的dp[j] < dp[i] + 1时，右侧的j'（j' > j）的dp值已被j优化过（更小），无需继续遍历
            for (int j = i + 1; j < n && dp[j] >= dp[i] + 1; ++j) {
                dp[j] = dp[i] + 1;
            }
        }
        // 最终结果：从位置0出发的最小跳跃次数
        return dp[0];
    }
}
```

[773. 滑动谜题](https://leetcode.cn/problems/sliding-puzzle/)

序列化答案，可以快速比较，然后使用set去重避免重复加入，然后使用BFS可以快速求最小步数

```java
public int slidingPuzzle(int[][] board) {
        Queue<String> q = new LinkedList<>();
        HashSet<String> used = new HashSet<>();
        StringBuffer sb = new StringBuffer();
        for(int i=0;i<2;i++){
            for(int j=0;j<3;j++){
                sb.append(board[i][j]);
            }
        }
        String start = sb.toString();
        int step=0;
        String target = "123450";
        q.offer(start);
        used.add(start);

        int[][] neighbor = new int[][]{{1, 3}, {0, 2, 4}, {1, 5},
                {0, 4}, {1, 3, 5}, {2, 4} };
        while(!q.isEmpty()){
            int sz = q.size();
            for(int i=0;i<sz;i++){
                String cur = q.poll();
                if(cur.equals(target)){
                    return step;
                }
                int idx=0;
                for(;cur.charAt(idx)!='0';idx++);
                for(int adj: neighbor[idx]){
                    String next = getNext(cur.toCharArray(), idx, adj);
                    if(!used.contains(next)){
                        q.offer(next);
                        used.add(next);
                    }
                }
            }
            step++;
        }
        return -1;
    }

    String getNext(char[] s, int i, int j){
        char t = s[i];
        s[i] = s[j];
        s[j] = t;
        return new String(s);
    }
```

[583. 两个字符串的删除操作](https://leetcode.cn/problems/delete-operation-for-two-strings/)

```java
public int minDistance(String word1, String word2) {
        int m=word1.length(),n=word2.length();
        int dp[][]=new int[m+1][n+1];
        for(int i=1;i<=m;++i)
            dp[i][0]=i;
        for(int i=1;i<=n;++i)
            dp[0][i]=i;//注意初始化逻辑是等于i的，不是等于一，这里容易错
        for(int i=1;i<=m;++i)
            for(int j=1;j<=n;++j)
                if(word1.charAt(i-1)==word2.charAt(j-1))
                    dp[i][j]=dp[i-1][j-1];
                else dp[i][j]=Math.min(dp[i-1][j]+1,Math.min(dp[i][j-1]+1,dp[i-1][j-1]+2));
        return dp[m][n];
    }
```

[792. 匹配子序列的单词数](https://leetcode.cn/problems/number-of-matching-subsequences/)

把所有的字符存进map中的动态数组然后使用二分法找到比当前下标只大一点点的下一个下标索引，更新当前下标

```java
public int numMatchingSubseq(String s, String[] words) {
    int n = s.length(); // 源字符串s的长度
    int ans = 0; // 记录符合条件的单词数量
    // 构建字符到索引列表的映射：key为s中的字符，value为该字符在s中出现的所有索引位置（按出现顺序存储）
    // 作用：快速查找某个字符在s中的所有位置，为后续子序列检查提供基础
    Map<Character, List<Integer>> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c = s.charAt(i); // 当前字符
        // 若map中已有该字符的列表，则获取；否则新建一个空列表
        List<Integer> list = map.getOrDefault(c, new ArrayList<>());
        list.add(i); // 将当前索引加入列表（记录字符c在s中出现的位置）
        map.put(c, list); // 更新map
    }
    // 遍历每个单词，检查是否是s的子序列
    for (String w : words) {
        boolean ok = true; // 标记当前单词是否为子序列
        int m = w.length(); // 当前单词的长度
        int idx = -1; // 记录上一个字符在s中匹配到的位置（初始为-1，确保第一个字符能匹配到s中>=0的位置）
        // 遍历当前单词的每个字符，检查能否在s中按顺序找到
        for (int i = 0; i < m && ok; i++) {
            char c = w.charAt(i); // 当前字符
            // 获取该字符在s中所有出现位置的列表（若s中没有该字符，返回空列表）
            List<Integer> list = map.getOrDefault(c, new ArrayList<>());
            // 二分查找：在list中找到第一个大于idx的位置（确保字符顺序符合子序列要求）
            int l = 0; // 左边界
            int r = list.size() - 1; // 右边界
            while (l < r) {
                int mid = l + r >> 1; // 等价于(l + r)/2，避免溢出
                // 若中间位置的索引大于idx，说明可能在左半部分找到更小的符合条件的索引，收缩右边界
                if (list.get(mid) > idx) {
                    r = mid;
                } else {
                    // 否则需要在右半部分查找，收缩左边界
                    l = mid + 1;
                }
            }
            // 检查是否找到有效位置：
            // 若列表为空（r < 0），或找到的位置仍 <= idx（无符合条件的位置），则当前单词不是子序列
            if (r < 0 || list.get(r) <= idx) {
                ok = false;
            } else {
                // 找到有效位置，更新idx为当前位置（作为下一个字符的查找起点）
                idx = list.get(r);
            }
        }
        // 若当前单词是子序列，计数+1
        if (ok) {
            ans++;
        }
    }
    return ans;
}
```

[474. 一和零](https://leetcode.cn/problems/ones-and-zeroes/)

背包问题，每个东西只能遍历一次，所以需要反向遍历

```java
public int findMaxForm(String[] strs, int m, int n) {
        int dp[][]=new int[m+1][n+1];
        for(String s:strs)
        {
            int zero=0,one=0;
            for(char c:s.toCharArray())
            {
                if(c=='0')zero++;
                if(c=='1')one++;
            }
            for(int i=m;i>=zero;--i)
                for(int j=n;j>=one;--j)
                    dp[i][j]=Math.max(dp[i][j],dp[i-zero][j-one]+1);
        }
        return dp[m][n];
    }
```

[391. 完美矩形](https://leetcode.cn/problems/perfect-rectangle/)

```java
class Solution {
    // 定义常量：INF用于初始化最大/最小值（避免溢出），BASE用于将二维坐标转为唯一整数键
    // BASE选择20003（大于可能的坐标范围，确保x*BASE+y不会冲突）
    final static int INF = Integer.MAX_VALUE / 2, BASE = 20003;

    public boolean isRectangleCover(int[][] rectangles) {
        // 精确覆盖的两个核心条件：
        // 1. 所有小矩形的面积之和 == 由最外层边界构成的大矩形面积
        // 2. 大矩形的四个顶点仅出现1次，其余所有顶点出现次数为偶数（0、2、4...）

        // 用哈希表记录每个顶点的出现次数（key为顶点的唯一标识，value为次数）
        // 预设容量为矩形数量*4（每个矩形4个顶点），减少哈希表扩容开销
        Map<Integer, Integer> map = new HashMap<>(rectangles.length * 4);
        
        // 初始化最大/最小坐标（用于确定最外层大矩形的边界）
        int minX = INF, minY = INF;  // 最左、最下边界
        int maxX = -INF, maxY = -INF; // 最右、最上边界
        long totalArea = 0;  // 所有小矩形的面积之和

        // 遍历每个矩形，更新边界、面积和顶点计数
        for (int[] rect : rectangles) {
            // 解析矩形的左下角(x1,y1)和右上角(x2,y2)
            int x1 = rect[0], y1 = rect[1];
            int x2 = rect[2], y2 = rect[3];

            // 更新最外层边界（大矩形的边界）
            minX = Math.min(minX, x1);  // 取所有矩形的最左x
            minY = Math.min(minY, y1);  // 取所有矩形的最下y
            maxX = Math.max(maxX, x2);  // 取所有矩形的最右x
            maxY = Math.max(maxY, y2);  // 取所有矩形的最上y

            // 将当前矩形的4个顶点转为唯一键（x*BASE + y 避免坐标冲突）
            // 并更新哈希表中该顶点的出现次数（+1）
            map.merge(x1 * BASE + y1, 1, Integer::sum);  // 左下角
            map.merge(x1 * BASE + y2, 1, Integer::sum);  // 左上角
            map.merge(x2 * BASE + y1, 1, Integer::sum);  // 右下角
            map.merge(x2 * BASE + y2, 1, Integer::sum);  // 右上角

            // 累加当前矩形的面积（转为long避免int溢出）
            totalArea += (long) (x2 - x1) * (y2 - y1);
        }

        // 条件1：判断总面积是否等于大矩形的面积
        // 计算大矩形面积：(宽) * (高)
        long bigRectangleArea = (long) (maxX - minX) * (maxY - minY);
        if (bigRectangleArea != totalArea) {
            return false;  // 面积不相等，必然不是精确覆盖
        }

        // 条件2：检查大矩形的4个顶点是否仅出现1次
        // 大矩形的四个顶点为：(minX,minY)、(minX,maxY)、(maxX,minY)、(maxX,maxY)
        for (int x : new int[]{minX, maxX}) {
            for (int y : new int[]{minY, maxY}) {
                // 计算顶点的唯一键
                int key = x * BASE + y;
                // 若该顶点出现次数不是1，不符合条件
                if (map.getOrDefault(key, 0) != 1) {
                    return false;
                }
                // 将已检查的顶点次数置0（避免后续重复检查）
                map.put(key, 0);
            }
        }

        // 条件2补充：检查其余所有顶点的出现次数是否为偶数
        for (int count : map.values()) {
            if (count % 2 != 0) {
                return false;  // 存在奇数次出现的顶点，不符合条件
            }
        }

        // 所有条件均满足，是精确覆盖
        return true;
    }
}
```

[1129. 颜色交替的最短路径](https://leetcode.cn/problems/shortest-path-with-alternating-colors/)

两个邻接表然后从红变成蓝相互交替使用BFS

```java
public int[] shortestAlternatingPaths(int n, int[][] redEdges, int[][] blueEdges) {
        // 标明颜色，这是很好的习惯哦。
        final int RED = 0;
        final int BLUE = 1;

        // 构建双层邻接表
        List<Integer>[][] adj = new ArrayList[2][n];
        for (int i = 0; i < n; i++) {
            adj[RED][i] = new ArrayList<>();
            adj[BLUE][i] = new ArrayList<>();
        }
        for (int[] edge : redEdges) {
            adj[RED][edge[0]].add(edge[1]);
        }
        for (int[] edge : blueEdges) {
            adj[BLUE][edge[0]].add(edge[1]);
        }

        // 初始队列中同时含有蓝色源点和红色源点，并且我们也将相应颜色存入队列。
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[] {RED, 0});
        q.offer(new int[] {BLUE, 0});

        // 双层数组存储距离。
        int[][] dists = new int[2][n];
        Arrays.fill(dists[RED], Integer.MAX_VALUE);
        Arrays.fill(dists[BLUE], Integer.MAX_VALUE);
        dists[RED][0] = 0;
        dists[BLUE][0] = 0;

        while (!q.isEmpty()) {
            int[] current = q.poll();
            int uColor = current[0], u = current[1];
            int vColor = uColor ^ 1; // 异或切换 1 和 0，等同于 1 - uColor，得到下条边的颜色

            for (int v : adj[vColor][u]) {
                if (dists[vColor][v] != Integer.MAX_VALUE) continue;
                dists[vColor][v] = dists[uColor][u] + 1;
                q.offer(new int[] {vColor, v});
            }
        }

        // 将双层数组中的距离合并取小，无穷大改成 -1。
        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            result[i] = Math.min(dists[RED][i], dists[BLUE][i]);
            if (result[i] == Integer.MAX_VALUE) result[i] = -1;
        }
        return result;
    }
```

迷宫三剑客

迷宫一只用返回false和true，迷宫二返回最小距离，那么使用迪杰斯特拉堆优化版本就都可以实现

[788 · 迷宫II - LintCode](https://www.lintcode.com/problem/788/)

```java
import java.util.*;

public class Maze2 {
    public int shortestDistance(int[][] maze, int[] start, int[] destination) {
        int rows = maze.length;
        int cols = maze[0].length;
        int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};  // 方向数组
        
        // 距离矩阵：记录到达每个位置的最短距离
        int[][] dist = new int[rows][cols];
        for (int[] row : dist) {
            Arrays.fill(row, Integer.MAX_VALUE);
        }
        dist[start[0]][start[1]] = 0;
        
        // 优先队列：(距离, x, y)
        PriorityQueue<int[]> heap = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        heap.add(new int[]{0, start[0], start[1]});
        
        while (!heap.isEmpty()) {
            int[] curr = heap.poll();
            int currDist = curr[0];
            int x = curr[1];
            int y = curr[2];
            
            // 到达终点，返回当前距离（迪杰斯特拉保证是最短距离）
            if (x == destination[0] && y == destination[1]) {
                return currDist;//返回距离或者返回true就是第一题的解法
            }
            
            // 跳过非最优路径
            if (currDist > dist[x][y]) {
                continue;
            }
            
            // 尝试四个方向滚动
            for (int[] dir : dirs) {
                int dx = dir[0];
                int dy = dir[1];
                int nx = x;
                int ny = y;
                int step = 0;
                
                while (nx + dx >= 0 && nx + dx < rows && ny + dy >= 0 && ny + dy < cols 
                       && maze[nx + dx][ny + dy] == 0) {
                    nx += dx;
                    ny += dy;
                    step++;
                }
                
                int newDist = currDist + step;
                // 新路径更优则更新
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    heap.add(new int[]{newDist, nx, ny});
                }
            }
        }
        
        // 无法到达终点
        return -1;
    }
}
```

迷宫三处理的东西更加复杂

```java
public class Maze3 {
    public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
        int rows = maze.length;
        int cols = maze[0].length;
        // 方向数组：(dx, dy, 方向字符)，按字典序排序（u < d < l < r）
        int[][] dirs = {{-1, 0, 'u'}, {1, 0, 'd'}, {0, -1, 'l'}, {0, 1, 'r'}};
        
        // 距离矩阵：记录到达每个位置的最短距离
        int[][] dist = new int[rows][cols];
        // 路径矩阵：记录到达每个位置的最优路径
        String[][] path = new String[rows][cols];
        for (int i = 0; i < rows; i++) {
            Arrays.fill(dist[i], Integer.MAX_VALUE);
            Arrays.fill(path[i], "");
        }
        dist[ball[0]][ball[1]] = 0;
        
        // 优先队列：(距离, x, y, 路径)，排序规则：先按距离，再按路径字典序
        PriorityQueue<State> heap = new PriorityQueue<>((a, b) -> {
            if (a.dist != b.dist) {
                return a.dist - b.dist;
            } else {
                return a.path.compareTo(b.path);
            }
        });
        heap.add(new State(0, ball[0], ball[1], ""));
        
        while (!heap.isEmpty()) {
            State curr = heap.poll();
            int currDist = curr.dist;
            int x = curr.x;
            int y = curr.y;
            String currPath = curr.path;
            
            // 到达洞口，返回路径
            if (x == hole[0] && y == hole[1]) {
                return currPath;
            }
            
            // 跳过非最优路径（距离更大，或距离相同但路径字典序更大）
            if (currDist > dist[x][y]) {
                continue;
            }
            if (currDist == dist[x][y] && currPath.compareTo(path[x][y]) > 0) {
                continue;
            }
            
            // 尝试四个方向滚动
            for (int[] dir : dirs) {
                int dx = dir[0];
                int dy = dir[1];
                char c = (char) dir[2];
                int nx = x;
                int ny = y;
                int step = 0;
                String newPath = currPath + c;  // 拼接方向字符
                
                // 滚动过程中若经过洞口，直接停止
                while (true) {
                    // 检查是否到达洞口
                    if (nx == hole[0] && ny == hole[1]) {
                        break;
                    }
                    // 检查是否撞墙或出界
                    if (nx + dx < 0 || nx + dx >= rows || ny + dy < 0 || ny + dy >= cols 
                        || maze[nx + dx][ny + dy] == 1) {
                        break;
                    }
                    // 继续滚动
                    nx += dx;
                    ny += dy;
                    step++;
                }
                
                int newDist = currDist + step;
                // 判断是否需要更新
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    path[nx][ny] = newPath;
                    heap.add(new State(newDist, nx, ny, newPath));
                } else if (newDist == dist[nx][ny]) {
                    // 距离相同，选字典序更小的路径
                    if (newPath.compareTo(path[nx][ny]) < 0) {
                        path[nx][ny] = newPath;
                        heap.add(new State(newDist, nx, ny, newPath));
                    }
                }
            }
        }
        
        // 无法到达洞口
        return "impossible";
    }
    
    // 辅助类：存储状态（距离、坐标、路径）
    class State {
        int dist;
        int x;
        int y;
        String path;
        
        State(int dist, int x, int y, String path) {
            this.dist = dist;
            this.x = x;
            this.y = y;
            this.path = path;
        }
    }
}
```

[341. 扁平化嵌套列表迭代器](https://leetcode.cn/problems/flatten-nested-list-iterator/)

DFS拆开每一个列表然后加入队列中，再遍历移动指针就好

```java
class NestedIterator implements Iterator<Integer> {

    Deque<Integer> queue = new ArrayDeque<>();

    public NestedIterator(List<NestedInteger> nestedList) {
        dfs(nestedList);
    }

    @Override
    public Integer next() {
        return hasNext() ? queue.pollFirst() : -1;
    }

    @Override
    public boolean hasNext() {
        return !queue.isEmpty();
    }

    void dfs(List<NestedInteger> list) {
        for (NestedInteger item : list) {
            if (item.isInteger()) {
                queue.addLast(item.getInteger());
            } else {
                dfs(item.getList());
            }
        }
    }
}

```

[1048. 最长字符串链](https://leetcode.cn/problems/longest-string-chain/)

hm相当于dp数组，每一次把字符的第i个字符跳过组成新的看看之前有没有

```java
public int longestStrChain(String[] words) {
        HashMap<String,Integer> hm=new HashMap<>();
        Arrays.sort(words, Comparator.comparingInt(String::length));
        int res=0;
        for(String word:words)
        {
            int best=0;
            for(int i=0;i<word.length();++i)
            {
                String temp=word.substring(0,i)+word.substring(i+1);
                best=Math.max(best,hm.getOrDefault(temp,0)+1);
            }
            hm.put(word,best);
            res=Math.max(res,best);
        }
        return res;
    }
```

[1358. 包含所有三种字符的子字符串数目](https://leetcode.cn/problems/number-of-substrings-containing-all-three-characters/)

滑动窗口，维护字符数等于三的窗口，等于三直接相加，然后缩短左边界

```java
public int numberOfSubstrings(String s) {
        int cnt[]=new int[3],nownum=0;
        int left=0,res=0;
        for(int right=0;right<s.length();++right)
        {
            if(cnt[s.charAt(right)-'a']++==0)
                ++nownum;
            while(nownum==3){
                res+=s.length()-right;//加上右边剩下的其他字串
                if(--cnt[s.charAt(left++)-'a']==0)
                    --nownum;
            }
            //或者res+=left 放在这，和上面相加是等价的
        }
        return res;
    }
```

[910. 最小差值 II](https://leetcode.cn/problems/smallest-range-ii/)

让最大值尽量小一点，最小值尽量大一点

```java
public int smallestRangeII(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length;
        int ans = nums[n - 1] - nums[0];
        for (int i = 1; i < n; i++) {
            int mx = Math.max(nums[i - 1] + k, nums[n - 1] - k);//最大值在当前加K和最大-k中
            int mn = Math.min(nums[0] + k, nums[i] - k);//最小值在最小加k和当前-k中
            ans = Math.min(ans, mx - mn);
        }
        return ans;
    }
```

[1668. 最大重复子字符串](https://leetcode.cn/problems/maximum-repeating-substring/)

不断增加字符串看看原字符串有没有这个子串

```java
public int maxRepeating(String sequence, String word) {
        if (word == null || word.length() == 0) return 0;
        int k = 0;
        StringBuilder repeated = new StringBuilder(word);
        while (sequence.contains(repeated.toString())) {
            k++;
            repeated.append(word);
        }
        return k;
    }
```

[313. 超级丑数](https://leetcode.cn/problems/super-ugly-number/)

注意下标含义

```java
public int nthSuperUglyNumber(int n, int[] primes) {
        if(n==1) return 1;
        long dp[]=new long[n];//注意用long
        dp[0]=1;
        int nowidx[]=new int[primes.length];
        for(int i=1;i<n;++i)
        {
            long minnum=Long.MAX_VALUE;
            for(int j=0;j<primes.length;++j)
                minnum=Math.min(minnum,primes[j]*dp[nowidx[j]]);
            dp[i]=minnum;
            for(int j=0;j<primes.length;++j)
                if(dp[nowidx[j]]*primes[j]==minnum)
                    ++nowidx[j];
        }
        return (int)dp[n-1];
    }
```

[1594. 矩阵的最大非负积](https://leetcode.cn/problems/maximum-non-negative-product-in-a-matrix/)

本来用的深搜但是超时了，动态规划，记录到当前节点最小值和最大值矩阵，从上到下从左到右遍历

```java
public int maxProductPath(int[][] grid) {
        final int MOD = 1000000000 + 7;
        int m = grid.length, n = grid[0].length;
        long[][] maxgt = new long[m][n];
        long[][] minlt = new long[m][n];

        maxgt[0][0] = minlt[0][0] = grid[0][0];
        for (int i = 1; i < n; i++) {
            maxgt[0][i] = minlt[0][i] = maxgt[0][i - 1] * grid[0][i];
        }
        for (int i = 1; i < m; i++) {
            maxgt[i][0] = minlt[i][0] = maxgt[i - 1][0] * grid[i][0];
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (grid[i][j] >= 0) {
                    maxgt[i][j] = Math.max(maxgt[i][j - 1], maxgt[i - 1][j]) * grid[i][j];
                    minlt[i][j] = Math.min(minlt[i][j - 1], minlt[i - 1][j]) * grid[i][j];
                } else {
                    maxgt[i][j] = Math.min(minlt[i][j - 1], minlt[i - 1][j]) * grid[i][j];
                    minlt[i][j] = Math.max(maxgt[i][j - 1], maxgt[i - 1][j]) * grid[i][j];
                }
            }
        }
        if (maxgt[m - 1][n - 1] < 0) {
            return -1;
        } else {
            return (int) (maxgt[m - 1][n - 1] % MOD);
        }
    }
```

[299. 猜数字游戏](https://leetcode.cn/problems/bulls-and-cows/)

只有一个注意的地方就是b的增加是要加两者的最小值的

```java
public String getHint(String secret, String guess) {
        int a=0,b=0;
        int real[]=new int[10],gu[]=new int[10];
        for(int i=0;i<secret.length();++i)
        {
            char s=secret.charAt(i),g=guess.charAt(i);
            if(s==g)
                ++a;
            ++real[s-'0'];++gu[g-'0'];
        }
        for(int i=0;i<10;++i)
            if(real[i]>0) b+=Math.min(gu[i],real[i]);
        StringBuilder sb=new StringBuilder();
        sb.append(a).append('A').append(b-a).append('B');
        return sb.toString();
    }
```

[397. 整数替换](https://leetcode.cn/problems/integer-replacement/)

```java
//位运算与贪心和最后两位是11可以进行加一，会少一步，但是3不行
public int integerReplacement(int an) {
        long n=an;
        int step=0;
        while(n!=1)
        {
            if((n&1)!=1)
                n>>=1;
            else if(n!=3&&(n&3)==3)
                n+=1;
            else n-=1;
            ++step;
        }
        return step;
    }

//后序记忆化递归
Map<Long, Integer> map = new HashMap<>();
    public int integerReplacement(int n) {
        return dfs(n * 1L);
    }
    int dfs(long n) {
        if (n == 1) return 0;
        if (map.containsKey(n)) return map.get(n);
        int ans = n % 2 == 0 ? dfs(n / 2) : Math.min(dfs(n + 1), dfs(n - 1));
        map.put(n, ++ans);
        return ans;
    }
```

[769. 最多能完成排序的块](https://leetcode.cn/problems/max-chunks-to-make-sorted/)

注意到![1761550166794](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1761550166794.png)

当前值就是他应该在的下标

```java
public int maxChunksToSorted(int[] arr) {
        int step=0,maxlen=0;
        for(int i=0;i<arr.length;++i)
        {
            maxlen=Math.max(maxlen,arr[i]);
            if(maxlen==i) ++step;
        }
        return step;
    }
```

[853. 车队](https://leetcode.cn/problems/car-fleet/)

单调栈，正序遍历的方式

```java
public int carFleet(int target, int[] position, int[] speed) {
        // 1. 理解问题：车队的定义是“后车速度≥前车，最终会追上并合并为一个车队”
        // 核心逻辑：从终点往起点看，速度慢（到达时间长）的车会成为“领头车”，速度快的车会被它挡住合并
        // 用栈存储每个车队的“领头车到达时间”，栈的大小就是车队数量
        // 初始化time数组：索引=车辆初始位置，值=该位置车辆到达终点的时间
        // 数组长度设为target（最大可能位置是target-1），未停车的位置时间默认0
        double[] time = new double[target]; 
        // 2. 填充time数组：计算每辆车的到达时间
        for (int i = 0; i < position.length; i++) {
            int carPosition = position[i]; // 当前车的初始位置
            int carSpeed = speed[i];       // 当前车的速度
            // 到达时间 = 剩余距离 / 速度 → 剩余距离 = 终点(target) - 初始位置
            double arriveTime = (target - carPosition) / (double) carSpeed;
            // 将到达时间存入对应位置的time数组（比如位置3的车，time[3]就是它的到达时间）
            time[carPosition] = arriveTime; 
        }
        // 3. 用单调栈维护车队：栈中存储“每个车队领头车的到达时间”
        // 单调栈特性：从栈底到栈顶，到达时间递减（因为慢车在前，快车会被合并）
        Deque<Double> stack = new ArrayDeque<>(); 
        // 4. 从起点（位置0）往终点（位置target-1）遍历，模拟车辆行驶合并
        for (int i = 0; i < target; i++) {
            // 只处理有车的位置（time[i]>0说明该位置停了车）
            if (time[i] > 0) {
                // 关键合并逻辑：如果当前车的到达时间 ≥ 栈顶车的到达时间
                // 说明当前车速度更快（能追上栈顶车），会合并到栈顶车的车队，因此弹出栈顶（移除被合并的车队）
                while (!stack.isEmpty() && time[i] >= stack.peek()) {
                    stack.pop();
                }
                // 将当前车（或合并后的车队领头车）的到达时间压入栈
                stack.push(time[i]);
            }
        }
        // 5. 栈的大小就是最终车队数量（每个栈元素对应一个独立车队）
        return stack.size();
    }
```

[477. 汉明距离总和](https://leetcode.cn/problems/total-hamming-distance/)

算出每一位的0和1的个数直接相乘就行

```java
public int totalHammingDistance(int[] nums) {
        int ans = 0,n = nums.length;
        for(int i = 0;i < 30;i++){
            int c = 0;
            for(int val : nums){
                c += (val >> i) & 1;
            }
            ans += c * (n -c);
        }
        return ans;
    }
```

[1367. 二叉树中的链表](https://leetcode.cn/problems/linked-list-in-binary-tree/)

```java
public boolean dfs(TreeNode root,ListNode head)
    {
        if(head==null) return true;
        if(root==null) return false;
        if(root.val!=head.val) return false;
        return dfs(root.left,head.next)||dfs(root.right,head.next);
    }
    public boolean isSubPath(ListNode head, TreeNode root) {
        if(root==null) return false;
        return dfs(root,head)||isSubPath(head,root.left)||isSubPath(head,root.right);
    }
```

[423. 从英文中重建数字](https://leetcode.cn/problems/reconstruct-original-digits-from-english/)

脑筋急转弯，有一些数字的字符是它专有的，然后贪心优先构建就好

```java
String[] numStr = new String[]{"eight","four","two","six","zero","five", "one","seven","three", "nine"};
    public String originalDigits(String s) {
        int[] cnt = new int[26];
        for(char ch : s.toCharArray()){
            cnt[ch - 'a'] ++;
        }
        int[] cnt1 = new int[10];
        cnt1[0] = cnt['z' - 'a'];
        cnt1[2] = cnt['w' - 'a'];
        cnt1[6] = cnt['x' - 'a'];
        cnt1[4] = cnt['u' - 'a'];
        cnt1[8] = cnt['g' - 'a'];
        cnt1[5] = cnt['f' - 'a'] - cnt1[4];
        cnt1[3] = cnt['h' - 'a'] - cnt1[8];
        cnt1[7] = cnt['v' - 'a'] - cnt1[5];
        cnt1[9] = cnt['i' - 'a'] - cnt1[5] - cnt1[6] - cnt1[8];
        cnt1[1] = cnt['o' - 'a'] - cnt1[0] - cnt1[2] - cnt1[4];
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < 10; i++){
            int d = cnt1[i];
            for(int j = 0; j < d;j++){
                sb.append(i);
            }
        }
        return sb.toString();
    }
```

[859. 亲密字符串](https://leetcode.cn/problems/buddy-strings/)

![1761795225280](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\1761795225280.png)

```java
public boolean buddyStrings(String s, String goal) {
        int n = s.length(), m = goal.length();
        if (n != m) return false;
        int[] cnt1 = new int[26], cnt2 = new int[26];
        int sum = 0;
        for (int i = 0; i < n; i++) {
            int a = s.charAt(i) - 'a', b = goal.charAt(i) - 'a';
            cnt1[a]++; cnt2[b]++;
            if (a != b) sum++;
        }
        boolean ok = false;
        for (int i = 0; i < 26; i++) {
            if (cnt1[i] != cnt2[i]) return false;
            if (cnt1[i] > 1) ok = true;
        }
        return sum == 2 || (sum == 0 && ok);
    }
```

[795. 区间子数组个数](https://leetcode.cn/problems/number-of-subarrays-with-bounded-maximum/)

```java
public int numSubarrayBoundedMax(int[] nums, int left, int right) {
    int n = nums.length, ans = 0;
    int i0 = -1; // 记录最近一个 > right 的元素下标（非法位置）
    int i1 = -1; // 记录最近一个 >= left 的元素下标（潜在合法位置）
    // 遍历每个位置，动态更新以该位置为结尾的合法子数组个数
    for (int i = 0; i < n; ++i) {
        // 若当前元素超过上界 right，则该位置及之前的所有子数组都不再合法
        // 更新 i0 为当前下标，表示“最近的非法断点”
        if (nums[i] > right) i0 = i;
        // 若当前元素 >= left，则说明它可以作为一个子数组的最大值
        // 更新 i1 为当前位置
        if (nums[i] >= left) i1 = i;
        // 对于以 i 为结尾的子数组：
        // 所有起点在 (i0, i1] 区间内的子数组都是合法的
        // 因为它们的最大值在 [left, right] 范围内
        ans += i1 - i0;
    return ans;
}

```

[629. K 个逆序对数组](https://leetcode.cn/problems/k-inverse-pairs-array/)

```java
int mod = (int)1e9 + 7; // 模数，防止结果溢出
public int kInversePairs(int n, int k) {
    // f[i][j] 表示由 1~i 组成的所有排列中，恰好有 j 个逆序对的排列数量
    int[][] f = new int[n + 1][k + 1];
    // sum[i][j] 表示 f[i][0] + f[i][1] + ... + f[i][j] 的前缀和，用于快速区间求和优化
    int[][] sum = new int[n + 1][k + 1];
    // 初始条件：只有一个元素 {1} 的排列时，逆序对数为 0 的唯一排列
    f[1][0] = 1;
    Arrays.fill(sum[1], 1); // f[1][0] = 1，因此前缀和都是 1
    // 状态转移
    for (int i = 2; i <= n; i++) {          // 枚举排列长度
        for (int j = 0; j <= k; j++) {      // 枚举逆序对数量
            /*
             * 状态转移公式推导：
             * 当在长度为 i-1 的排列中插入新元素 i 时，
             * i 可以插入到任意位置（从最右到最左）。
             * 若插在最右，不增加逆序对；
             * 若插在最左，则增加 (i-1) 个逆序对。
             * 因此：
             *   f[i][j] = f[i-1][j] + f[i-1][j-1] + ... + f[i-1][j-(i-1)]
             *
             * 为了避免 O(i) 的求和，我们引入前缀和数组 sum[i-1][x] 来优化。
             * 所以：
             *   f[i][j] = sum[i-1][j] - sum[i-1][j-i]    (若 j >= i)
             *   f[i][j] = sum[i-1][j]                   (若 j < i)
             */
            if (j < i) {
                // 当 j < i 时，区间 [j-i, j] 的左边界越界，只能取 sum[i-1][j]
                f[i][j] = sum[i - 1][j];
            } else {
                // 否则取差值区间求和，并加 mod 防止负数
                f[i][j] = (sum[i - 1][j] - sum[i - 1][j - i] + mod) % mod;
            }
            // 更新当前行的前缀和 sum[i][j]
            if (j == 0) {
                sum[i][j] = f[i][j];
            } else {
                sum[i][j] = (sum[i][j - 1] + f[i][j]) % mod;
            }
        }
    }
    // 返回长度为 n 的排列中恰好有 k 个逆序对的数量
    return f[n][k];
}
```

[65. 有效数字](https://leetcode.cn/problems/valid-number/)

```java
public boolean isNumber(String s) {
    int n = s.length();
    char[] cs = s.toCharArray();  // 将字符串转为字符数组，方便逐字符处理
    int idx = -1;  // 记录 'e' 或 'E' 出现的位置，用于拆分科学计数法
    // Step 1：检测 'e' 或 'E' 出现的位置，同时检查其合法性（不能出现多个）
    for (int i = 0; i < n; i++) {
        if (cs[i] == 'e' || cs[i] == 'E') {
            // 如果第一次出现则记录下标
            if (idx == -1) idx = i;
            // 若再次出现，说明有多个 'e'/'E'，非法，直接返回 false
            else return false;
        }
    }
    boolean ans = true;
    /*
     * Step 2：根据是否存在 'e' 或 'E' 分两种情况处理
     *   ✅ 情况一：存在 'e'/'E'，说明是科学计数法，例如 "1.23e10"
     *       → 左侧为底数部分，可以是小数
     *       → 右侧为指数部分，必须为整数
     *   ✅ 情况二：不存在 'e'/'E'，说明是普通数字（整数或小数）
     */
    if (idx != -1) {
        // 检查底数部分 [0, idx-1]：允许小数
        ans &= check(cs, 0, idx - 1, false);
        // 检查指数部分 [idx+1, n-1]：必须是整数
        ans &= check(cs, idx + 1, n - 1, true);
    } else {
        // 没有 'e'/'E'，整体检查是否为合法数字（可含小数点）
        ans &= check(cs, 0, n - 1, false);
    }
    return ans;
}

/**
 * 检查字符数组 cs 的子区间 [start, end] 是否为合法数字
 * @param cs 字符数组
 * @param start 起始下标
 * @param end 结束下标
 * @param mustInteger 若为 true，表示该部分必须为整数（不能出现小数点）
 */
boolean check(char[] cs, int start, int end, boolean mustInteger) {
    // 若区间为空，说明没有数字（例如 "e" 或 "e+"），直接非法
    if (start > end) return false;
    // 允许首位出现正负号
    if (cs[start] == '+' || cs[start] == '-') start++;
    boolean hasDot = false;  // 标记是否出现过小数点
    boolean hasNum = false;  // 标记是否出现过数字
    for (int i = start; i <= end; i++) {
        if (cs[i] == '.') {
            /*
             * 遇到小数点时：
             *   ① 如果该部分必须是整数（mustInteger=true），则非法；
             *   ② 如果已经出现过小数点（hasDot=true），也非法；
             *   否则标记 hasDot=true
             */
            if (mustInteger || hasDot) return false;
            hasDot = true;
        } else if (cs[i] >= '0' && cs[i] <= '9') {
            // 若为数字字符，标记 hasNum=true
            hasNum = true;
        } else {
            // 若出现除数字、小数点、正负号以外的字符，直接非法
            return false;
        }
    }

    // 必须至少包含一个数字，否则像 "+" 或 "." 都不合法
    return hasNum;
}

```

[862. 和至少为 K 的最短子数组](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/)

```java
public static int shortestSubarray(int[] arr, int k) { 
    // 目标：返回和 ≥ k 的最短连续子数组长度，若不存在返回 -1
    // 算法思想（单调队列 +“前缀和差值 ≥ k”）：
    // 维护一个按前缀和值“严格递增”的双端队列（用数组模拟，l 为队头指针，r 为队尾指针的后一位），
    // 对每个位置 i，把前缀和 S[i] 入队前先把队尾中 ≥ S[i] 的前缀和弹出（保持递增），
    // 然后尽可能从队头弹出：只要 S[i] - S[队头] ≥ k，就可以更新答案并弹出队头，以期更短长度。
    //
    // 本实现是“在线”累加的变体：
    // preSum[] 保存“被保留下的单调递增前缀和”
    // queue[] 保存与 preSum[] 一一对应的“前缀位置下标”
    // 每轮把新元素 arr[i] 累加到当前队尾的前缀和上得到 t，相当于生成新的前缀和 S[i]；
    // 若 t 破坏递增性，则回退队尾（r--）直到递增，再把(t, i)入队；
    // 随后用队尾的前缀和（即 S[i]）去尽量缩短与队头之差 ≥ k 的区间，更新最短长度。

    int n = arr.length, l = 0, r = 1, i = 0;
    int ans = Integer.MAX_VALUE;

    long[] preSum = new long[n + 1]; // 保存保留下来的前缀和（严格递增），与 queue 同步
    int[] queue = new int[n + 1];    // 保存对应前缀和的“前缀下标”（S 的下标）

    // 初始化：空前缀 S[0] = 0 入队
    // 约定：queue 里存的是“前缀位置”，preSum 里存的是相同位置的前缀和
    preSum[0] = 0;
    queue[0] = 0;

    // 主循环：在线处理每个元素 arr[i]，持续维护“单调递增的前缀和队列”
    while (i < n) {
        // 1) 生成“新的前缀和”：
        //    若当前队尾对应的前缀和是 S[p]，把 arr[i] 累加得到 t = S[p] + arr[i]。
        //    在常规前缀和写法中 t 应该是 S[i] = S[i-1] + arr[i]，
        //    这里通过维护与回退队尾，保证队尾对应的前缀位置同步推进，从而在线得到“有效的当前前缀和”。
        long t = preSum[r - 1] + arr[i];

        // 2) 保持 preSum 严格递增：
        //    若新前缀和 t 小于等于队尾的前缀和 preSum[r-1]，则弹出队尾（r--），直到 t 能接在后面保持递增。
        //    直觉：较大的前缀和在后续求差 S[j]-S[i] 时更“吃亏”，被更小的 t 支配，因此可以丢弃。
        while (l < r && preSum[r - 1] >= t) {
            r--; // 回退队尾，丢弃较差的前缀和
        }

        // 3) 把新的前缀和与其“前缀下标”入队：
        //    这里 ++i 把当前位置前缀索引推进到 i（对应 S[i]），并与 t 绑定。
        preSum[r] = t;
        queue[r++] = ++i;

        // 4) 使用当前“最新前缀和”（队尾即 S[i]）尽可能缩短满足和 ≥ k 的区间：
        //    只要 S[i] - S[队头] ≥ k，说明从(队头位置)到 i 的子数组和 ≥ k，
        //    就尝试更新最短长度，并把队头弹出以期获得更短的区间（更大的 S[队头] 会让长度更短或同样短）。
        while (preSum[r - 1] - preSum[l] >= k) {
            // queue[r-1] 是当前前缀位置 i，queue[l] 是被减的前缀位置 p
            // 子数组区间为 (p, i]，长度 = i - p
            ans = Math.min(ans, queue[r - 1] - queue[l++]);
        }
    }

    // 若未找到任何和 ≥ k 的子数组，则返回 -1
    return ans == Integer.MAX_VALUE ? -1 : ans;
}

/*
【正确性要点与循环不变式】
1) 单调性不变式：
   在 while(i<n) 主循环任意时刻，preSum[l..r-1] 严格递增。
   维护方式：每次产生新前缀和 t 前，若 t ≤ preSum[r-1]，持续 r-- 弹出队尾，直至满足递增。

2) 最优性（可行解尽可能短）：
   对固定的“当前 i（即队尾前缀）”，从队头开始检查差值：
   只要 preSum[i] - preSum[队头] ≥ k，就更新答案并弹出队头，原因：
   - 队头越往右，前缀索引越大，区间 (队头, i] 越短；
   - 弹出后继续尝试可能得到更短的满足条件的区间；
   - 若不弹出，后续 i' > i 时，preSum[i'] 通常更大，仍可用更右的队头获得更短区间。

3) 支配（淘汰）原则：
   当有两个前缀 (p1, S[p1]) 与 (p2, S[p2]) 且 p1 < p2、S[p1] ≥ S[p2]，
   对任何后续 j，S[j] - S[p2] ≥ S[j] - S[p1]，并且 p2 更靠后使区间更短，
   因此 (p1, S[p1]) 永远不优于 (p2, S[p2])，故可从队尾删除。

【时间复杂度】
- 每个前缀（元素）至多入队一次、出队一次：两个 while 都是均摊 O(1)。
- 整体 O(n)，空间 O(n)。
*/

```

[31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

分为三步，从后往前找到第一个下降的数字，如果找到了这样的数字，找到从右边开始找第一个比它大的数字并交换，反转 i 之后的子数组

```java
public void nextPermutation(int[] nums) {
        int idx=nums.length-2;
        while(idx>=0&&nums[idx]>=nums[idx+1])
            --idx;
        if(idx>=0)
        {
            int r=nums.length-1;
            while(nums[r]<=nums[idx])
                --r;
            swap(nums,r,idx);
        }
        reverse(nums,idx+1, nums.length-1);
    }

    void swap(int[] nums,int left,int right)
    {
        int temp=nums[left];
        nums[left]=nums[right];
        nums[right]=temp;
    }

    void reverse(int[] nums,int left,int right)
    {
        while(left<right)
        {
            swap(nums,left,right);
            ++left;--right;
        }
    }
```

[224. 基本计算器](https://leetcode.cn/problems/basic-calculator/)

计算器通解，给我背下来

```java
public int calculate(String s) {
        // 存放所有的数字
        Deque<Integer> nums = new ArrayDeque<>();
        // 为了防止第一个数为负数，先往 nums 加个 0
        nums.addLast(0);
        // 将所有的空格去掉
        s = s.replaceAll(" ", "");
        // 存放所有的操作，包括 +/-
        Deque<Character> ops = new ArrayDeque<>();
        int n = s.length();
        char[] cs = s.toCharArray();
        for (int i = 0; i < n; i++) {
            char c = cs[i];
            if (c == '(') {
                ops.addLast(c);
            } else if (c == ')') {
                // 计算到最近一个左括号为止
                while (!ops.isEmpty()) {
                    char op = ops.peekLast();
                    if (op != '(') {
                        calc(nums, ops);
                    } else {
                        ops.pollLast();
                        break;
                    }
                }
            } else {
                if (isNum(c)) {
                    int u = 0;
                    int j = i;
                    // 将从 i 位置开始后面的连续数字整体取出，加入 nums
                    while (j < n && isNum(cs[j])) u = u * 10 + (int)(cs[j++] - '0');
                    nums.addLast(u);
                    i = j - 1;
                } else {
                    if (i > 0 && (cs[i - 1] == '(' || cs[i - 1] == '+' || cs[i - 1] == '-')) {
                        nums.addLast(0);
                    }
                    // 有一个新操作要入栈时，先把栈内可以算的都算了
                    while (!ops.isEmpty() && ops.peekLast() != '(') calc(nums, ops);
                    ops.addLast(c);
                }
            }
        }
        while (!ops.isEmpty()) calc(nums, ops);
        return nums.peekLast();
    }
    void calc(Deque<Integer> nums, Deque<Character> ops) {
        if (nums.isEmpty() || nums.size() < 2) return;
        if (ops.isEmpty()) return;
        int b = nums.pollLast(), a = nums.pollLast();
        char op = ops.pollLast();
        nums.addLast(op == '+' ? a + b : a - b);
    }
    boolean isNum(char c) {
        return Character.isDigit(c);
    }
```

计算器2

```java
HashMap<Character,Integer> hm=new HashMap<>(){{
            put('-', 1);  // 减法优先级1
            put('+', 1);  // 加法优先级1
            put('*', 2);  // 乘法优先级2
            put('/', 2);  // 除法优先级2
            put('%', 2);  // 取模优先级2
            put('^', 3);
    }};
    Deque<Integer> nums=new LinkedList<>();
    Deque<Character> sign=new LinkedList<>();
    public int calculate(String s) {
        s=s.replaceAll(" ","");//去除空格
        char []str=s.toCharArray();
        nums.add(0);//添加这个0主要是防止第一个符号是-
        for(int i=0;i<s.length();++i)
        {
            char c=str[i];
            if(c=='(') sign.add(c);//先判断左括号
            else if(c==')'){
                while(sign.peekLast()!='(')//再判断右括号，然后一直计算到左括号
                    cal();
                sign.removeLast();//别忘记把左括号删掉
            }else if(Character.isDigit(c)){
                int j=i;
                int num=0;
                while(j<s.length()&&Character.isDigit(str[j]))
                {
                    num=num*10+str[j]-'0';
                    ++j;
                }
                nums.add(num);//一直读到最后一个数字
                i=j-1;//读到数字之后调指针到最后一个数字，别忘了for循环后面有一个i++
            }else{//说明是符号
                if(i>0&&(str[i-1]=='('||str[i-1]=='+'||str[i-1]=='-'))
                    nums.add(0);//符号前是左括号或者加或者减要自动添加一个0，（+1 （-1 +(1这种情况
                while(!sign.isEmpty()&&sign.peekLast()!='('
                      &&hm.get(sign.peekLast())>=hm.get(str[i]))
                    //三个判断条件，符号栈不为空，栈头不是（，当前符号优先级平级或者更低
                    cal();
                sign.add(c);//最后加入当前符号
            }
        }
        while(!sign.isEmpty())
            cal();//最后都是同级的符号或者当前是高等级符号前面都是低等级符号
        return nums.peekLast();//返回最后一个值，因为有可能最前面那个0是没有计算的
    }

    void cal()//强制计算
    {
        if (nums.size() < 2 || sign.isEmpty()) {
            return;
        }
        int n2=nums.pollLast(),n1=nums.pollLast();
        char s=sign.pollLast();
        if(s=='*') nums.add(n1*n2);
        else if(s=='/') nums.add(n1/n2);
        else if(s=='+') nums.add(n1+n2);
        else nums.add(n1-n2);
    }
```

[阿拉伯数字转中文_牛客题霸_牛客网](https://www.nowcoder.com/practice/6eec992558164276a51d86d71678b300?tpId=196&tqId=40404&rp=1&ru=/exam/oj&qru=/exam/oj&sourceUrl=%2Fexam%2Foj%3Fpage%3D1%26tab%3D%E7%AE%97%E6%B3%95%E7%AF%87%26topicId%3D196&difficulty=undefined&judgeStatus=undefined&tags=&title=中文)

```java
import java.util.*;

public class Solution {
    public String num2cn(int n) {
        // 特殊情况：输入为0时直接返回"零"
        if (n == 0)
            return "零";
        
        // 将数字转为绝对值的字符数组（处理负数，最后再补"负"字）
        char[] words = String.valueOf(Math.abs(n)).toCharArray();
        
        // 单位映射：索引对应位数（个位/十位/百位/千位/万位/亿位等）
        // 索引0:个位(空)、1:十位、2:百位、3:千位、4:空（万级分隔）、5:万、6:亿
        String[] unitsMap = new String[]{"", "十", "百", "千", "", "万", "亿"};
        
        // 数字映射：索引对应0-9的中文表达
        String[] digitsMap = new String[]{"零", "一", "二", "三", "四", "五", "六", "七", "八", "九"};
        
        // 最终结果的构建器
        StringBuilder builder = new StringBuilder();
        
        // 分节处理数字：中文数字按"万/亿"分节，每节最多4位（个/十/百/千）
        // cnt：当前处理的节数（0:个级，1:万级，2:亿级...）
        // len：当前节的位数（最多4位）
        for (int cnt = 0, len = Math.min(words.length, 4); len > 0; len = Math.min(words.length - cnt * 4, 4)) {
            // 存储当前节的中文（如"一千二百三十四"）
            StringBuilder segBuilder = new StringBuilder();
            
            // pos：当前节最后一位数字在原字符数组中的索引（从后往前定位节的位置）
            int pos = words.length - 1 - cnt * 4;
            
            // 标记当前节是否全为0（全为0则无需添加该节）
            boolean full0 = true;
            
            // 遍历当前节的每一位（从高位到低位，如千位→百位→十位→个位）
            for (int i = len - 1; i >= 0; i--) {
                // 当前位的数字值（字符转数字）
                int val = words[pos - i] - '0';
                
                // 如果当前位非0
                if (val != 0) {
                    full0 = false; // 标记当前节非全0
                    // 拼接数字中文 + 对应单位（如"三"+"百"="三百"）
                    segBuilder.append(digitsMap[val]).append(unitsMap[i]);
                }
            }
            
            // 如果当前节非全0，添加节单位（如万级加"万"，亿级加"亿"）
            if (!full0)
                segBuilder.append(unitsMap[cnt + 4]);
            
            // 将当前节结果插入到最终结果的最前面（因为是从低位节往高位节处理）
            builder.insert(0, segBuilder);
            
            cnt++; // 处理下一节（万级→亿级）
        }
        
        // 处理特殊情况：开头为"一十"时简化为"十"（如10→"十"而非"一十"）
        String val = builder.toString().replaceAll("^一十","十");
        
        // 处理负数：原数为负则加"负"前缀，否则直接返回结果
        return n < 0 ? "负" + val : val;
    }
}
```

