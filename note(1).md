# 数组

## 二分查找

704easy 二分查找 * 【左闭右闭】

https://leetcode.cn/problems/binary-search/

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] < target) left = mid + 1;
            else if(nums[mid] > target) right = mid - 1;
            else return mid;
        }
        return -1;
    }
}
```

35easy 搜索插入位置 * 【和“二分查找”类似，关键在插入的位置】

https://leetcode.cn/problems/search-insert-position/

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] < target) left = mid + 1;
            else if(nums[mid] > target) right = mid - 1;
            else return mid;
        }
        return right + 1;
    }
}
```

34mid 在排序数组中查找元素的第一个和最后一个位置 *

https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftBorder = getLeftBorder(nums, target);
        int rightBorder = getRightBorder(nums, target);
        if(leftBorder == -2 || rightBorder == -2) return new int[]{-1, -1};
        if(rightBorder - leftBorder > 1) return new int[]{leftBorder + 1, rightBorder -1};
        return new int[]{-1, -1};
    }
    //获取右边界
    private int getRightBorder(int[] nums, int target) {
        int rightBorder = -2;
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] < target) {
                left = mid + 1;
                rightBorder = left;
            } else if(nums[mid] > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
                rightBorder = left;
            }
        }
        return rightBorder;
    }
    //获取左边界
    private int getLeftBorder(int[] nums, int target) {
        int leftBorder = -2;
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] < target) {
                left = mid + 1;                
            } else if(nums[mid] > target) {
                right = mid - 1;
                leftBorder = right;
            } else {
                right = mid - 1;
                leftBorder = right;
            }
        }
        return leftBorder;
    }
}
```

69easy x的平方根 （注意x==0和x==1的情况）* 

https://leetcode.cn/problems/sqrtx/

```java
class Solution {
    public int mySqrt(int x) {
        if(x == 0 || x == 1) return x;
        int left = 1, right = x ;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            long sq = (long) mid * mid;
            if(sq < x) left = mid + 1;
            else if(sq > x) right = mid - 1;
            else return mid;
        }
        return right; //跳出循环就说明right < left了
    }
}
```

367easy 有效的完全平方数 *  【和“x的平方根”类似】

https://leetcode.cn/problems/valid-perfect-square/

```java
class Solution {
    public boolean isPerfectSquare(int num) {
        int left = 1, right = num;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            long sq = (long) mid * mid;
            if(sq < num) left = mid + 1;
            else if(sq > num) right = mid - 1;
            else return true;
        }
        return false;
    }
}
```



## 移除元素

27easy 移除元素 （快慢指针）* 

【最后的slow指向的下标，就是移除元素后的长度，指向最后一个元素的下一位】

https://leetcode.cn/problems/remove-element/

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int slow = 0;
        for(int fast = 0; fast < nums.length; fast++) {
            if(nums[fast] != val) {
                nums[slow] = nums[fast];
                slow++;
            }
        }
        return slow;
    }
}
```

26easy 删除有序数组中的重复项  【和“移除元素”类似】

https://leetcode.cn/problems/remove-duplicates-from-sorted-array/

【快慢指针（ck写出）】*

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums.length == 1) return 1; 
        int slow = 1;
        for(int fast = 1; fast < nums.length; fast++) {
            if(nums[fast] != nums[fast - 1]) { //比较当前元素和前一个元素是否相同
                nums[slow] = nums[fast];
                slow++;
            }
        }
        return slow;
    }
}
```

283easy 移动零 【快慢指针】* 【和“移除元素”类似】

https://leetcode.cn/problems/move-zeroes/

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int slow = 0;
        for(int fast = 0; fast < nums.length; fast++) {
            if(nums[fast] != 0) {
                nums[slow] = nums[fast];
                slow++;
            }
        }
        for(int i = slow; i < nums.length; i++) {
            nums[i] = 0;
        }
    }
}
```

844easy 比较含退格的字符串  【双指针，倒序】 *

https://leetcode.cn/problems/backspace-string-compare/

```java
class Solution {
    public boolean backspaceCompare(String s, String t) {
        char[] charS = s.toCharArray();
        char[] charT = t.toCharArray();
        int slow1 = charS.length - 1;
        int k1 = 0; //标记charS跳过的次数
        for(int fast = charS.length - 1; fast >= 0; fast--) { //倒序遍历
            if(charS[fast] != '#') {                
                if(k1 == 0) {
                    charS[slow1] = charS[fast];
                    slow1--;   
                } else {
                    k1--; //k1 > 0，跳过赋值，并k1--
                }
            } else k1++;//遍历到'#',k1++
        }
        int slow2 = charT.length - 1;
        int k2 = 0; //标记charT跳过的次数
        for(int fast = charT.length - 1; fast >= 0; fast--) { //倒序遍历
            if(charT[fast] != '#') {
                if(k2 == 0) {
                    charT[slow2] = charT[fast];
                    slow2--;
                } else { //k2 > 0，跳过赋值，并k2--
                    k2--;
                }
            } else k2++; //遍历到'#',k2++
        }
        if((charS.length - 1 - slow1) != (charT.length - 1 - slow2)) return false;
        for(int i = charS.length - 1, j = charT.length - 1; i > slow1; i--, j--) {
            if(charS[i] != charT[j]) return false;
        }
        return true;
    }
}
```

977easy 有序数的平方 【 双指针，一首一尾。将数组倒序插入】 *

## 有序数组的平方

977easy 有序数的平方 

https://leetcode.cn/problems/squares-of-a-sorted-array/

【 双指针，一首一尾。将数组倒序插入】 *

【一定要倒序，因为原数组两侧的数字的平方，一定有一个是最大值】

【重新定义一个数组用于赋值】

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int[] res = new int[nums.length];
        int left = 0, right = nums.length - 1;
        int index = nums.length - 1;
        while(left <= right) {
            if(nums[right] * nums[right] > nums[left] * nums[left]) {
                res[index] = nums[right] * nums[right];
                index--;
                right--;
            } else {
                res[index] = nums[left] * nums[left];
                index--;
                left++;
            }
        }
        return res;
    }
}
```



## 长度最小的子数组

209mid 长度最小的子数组  

https://leetcode.cn/problems/minimum-size-subarray-sum/

【滑动窗口（双指针，先滑动终止位置right，当sum >= target时，开始while循环，计算最小的长度res,再滑动起始位置left）】 *

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int res = Integer.MAX_VALUE;
        int left = 0; //慢指针，指向滑动窗口的左端
        int sum = 0; //记录滑动窗口内总和
        for(int right = 0; right < nums.length; right++) {
            sum += nums[right]; //遍历时更新sum
            while(sum >= target) {//当sum >= target时，计算窗口长度，并将窗口左端向右移，缩短窗口
                int subLength = right - left + 1;
                res = Math.min(res, subLength);
                sum -= nums[left]; //缩短窗口时更新sum
                left++;
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

904mid 水果成篮 

https://leetcode.cn/problems/fruit-into-baskets/

【滑动窗口，双指针，map存储】 * 【和“长度最小的子数组”类似，求的是最大长度】

【两种类型的水果必须是连续成串的】

```java
class Solution {
    public int totalFruit(int[] fruits) {
      Map<Integer, Integer> map = new HashMap<>();
      int left = 0;
      int len = 0;
      for(int right = 0; right < fruits.length; right++) {
          map.put(fruits[right], map.getOrDefault(fruits[right], 0) + 1);
          while(map.size() > 2) {
              map.put(fruits[left], map.get(fruits[left]) - 1);
              if(map.get(fruits[left]) == 0) map.remove(fruits[left]);
              left++;
          }
          len = Math.max(len, right - left + 1);
      }
      return len;
    }
}
```

76hard 最小覆盖子串 （滑动窗口）

https://leetcode.cn/problems/minimum-window-substring/

【windowMap.get(c).equals(needMap.get(c))，不能用==判断。如果是2个Integer，==比较的对象是否相等，equals比较的是值是否相等。】*

```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> needMap = new HashMap<>();
        Map<Character, Integer> windowMap = new HashMap<>();
        char[] charS = s.toCharArray();
        char[] charT = t.toCharArray();
        for(char ch : charT) needMap.put(ch, needMap.getOrDefault(ch, 0) + 1);
        int right = 0, left = 0, start = 0, len = Integer.MAX_VALUE;
        int valid = 0; //标记每一种字符是否都在windowmap内满足应有数量
        while(right < charS.length) {
            char s1 = charS[right];
            right++;
            if(needMap.containsKey(s1)) {
                windowMap.put(s1, windowMap.getOrDefault(s1, 0) + 1);
                if(windowMap.get(s1).equals(needMap.get(s1))) valid++;
            }
            while(valid == needMap.size()) {
                if(right - left < len) {
                    len = right - left;
                    start = left;
                    // res = s.substring(left, right); //每次截取字符串很消耗性能
                }
                char s2 = charS[left];
                left++;
                if(windowMap.containsKey(s2)) {
                    if(windowMap.get(s2).equals(needMap.get(s2))) valid--;
                    windowMap.put(s2, windowMap.get(s2) - 1);
                }                
            }
        }
        //右端不能用right，因为right是按顺序遍历到最右端的，但遍历到的字符不一定添加进windowMap
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len); 
    }
}
```



## 螺旋矩阵Ⅱ

59mid 螺旋矩阵Ⅱ 【把握分界点、loop次数】 *

https://leetcode.cn/problems/spiral-matrix-ii/

<img src="E:\2021java\Typora\typora-user-images\20220922102236.png" alt="img" style="zoom: 33%;" />

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] res = new int[n][n];
        int startX = 0, startY = 0; //startX为纵向起点，startY为横向起点
        int i = 0, j = 0; //i为纵向，j为横向
        int loop = 0;
        int count = 1;
        int offset = 1; //给终点留的格数
        while(loop < n/ 2) {
            for(j = startY; j < n - offset; j++) {
                res[startX][j] = count++;
            }
            for(i = startX; i < n - offset; i++) {
                res[i][j] = count++;
            }
            for(; j > startY; j--) {
                res[i][j] = count++;
            }
            for(; i > startX; i--) {
                res[i][j] = count++;
            }
            startX++;
            startY++;
            offset++;
            loop++;
        }
        if(n % 2 == 1) res[startX][startY] = count;
        return res;
    }
}
```

剑指offer29easy 顺时针打印矩阵 【与54mid 螺旋矩阵一样】 *

54mid 螺旋矩阵 *

https://leetcode.cn/problems/spiral-matrix/

【遍历顺序和“螺旋矩阵Ⅱ”一样】【注意第一层循环的终止条件】

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int lenX = matrix.length;
        int lenY = matrix[0].length;
        int total = lenX * lenY;
        List<Integer> list = new ArrayList<>();
        int startX = 0, startY = 0;
        int i = 0, j = 0;
        int offset = 1;
        while(startX < lenX - offset || startY < lenY - offset) {
            for(j = startY; j < lenY - offset; j++) {
                list.add(matrix[startX][j]);
                if(list.size() == total) break;
            }
            if(list.size() == total) break;
            for(i = startX; i < lenX - offset; i++) {
                list.add(matrix[i][j]);
                if(list.size() == total) break;
            }
            if(list.size() == total) break;
            for(; j > startY; j--) {
                list.add(matrix[i][j]);
                if(list.size() == total) break;
            }
            if(list.size() == total) break;
            for(; i > startX; i--) {
                list.add(matrix[i][j]);
                if(list.size() == total) break;
            }
            if(list.size() == total) break;
            startX++;
            startY++;
            offset++;
        }
        if(list.size() < total) list.add(matrix[startX][startY]);
        return list;
    }
}
```

简化一下：

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        LinkedList<Integer> list = new LinkedList<>();
        int m = matrix.length, n = matrix[0].length;
        int startX = 0, startY = 0;
        int i = 0, j = 0, offset = 1;
        while(startX < m - offset || startY < n - offset) {
            for(j = startY; j < n - offset && list.size() < m * n; j++) {
                list.add(matrix[startX][j]);
            }
            for(i = startX; i < m - offset && list.size() < m * n; i++) {
                list.add(matrix[i][j]);
            }
            for(; j > startY && list.size() < m * n; j--) {
                list.add(matrix[i][j]);
            }
            for(; i > startX && list.size() < m * n; i--) {
                list.add(matrix[i][j]);
            }
            if(list.size() == m * n) break;
            startX++;
            startY++;
            offset++;
        }
        if(list.size() < m * n) list.add(matrix[startX][startY]);
        return new ArrayList<>(list);
    }
}
```



# 链表

对于最后要返回链表的问题，大多数需要定义虚拟头结点（反转链表除外，因为返回的链表首尾变了）

对于最后要返回结点的问题，大多数不需要定义虚拟头结点

## 移除链表元素
203easy 移除链表元素 【定义虚拟头指针】 

https://leetcode.cn/problems/remove-linked-list-elements/

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head == null) return head;
        ListNode preHead = new ListNode(-1, head); //虚拟头结点，同时定义了cur指向head
        ListNode cur = preHead;        
        while(cur.next != null) {
            if(cur.next.val == val) cur.next = cur.next.next;
            else cur = cur.next;            
        }
        return preHead.next;
    }
}
```



## 设计链表
707mid 设计链表 【定义虚拟头指针】【记得在添加节点后size++，删除节点后size--】

https://leetcode.cn/problems/design-linked-list/

【想在index位置前插入一个节点，应该按如下遍历，让cur停在index的前一位节点】

```java
for(int i = 0; i < index; i++) {
      cur = cur.next;
}
```



```java
class ListNode{
    int val;
    ListNode next;
    public ListNode(){}
    public ListNode(int val) {
        this.val = val;
    }
}
class MyLinkedList {
    ListNode dummyHead;
    int size;

    public MyLinkedList() {
        dummyHead = new ListNode(-1);
        size = 0;
    }
    
    public int get(int index) {
        if(index < 0 || index >= size) return -1;
        ListNode cur = dummyHead;
        for(int i = 0; i < index; i++) {
            cur = cur.next;
        }
        return cur.next.val;
    }
    
    public void addAtHead(int val) {
        addAtIndex(-1, val);
    }
    
    public void addAtTail(int val) {
        addAtIndex(size, val);
    }
    
    public void addAtIndex(int index, int val) {
        ListNode node = new ListNode(val);
        if(index == size) {            
            ListNode cur = dummyHead;
            while(cur != null) {
                if(cur.next == null) {
                    cur.next = node;
                    size++;
                    break;
                }
                else cur = cur.next;
            }
        } 
        else if(index > size) return;
        else if(index < 0) {
            node.next = dummyHead.next;
            dummyHead.next = node;
            size++;
        } 
        else{
            ListNode cur = dummyHead;
            for(int i = 0; i < index; i++) {
                cur = cur.next;
            }
            node.next = cur.next;
            cur.next = node;
            size++;
        }
    }
    
    public void deleteAtIndex(int index) {
        if(index < 0 || index >= size) return;
        ListNode cur = dummyHead;
        for(int i = 0; i < index; i++) {
            cur = cur.next;
        }
        cur.next = cur.next.next;
        size--;
    }
}

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList obj = new MyLinkedList();
 * int param_1 = obj.get(index);
 * obj.addAtHead(val);
 * obj.addAtTail(val);
 * obj.addAtIndex(index,val);
 * obj.deleteAtIndex(index);
 */
```



简化一下：

```java
class ListNode{
    int val;
    ListNode next;
    public ListNode(){}
    public ListNode(int val) {
        this.val = val;
    }
    public ListNode(int val, ListNode next) {
        this.val = val;
        this.next = next;
    }
}
class MyLinkedList {
    ListNode dummyHead;
    int size; 

    public MyLinkedList() {
        dummyHead = new ListNode(0);
        size = 0;
    }
    
    public int get(int index) {
        if(index < 0 || index >= size) return -1;
        ListNode cur = dummyHead;
        while(index-- > 0) cur = cur.next;
        return cur.next.val;
    }
    
    public void addAtHead(int val) {
        addAtIndex(-1, val);
    }
    
    public void addAtTail(int val) {
        addAtIndex(size, val);
    }
    
    public void addAtIndex(int index, int val) {
        if(index > size) return;
        ListNode node = new ListNode(val);
        if(index < 0) {            
            ListNode tmp = dummyHead.next;
            node.next = tmp;
            dummyHead.next = node;
        } else {
            ListNode cur = dummyHead;
            while(index-- > 0) {
                cur = cur.next;
            }
            ListNode tmp = cur.next;
            node.next = tmp;
            cur.next = node;
        }
        size++;  
    }
    
    public void deleteAtIndex(int index) {
        if(index < 0 || index >= size) return;
        ListNode cur = dummyHead;
        while(index-- > 0) {
            cur = cur.next;
        }
        cur.next = cur.next.next;
        size--;
    }
}

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList obj = new MyLinkedList();
 * int param_1 = obj.get(index);
 * obj.addAtHead(val);
 * obj.addAtTail(val);
 * obj.addAtIndex(index,val);
 * obj.deleteAtIndex(index);
 */
```



## 反转链表
206easy 反转链表 【双指针/递归】

https://leetcode.cn/problems/reverse-linked-list/

【双指针】

![image-20221127191429911](E:\2021java\Typora\typora-user-images\image-20221127191429911.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode cur = head;
        ListNode pre = null;
        while(cur != null) { //每次遍历都是将cur指向pre，然后pre右移，然后cur右移
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```

【递归 + 双指针】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        return reverse(null, head);
    }
    ListNode reverse(ListNode pre, ListNode cur) {
        //终止条件
        if(cur == null) return pre;
        ListNode temp = cur.next;
        cur.next = pre;
        pre = cur;
        cur = temp;
        return reverse(pre, cur);
    }
}
```

92mid 反转链表 II

https://leetcode.cn/problems/reverse-linked-list-ii/

【先将中间段反转，再将头段指向中间段尾部，将中间段头部指向尾段】

<img src="E:\2021java\Typora\typora-user-images\image-20221228013308370.png" alt="image-20221228013308370" style="zoom: 33%;" />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode dummyHead = new ListNode(-1, head); //虚拟头结点
        ListNode pre = dummyHead; //pre指针
        int start = left;
        start--;
        while(start-- > 0) {
            pre = pre.next; //找到left位置的前一个节点
        }
        int len = right - left + 1; //计算反转部分的长度
        ListNode temp1 = pre; //前段的尾部，反转后为首部
        ListNode cur = pre.next;
        ListNode temp2 = cur; //反转部分（中间段）的头部，反转后为尾部
        while(len-- > 0) { //反转中间部分
            ListNode tmep = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmep;
        }
        temp1.next = pre; //前段尾部指向反转部分反转后的首部
        temp2.next = cur; //反转部分反转后的尾部指向后段的首部
        return dummyHead.next;
    }
}
```



## 两两交换链表中的节点
24mid 两两交换链表中的节点 【虚拟头节点/递归】

https://leetcode.cn/problems/swap-nodes-in-pairs/

【虚拟头结点+非递归】

<img src="E:\2021java\Typora\typora-user-images\image-20221127000658468.png" alt="image-20221127000658468" style="zoom: 50%;" />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode dummyHead = new ListNode(-1, head);
        ListNode cur = dummyHead;
        while(cur.next != null && cur.next.next != null) {
            ListNode temp1 = cur.next;
            ListNode temp3 = cur.next.next.next;
            cur.next = temp1.next;
            cur.next.next = temp1;
            temp1.next = temp3;
            cur = temp1;
        }
        return dummyHead.next;
    }
}
```

【递归】

<img src="E:\2021java\Typora\typora-user-images\image-20221127002524522.png" alt="image-20221127002524522" style="zoom: 25%;" />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode newHead = head.next;
        head.next = swapPairs(newHead.next);
        newHead.next = head;
        return newHead;
    }
}
```



## 删除链表的倒数第N个结点

19mid 删除链表的倒数第N个结点

https://leetcode.cn/problems/remove-nth-node-from-end-of-list/

【虚拟头结点，快慢指针，让fast先走n+1步，最后fast和slow同时走，直到fast指向null，此时slow停留在被删除节点的前一位】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummyHead = new ListNode(-1, head);
        ListNode fast = dummyHead, slow = dummyHead;
        for(int i = 0; i < n + 1; i++) {
            fast = fast.next;
        }
        while(fast != null) {
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return dummyHead.next;
    }
}
```



## 链表相交

160easy 链表相交 【双指针，比较的是结点而不是结点的值】

https://leetcode.cn/problems/intersection-of-two-linked-lists/

<img src="E:\2021java\Typora\typora-user-images\面试题02.07.链表相交_1.png" alt="面试题02.07.链表相交_1" style="zoom:50%;" />

求出两个链表的长度，并求出两个链表长度的差值，然后让curA移动到，和curB 末尾对齐的位置

<img src="E:\2021java\Typora\typora-user-images\面试题02.07.链表相交_2.png" alt="面试题02.07.链表相交_2" style="zoom:50%;" />

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int lenA = 0, lenB = 0;
        int gap = 0; //两条链表的长度差
        ListNode curA = headA, curB = headB;
        while(curA != null) {
            lenA++; //求A链表长度
            curA = curA.next;
        }
        while(curB != null) {
            lenB++; //求B链表长度
            curB = curB.next;
        }
        curA = headA;
        curB = headB;
        gap = Math.abs(lenA - lenB);
        if(lenA > lenB) {//A链表长于B链表
            while(gap > 0) { //将curA指向一个位置，使A链和B链末尾对齐
                curA = curA.next;
                gap--;
            }
            while(curA != null) {
                if(curA == curB) return curA; //遍历节点，如果相等就返回
                curA = curA.next;
                curB = curB.next;
            }
        } else { //B链表长于A链表
            while(gap > 0) { //将curB指向一个位置，使B链和A链末尾对齐
                curB = curB.next;
                gap--;
            }
            while(curB != null) {
                if(curB == curA) return curB; //遍历节点，如果相等就返回
                curA = curA.next;
                curB = curB.next;
            }
        }
        return null; //没有找到相等节点，返回null
    }
}
```

【简短版本】

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode nodeA = headA, nodeB = headB;
        while(nodeA != nodeB){
            nodeA = nodeA != null ? nodeA.next : headB;
            nodeB = nodeB != null ? nodeB.next : headA;
        }
        return nodeA;
    }
}
```

面试题02.07easy 链表相交 【同上】

## 环形链表II

142mid 环形链表II 【快慢指针，先判断有环，再同时从head和相遇处出发，找到环的入口】

https://leetcode.cn/problems/linked-list-cycle-ii/

<img src="E:\2021java\Typora\typora-user-images\20220925103433.png" alt="img" style="zoom:50%;" />

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while(fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if(fast == slow) { //找到相遇点
                ListNode cur1 = fast; 
                ListNode cur2 = head;
                //定义指针分别指向头结点和相遇点，同时前进，相遇点即为入环口
                while(cur1 != cur2) {
                    cur1 = cur1.next;
                    cur2 = cur2.next;
                }
                return cur1;
            }
        }
        return null;
    }
}
```



# 哈希表

【“有效的字母异位词”、“赎金信”用数组做映射】

【“两个数组的交集”、“快乐数”用set，重复的数据只存一次】

【“两个数组的交集II”、“两数之和”、“四数相加II”用map】

【“三数之和”、“四数之和”用双指针，剪枝和去重】

## 有效的字母异位词

242easy  有效的字母异位词 【构建数组来做哈希表】

https://leetcode.cn/problems/valid-anagram/

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length() != t.length()) return false;
        int[] record = new int[26]; //26个小写字母，所以对应有26的长度存储数量即可     
        for(char c : s.toCharArray()) {
            record[c - 'a']++;
        }
        for(char c : t.toCharArray()) {
            record[c - 'a']--;
        }
        for(int r : record) {
            if(r != 0) return false;
        }
        return true;
    }
}
```



## 两个数组的交集

349easy 两个数组的交集 【构建set来做哈希表/构建数组做哈希表】

https://leetcode.cn/problems/intersection-of-two-arrays/

【Set做哈希表 94%】

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> set2 = new HashSet<>();
        for(int num : nums1) {
            set1.add(num);
        }
        for(int num : nums2) {
            if(set1.contains(num)) set2.add(num);
        }
        List<Integer> list = new ArrayList<>(set2); //将set转为list遍历输出
        int[] res = new int[list.size()];
        for(int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```

【数组做哈希表 98%】

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        int[] record = new int[1001];
        Set<Integer> set = new HashSet<>();
        for(int num : nums1) {
            record[num] = 1;
        }
        for(int num : nums2) {
            if(record[num] == 1) set.add(num);
        }
        List<Integer> list = new ArrayList<>(set); //将set转为list遍历输出
        int[] res = new int[list.size()];
        for(int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```

知识1：set转为list再遍历输出

```java
List<Integer> list = new ArrayList<>(set); //将set转为list遍历输出
```



350easy 两个数组的交集II 【构建map做哈希表】【短的数组放前面】

https://leetcode.cn/problems/intersection-of-two-arrays-ii/

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if(nums2.length < nums1.length) return intersect(nums2, nums1); //长度短的数组放前面
        int[] res = new int[nums1.length];
        int index = 0;
        Map<Integer, Integer> map = new HashMap<>();
        for(int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        for(int num : nums2) {
            int count = map.getOrDefault(num, 0);
            if(count > 0) {
                res[index++] = num;
                count--;
                if(count > 0) map.put(num, count);
                else map.remove(num);
            }
        }
        return Arrays.copyOfRange(res, 0, index); //复制数组范围[0, index)的内容并返回
    }
}
```



【简化版 ck】

```java 
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        if(nums1.length > nums2.length) return intersect(nums2, nums1);//长度短的数组放前面
        Map<Integer, Integer> map = new HashMap<>();
        for(int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        LinkedList<Integer> list = new LinkedList<>();
        for(int num : nums2) {
            if(map.containsKey(num) && map.get(num) > 0) {
                list.add(num);
                map.put(num, map.get(num) - 1);
            }
        }
        int[] res = new int[list.size()];
        for(int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```



## 快乐数

202easy 快乐数

https://leetcode.cn/problems/happy-number/

【构建set做哈希表，关键在于如何读取n的各个位数字】

【构造getNextNumber方法】【构造循环，当n==1或出现重复n在set里的时候，跳出循环】

```java
class Solution {
    public boolean isHappy(int n) {
        Set<Integer> set = new HashSet<>();
        //当n == 1或者添加到set里的数出现重复时，跳出循环
        while(n != 1 && !set.contains(n)) {
            set.add(n);
            n = getNExtNumber(n);
        }
        return n == 1;
    }
    private int getNExtNumber(int n) {
        int res = 0;
        while(n > 0) {
            int temp = n % 10; //取出数字n的最后一位
            res += temp * temp;
            n = n / 10; //保留n取出最后一位后的部分
        }
        return res;
    }
}
```



## 两数之和

1easy 两数之和 

https://leetcode.cn/problems/two-sum/

【构建map做哈希表】

【遍历的时候先求出diff，判断diff是否存在于map，如果存在，说明找到两个下标i和map.get(diff)，如果不存在，说明当前i坐标的差值没有被添加进去，此时再将nums[i]和i添加进map内】

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++) {
            int diff = target - nums[i];
            if(map.containsKey(diff)) return new int[]{i, map.get(diff)};
            map.put(nums[i], i); //如果diff不在map，再将nums[i]添加进map
        }
        return null;
    }
}
```



## 四数相加II

454mid 四数相加II 

https://leetcode.cn/problems/4sum-ii/

【构建map做哈希表】

【统计nums1,nums2两个数组中的元素之和，同时统计出现的次数，放入map】

【统计剩余的nums3,nums4两个数组的元素和，在map中找是否存在相加为0的情况，同时记录次数】

```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        Map<Integer, Integer> map = new HashMap<>();
        int res = 0;
        //统计nums1,nums2两个数组中的元素之和，同时统计出现的次数，放入map
        for(int i : nums1) {
            for(int j : nums2) {
                int temp = i + j;
                map.put(temp, map.getOrDefault(temp, 0) + 1);
            }
        }
        //统计剩余的两个元素的和，在map中找是否存在相加为0的情况，同时记录次数
        for(int i : nums3) {
            for(int j : nums4) {
                int diff = -i - j;
                res += map.getOrDefault(diff, 0);
            }
        }
        return res;
    }
}
```



## 赎金信

383easy 赎金信  【和“有效的字母异位词”基本一样】

https://leetcode.cn/problems/ransom-note/

【构建数组做哈希表，先存储长一点的字符串内字符，再校验短字符串字符】

字符串转换为字符数组：toCharArray()

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] mag = new int[26];
        for(char m : magazine.toCharArray()) {
            mag[m - 'a']++;
        }
        for(char r : ransomNote.toCharArray()) {
            mag[r - 'a']--;
        }
        for(int i : mag) {
            if(i < 0) return false;
        }
        return true;
    }
}
```



## 三数之和

15mid 三数之和 【双指针、去重】

【第一层遍历的k】【第二层遍历的i和j是双指针】

https://leetcode.cn/problems/3sum/

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums); //排序，从小到大
        List<List<Integer>> res = new ArrayList<>();
        for(int k = 0; k < nums.length - 2; k++) {
            if(nums[k] > 0) break; //剪枝，如果遍历到的数大于0，后续添加的数不可能和为0
            if(k > 0 && nums[k] == nums[k - 1]) continue; //去重，当前数和上一次遍历的相同
            int i = k + 1, j = nums.length - 1; //定义前后双指针
            while(i < j) {
                int sum = nums[k] + nums[i] + nums[j];
                if(sum < 0) {
                    i++;
                } else if(sum > 0) {
                    j--;
                } else {
                    res.add(Arrays.asList(nums[k], nums[i], nums[j]));
                    while(i < j && nums[i + 1] == nums[i]) i++; //双指针移动时去重
                    while(i < j && nums[j - 1] == nums[j]) j--; //双指针移动时去重
                    i++; //再走一步
                    j--; //再走一步
                }
            }
        }
        return res;
    }
}
```



## 四数之和

18mid 四数之和 

 https://leetcode.cn/problems/4sum/

【和三数之和基本一样】

【双指针，一级剪枝，一级去重，二级剪枝，二级去重】

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(nums);
        for(int i = 0; i < nums.length - 3; i++) {
            if(nums[i] > 0 && nums[i] >= target) break; //剪枝，nums[i] > 0不要漏
            if(i > 0 && nums[i] == nums[i - 1]) continue; //去重
            for(int j = i + 1; j < nums.length - 2; j++) {
                if(nums[j] > 0 && nums[j] >= target - nums[i]) break; //剪枝，nums[j] > 0不要漏
                if(j > i + 1 && nums[j] == nums[j - 1]) continue; //去重
                int left = j + 1, right = nums.length - 1;
                while(left < right) {
                    int sum = nums[i] + nums[j] + nums[left] + nums[right];
                    if(sum < target) left++;
                    else if(sum > target) right--;
                    else {
                        res.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        while(left < right && nums[left + 1] == nums[left]) left++;
                        while(left < right && nums[right - 1] == nums[right]) right--;
                        left++;
                        right--;
                    }
                }
            }
        }
        return res;
    }
}
```



# 字符串

【“反转字符串”、“反转字符串II”用双指针】

【“替换空格”用StringBuilder】

【“反转字符串中的单词 ”需要构造反转字符串方法、除多余空格方法、反转字符串里的单词方法】

【“左旋转字符串 ”需要构造反转字符串方法，先反转前n个，再反转剩余的，最后全部一起反转】

【“实现 strStr()”、“重复的字符串”用到KMP算法，求出next数组，表示每个位置的最长公共前后缀的长度】

## 反转字符串

344easy 反转字符串【双指针，首尾交换】

https://leetcode.cn/problems/reverse-string/

```java
class Solution {
    public void reverseString(char[] s) {
        int left = 0, right = s.length - 1;
        while(left < right) {
            char temp = s[right];
            s[right] = s[left];
            s[left] = temp;
            left++;
            right--;
        }        
    }
}
```



## 反转字符串II

541easy 反转字符串II 【循环步长为2k，构建一个反转字符串的方法再调用】

https://leetcode.cn/problems/reverse-string-ii/

```java
class Solution {
    public String reverseStr(String s, int k) {
        if(s.length() == 1) return s;
        char[] charS = s.toCharArray();
        for(int i = 0; i < charS.length; i += k * 2) {
            int len = charS.length - 1 - i + 1; //剩余字符数
            if(len >= k * 2) reverse(charS, i, i + k - 1); //剩余字符大于2k
            else if (len >= k && len < k * 2) reverse(charS, i, i + k - 1);//剩余字符数小于2k大于k
            else reverse(charS, i, charS.length - 1); //剩余字符少于k个
        }
        return new String(charS);
    }
    private void reverse(char[] charS, int left, int right) {
        while(left < right) {
            char temp = charS[right];
            charS[right] = charS[left];
            charS[left] = temp;
            left++;
            right--;
        }
    }
}
```

知识点：

return new String(ch)

## 替换空格

剑指offer05easy 替换空格 【使用StringBuilder】

https://leetcode.cn/problems/ti-huan-kong-ge-lcof/

```java
class Solution {
    public String replaceSpace(String s) {
        StringBuilder sb = new StringBuilder();
        for(Character c : s.toCharArray()) {
            if(c == ' ') sb.append("%20");
            else sb.append(c);
        }
        return sb.toString();
    }
}
```



## 反转字符串里的单词

151mid 反转字符串中的单词 

https://leetcode.cn/problems/reverse-words-in-a-string/

【//去掉所有不符合的空格，//反转所有字符，//反转每个单词】

```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = removeSpace(s);
        reverse(sb, 0, sb.length() - 1);
        reverseEachWord(sb, sb.length());
        return sb.toString();
    }
    //去除多余空格
    private StringBuilder removeSpace(String s) {
        StringBuilder sb = new StringBuilder();
        int start = 0, end = s.length() - 1;
        //去除首尾多余的空格，将start和end定位到字符首尾处
        while(s.charAt(start) == ' ') start++;
        while(s.charAt(end) == ' ') end--;
        //去除字符串中间的多余空格
        while(start <= end) {
            if(s.charAt(start) == ' ') { //当遍历到空格
                //如果sb上一个添加的不是空格，则此次加到sb
                if(sb.charAt(sb.length() - 1) != ' ') sb.append(s.charAt(start));                
            } else sb.append(s.charAt(start));//没有遍历到空格，直接加到sb
            start++;
        }
        return sb;
    }
    //反转字符串
    private void reverse(StringBuilder sb, int start, int end) {
        while(start < end) {
            Character temp = sb.charAt(end);
            sb.setCharAt(end, sb.charAt(start));
            sb.setCharAt(start, temp);
            start++;
            end--;
        }
    }
    //反转字符串内每一个单词，n指的是sb的长度
    private void reverseEachWord(StringBuilder sb, int n) {
        int start = 0, end = 1;
        while(start < n) {
            while(end < n && sb.charAt(end) != ' ') end++;
            reverse(sb, start, end - 1);
            start = end + 1;
            end = start + 1;
        }
    }
}
```

## 左旋转字符串

剑指 Offer 58easy - II. 左旋转字符串 

https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/

【构造一个反转字符串的方法】

【先反转前n个字符，再反转剩下的字符，最后反转整个字符串】

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder sb = new StringBuilder(s);
        reverse(sb, 0, n - 1);
        reverse(sb, n, sb.length() - 1);
        reverse(sb, 0, sb.length() - 1);
        return sb.toString();
    }
    private void reverse(StringBuilder sb, int start, int end) {
        while(start < end) {
            Character temp = sb.charAt(end);
            sb.setCharAt(end, sb.charAt(start));
            sb.setCharAt(start, temp);
            start++;
            end--;
        }
    }
}
```



【将字符串转成字符数组操作，更快】

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        char[] ch = s.toCharArray();
        reverse(ch, 0, n - 1);
        reverse(ch, n, ch.length - 1);
        reverse(ch, 0, ch.length - 1);
        return new String(ch);
    }
    void reverse(char[] ch, int left, int right) {
        while(left < right) {
            char tmp = ch[left];
            ch[left] = ch[right];
            ch[right] = tmp;
            left++;
            right--;
        }
    }
}
```



## 实现 strStr()

28mid 找出字符串中第一个匹配项的下标 

https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/

【最长相等前后缀】

【要在文本串haystack ：aabaabaafa 中查找是否出现过一个模式串needle：aabaaf。】

【前缀：指不包含最后一个字符的所有以第一个字符开头的连续子串。

后缀：指不包含第一个字符的所有以最后一个字符结尾的连续子串。】

计算前缀表：

<img src="E:\2021java\Typora\typora-user-images\KMP精讲8.png" alt="KMP精讲8" style="zoom:50%;" />

实现KMP算法，

1.先构造getNext()方法，通过模式串needle得出前缀表，即数组next[needle.length()] 

2.再根据前缀表遍历文本串

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int j = 0;
        int[] next = new int[needle.length()];
        getNext(next, needle);
        for(int i = 0; i < haystack.length(); i++) {
            while(j > 0 && haystack.charAt(i) != needle.charAt(j)) j = next[j - 1];
            if(haystack.charAt(i) == needle.charAt(j)) j++;
            if(j == needle.length()) return i - needle.length() + 1;
        }
        return -1;
    }
    private void getNext(int[] next, String needle) {
        int j = 0;
        next[0] = 0;
        for(int i = 1; i < needle.length(); i++) {
            while(j > 0 && needle.charAt(i) != needle.charAt(j)) j = next[j - 1];
            if(needle.charAt(i) == needle.charAt(j)) j++;
            next[i] = j;
        }
    }
}
```

## 重复的字符串

459easy 重复的子字符串

https://leetcode.cn/problems/repeated-substring-pattern/

【由重复子串组成的字符串中，最长相等前后缀不包含的子串就是最小重复子串】

1.先构造getNext()方法

2.数组长度减去最长相同前后缀的长度相当于是第一个周期的长度，也就是一个周期的长度，如果这个周期可以被整除，就说明整个数组就是这个周期的循环。

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int len = s.length();
        int[] next = new int[len];
        getNext(next, s);
        if(next[len - 1] != 0 && len % (len - next[len - 1]) == 0) return true;
        return false;
    }
    private void getNext(int[] next, String s) {
        int j = 0;
        next[0] = 0;
        for(int i = 1; i < s.length(); i++) {
            while(j > 0 && s.charAt(i) != s.charAt(j)) j = next[j - 1];
            if(s.charAt(i) == s.charAt(j)) j++;
            next[i] = j;
        }
    }
}
```

# 栈与队列

**知识**：Queue是单端队列，Deque是双端队列，两者都是继承于Collection，Deque是Queue的子接口

Queue常用子类是PriorityQueue；Deque常用子类是LinkedList以及ArrayDeque；

**ArrayDeque**可以作为栈或队列使用，但是栈的效率不如LinkedList高，通常作为**队列**使用。

**LinkedList**可以作为栈或队列使用，但是队列的效率不如ArrayQueue高，通常作为**栈**使用。

**ArrayDeque作为双端队列实现的常用方法**：

```java
//这四个函数只有返回值类型的区别，但更推荐使用offer系列，毕竟有返回值更好判断添加成不成功
public void addFirst(E e)		//
public void addLast(E e)		//
public boolean offerFirst(E e)	//
public boolean offerLast(E e)	//
    
//当deque为空时，remove系列会抛出NoSuchElementException的异常，poll系列会返回null
//推荐使用poll系列
public E removeFirst()
public E removeLast()
public E pollFirst()
public E pollLast()
    
//当deque为空时，使用get系列也会抛出NoSuchElementException的异常，peek系列只会返回null 
//推荐使用peek系列    
public E getFirst()
public E getLast()
public E peekFirst()
public E peekLast()  
    
//返回deque中的元素个数，前文已经使用过
public int size()    
    
//判断deque是否为空
public boolean isEmpty()

//判断deque中是否包含对象o，是则返回true，否则返回false
public boolean contains(Object o)    
```

**ArrayDeque作为普通队列实现的常用方法**：

```java
//这两个函数分别等价于addLast()、offerLast()，在队尾添加元素，e为null时二者都会抛出NullPointerException的异常
public boolean add(E e)
public boolean offer(E e)
    
//这两个函数分别等价于removeFirst()、poll()，在队首删除元素并返回出队的元素
//当队列为空时使用remove会抛出NoSuchElementException的异常，使用poll会返回null 
public E remove()
public E poll()

//这两个函数分别等价于getFirst()、peekFirst()，返回队首的元素，不会删除队首的元素
//当队列为空时使用element会抛出NoSuchElementException的异常，使用peek会返回null
public E element()
public E peek()   
    
//分别等价于addFirst()、removeFirst()，毕竟栈的出栈入栈都是在栈顶嘛，队首相当于栈顶
//这两个在ArrayDeque中一般用不到    
public void push(E e)
public E pop()    
```



## 用栈实现队列

232easy 用栈实现队列 

https://leetcode.cn/problems/implement-queue-using-stacks/

【用一个输入栈stackIn和一个输出栈stackOut模拟队列】

```java
class MyQueue {
    private Stack<Integer> stackIn; //输入栈
    private Stack<Integer> stackOut; //输出栈
    public MyQueue() {
        stackIn = new Stack<>();
        stackOut = new Stack<>();
    }
    //判断输出栈是否为空
    private void dumpStack(){
        if(!stackOut.isEmpty()) return; //如果输出栈不为空，则返回
        while(!stackIn.isEmpty()) stackOut.push(stackIn.pop());//输入栈的值全部转移到输出栈
    }
    
    public void push(int x) {
        stackIn.push(x); //往输入栈push
    }
    
    public int pop() {
        dumpStack();
        return stackOut.pop();
    }
    
    public int peek() {
        dumpStack();
        return stackOut.peek();
    }
    
    public boolean empty() {
        return stackIn.isEmpty() && stackOut.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```



## 用队列实现栈

225easy 用队列实现栈 

https://leetcode.cn/problems/implement-stack-using-queues/

【只能使用队列的基本操作 —— 也就是 `push to back`、`peek/pop from front`、`size` 和 `is empty` 这些操作。】

【不能使用双端队列的pollLast()】

【用一个队列que实现栈，push和队列基本一样，难点在于出栈。模拟栈的出栈，要把size-1个元素从队列弹出并添加到队尾，最后再把队列的首位元素弹出即可】【Deque的一些操作函数】

```java
class MyStack {
    private Deque<Integer> que;
    public MyStack() {
        que = new ArrayDeque<>();
    }
    
    public void push(int x) {  //插入操作一样
        que.addLast(x);
    }
    
    public int pop() {  //关键在于弹出
        int size = que.size();
        size--;
        while(size-- > 0) que.addLast(que.pollFirst()); //pollFirst为弹出首位元素，addLast为添加元素到队尾
        return que.pollFirst();
    }
    
    public int top() {  //获取栈顶元素即为获取队列尾部元素
        return que.peekLast(); //peekLast为获取队列尾部元素,peekFirst为获取队列首位元素
    }
    
    public boolean empty() {
        return que.isEmpty();
    }
}

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack obj = new MyStack();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.top();
 * boolean param_4 = obj.empty();
 */
```

## 有效的括号

20easy 有效的括号 【栈实现】

https://leetcode.cn/problems/valid-parentheses/

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for(int i = 0; i < s.length(); i++) {
            if(s.charAt(i) == '(') stack.push(')');
            else if(s.charAt(i) == '[') stack.push(']');
            else if(s.charAt(i) == '{') stack.push('}');
            else {
                if(!stack.isEmpty()) {
                    char c = stack.pop();
                    if(c != s.charAt(i)) return false;
                } else return false;                
            }
        }
        return stack.isEmpty();
    }
}
```



## 删除字符串中的所有相邻重复项

1047easy 删除字符串中的所有相邻重复项 

https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/

【和有效括号基本一样的思路，但是可以用StringBuilder替代栈去实现，同时定义一个top指针指向结果字符串的末尾下标】

【top指向字符串末尾相当于标记栈顶位置，res.deleteCharAt(top)相当于从栈顶弹出元素】

```java
class Solution {
    public String removeDuplicates(String s) {
        StringBuilder res = new StringBuilder();
        int top = -1; //top为res字符串的末尾下标
        for(Character c : s.toCharArray()) {
            if(top >= 0 && res.charAt(top) == c) res.deleteCharAt(top--);
            else {
                res.append(c);
                top++;
            }
        }
        return res.toString();
    }
}
```

## 逆波兰表达式求值

150mid 逆波兰表达式求值 

https://leetcode.cn/problems/evaluate-reverse-polish-notation/

【遇到数字就入栈，遇到符号就连续弹出两个栈顶元素（总能弹出两个）】

【先判断符号，最后else将数字入栈】

【特殊的后缀表达式，后续遍历（左、右、中)】

```java
class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for(String s : tokens) {
            if("+".equals(s)) {
                int num1 = stack.pop();
                int num2 = stack.pop();
                stack.push(num1 + num2);
            } else if("-".equals(s)) {
                int num1 = stack.pop();
                int num2 = stack.pop();
                stack.push(num2 - num1);
            } else if("*".equals(s)) {
                int num1 = stack.pop();
                int num2 = stack.pop();
                stack.push(num1 * num2);
            } else if("/".equals(s)) {
                int num1 = stack.pop();
                int num2 = stack.pop();
                stack.push(num2 / num1);
            } else stack.push(Integer.valueOf(s));
        }
        return stack.peek();
    }
}
```



## 滑动窗口最大值

239hard 滑动窗口最大值 

https://leetcode.cn/problems/sliding-window-maximum/

【大顶堆】

【构造双端队列，队列首部到尾部从大到小排列，每滑动一次窗口，就弹出一次最左边的值，即窗口的最大值】

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int len = nums.length;
        int[] res = new int[len - k + 1];
        int index = 0;
        MyDeque que = new MyDeque();
        for(int i = 0; i < len; i++) {
            if(i >= k)que.poll(nums[i - k]); //遍历到窗口的第四个值时，将窗口最左侧删掉，维持宽度为3
            que.add(nums[i]);
            if(i >= k - 1) res[index++] = que.peek();
        }
        return res;
    }
}
class MyDeque{
    Deque<Integer> que = new ArrayDeque<>();
    void add(int val) {
        while(!que.isEmpty() && que.peekLast() < val) que.pollLast();
        que.addLast(val);
    }
    void poll(int val){
        if(!que.isEmpty() && que.peekFirst() == val) que.pollFirst();
    }
    int peek(){
        return que.peekFirst();
    }
}
```

## 前K个高频元素

347mid 前K个高频元素 

https://leetcode.cn/problems/top-k-frequent-elements/

【小顶堆】

【队列元素，在首部到尾部，从小到大排列】【只维持长度为k的队列】

【1.用map存储相应数字出现的频率 2.使用优先队列存储map键值对，并使用小顶堆算法，当队列满k个键值对的时候，优先弹出频率最小的键值对（队列首部） 3.最后再将队列里的值存回数组并返回】

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        //优先队列，从小到大排序，就是小顶堆
        PriorityQueue<int[]> que = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        int[] res = new int[k];
        for(int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        for(Map.Entry<Integer, Integer> entry : map.entrySet()) {
            //只维持长度为k的队列
            if(que.size() < k) que.add(new int[]{entry.getKey(), entry.getValue()});
            else if(que.peek()[1] < entry.getValue()) {
                que.poll();
                que.add(new int[]{entry.getKey(), entry.getValue()});
            }
        }
        for(int i = 0; i < k; i++) {
            res[i] = que.poll()[0];
        }
        return res;
    }
}
```

优先队列 https://blog.csdn.net/ohwang/article/details/116934308

小到大排就是小顶堆，从大到小排就是大顶堆。

# 二叉树

【只有寻找某一条边（或者一个节点）的时候，递归函数会有bool类型的返回值】

【对于二叉搜索树的问题，通常用到pre指针，对前后数据进行对比】

## 二叉树理论基础

### 二叉树种类

满二叉树、完全二叉树、二叉搜索树、平衡二叉搜索树

#### 满二叉树

<img src="E:\2021java\Typora\typora-user-images\image-20221022163355835.png" alt="image-20221022163355835" style="zoom: 67%;" />



#### 完全二叉树

<img src="E:\2021java\Typora\typora-user-images\image-20221022163434673.png" alt="image-20221022163434673" style="zoom: 67%;" />

#### 二叉搜索树

<img src="E:\2021java\Typora\typora-user-images\image-20221022163513195.png" alt="image-20221022163513195" style="zoom: 67%;" />

#### 平衡二叉搜索树

<img src="E:\2021java\Typora\typora-user-images\image-20221022163548704.png" alt="image-20221022163548704" style="zoom: 67%;" />

### 二叉树的存储方式

链式存储：用指针

顺序存储：用数组

### 二叉树的遍历方式

**深度优先遍历**：先往深走，遇到叶子节点再往回走，包括：

前序遍历（递归法，迭代法）
中序遍历（递归法，迭代法）
后序遍历（递归法，迭代法）

**广度优先遍历**：一层一层的去遍历，包括：

层次遍历（迭代法）

### 二叉树的定义

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    public TreeNode(){}
    public TreeNode(int val) {
        this.val = val;
    }
    public TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

## 二叉树的递归遍历

【注意传递参数、递归终止条件、下探】

144easy 二叉树的前序遍历 【递归和迭代都要掌握】

https://leetcode.cn/problems/binary-tree-preorder-traversal/

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        preorder(root, list);
        return list;
    }
    void preorder(TreeNode node, List<Integer> list) {
        if(node == null) return;
        list.add(node.val);
        preorder(node.left, list);
        preorder(node.right, list);
    }
}
```

94easy 二叉树的中序遍历

https://leetcode.cn/problems/binary-tree-inorder-traversal/

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        inorder(root, list);
        return list;
    }
    void inorder(TreeNode node, List<Integer> list) {
        if(node == null) return;
        inorder(node.left, list);
        list.add(node.val);
        inorder(node.right, list);
    }
}
```

145easy 二叉树的后序遍历

https://leetcode.cn/problems/binary-tree-postorder-traversal/

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        postorder(root, list);
        return list;
    }
    void postorder(TreeNode node, List<Integer> list) {
        if(node == null) return;
        postorder(node.left, list);
        postorder(node.right, list);
        list.add(node.val);
    }
}
```

589easy N叉树的前序遍历

590easy N叉树的后序遍历

## 二叉树的迭代遍历

144easy 二叉树的前序遍历 

https://leetcode.cn/problems/binary-tree-preorder-traversal/

【栈来实现，存储当前遍历到的节点，然后先压入右子节点，再压入左子节点】

【每次弹出栈顶元素，都判断这个节点有无左右子节点，如果有，就先压入右子节点，再压入左子节点】

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {        
        List<Integer> list = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        if(root == null) return list;
        stack.push(root);
        while(!stack.isEmpty()) {
            TreeNode node = stack.pop(); //弹出栈顶元素
            list.add(node.val);
            if(node.right != null) stack.push(node.right); //压入弹出节点的右子节点
            if(node.left != null) stack.push(node.left); //压入弹出节点的左子节点
        }
        return list;
    }
}
```

145easy 二叉树的后序遍历 

https://leetcode.cn/problems/binary-tree-postorder-traversal/

【栈来实现，存储当前遍历到的节点，然后先压入左子节点，再压入右子节点，最后反转整个链表】

```java
class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root == null) return list;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while(!stack.isEmpty()) { //入栈：中左右，出栈：中右左，最后反转为：左右中，即为后序遍历
            TreeNode node = stack.pop();
            list.add(node.val);
            if(node.left != null) stack.push(node.left);
            if(node.right != null) stack.push(node.right);
        }
        Collections.reverse(list);
        return list;
    }
}
```

94easy 二叉树的中序遍历 

https://leetcode.cn/problems/binary-tree-inorder-traversal/

【只要指针cur不为空，就放进stack里；当cur为空时，就将栈内元素弹出放进res里】

【迭代方法，中序和前后序不同，需要用，用指针cur遍历节点（向左），stack存储遍历过的节点，当cur为空时，弹出stack的节点并将cur指向这个弹出的节点，将这个节点值存入结果数组里，然后cur向右下探】

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root == null) return list;
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.isEmpty()) {
            if(cur != null) {                
                stack.push(cur); //添加当前节点
                cur = cur.left; //节点向左子节点下探，无论是否为null
            } else {
                cur = stack.pop(); //当前节点为null，说明栈不为空，弹出栈顶元素
                list.add(cur.val); //添加栈顶元素值
                cur = cur.right; //节点向右子节点下探，无论是否为null
            }
        }
        return list;
    }
}
```

## 二叉树的层序遍历

102mid 二叉树的层序遍历 

https://leetcode.cn/problems/binary-tree-level-order-traversal/

【用队列保存每层的元素，用size记录该层节点数量，然后把size个节点添加到itemList，再遍历下一层】

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> resList = new ArrayList<>();
        if(root == null) return resList;
        Deque<TreeNode> que = new ArrayDeque<>();
        que.addLast(root);        
        while(!que.isEmpty()) {
            int size = que.size();
            List<Integer> itemList = new ArrayList<>();
            while(size-- > 0) {
                TreeNode node = que.pollFirst();
                itemList.add(node.val);
                if(node.left != null) que.addLast(node.left);
                if(node.right != null) que.addLast(node.right);
            }
            resList.add(itemList);
        }
        return resList;
    }
}
```

107mid 二叉树的层序遍历II 【和102一样，最后加个Collections.reverse(resList);】

199mid 二叉树的右视图 【和102基本一样，将每层的itemList的最后一个元素（最右边）添加进res结果集里】

637easy 二叉树的层平均值 【和102基本一样，求出每层节点值的和的平均值】

429mid N 叉树的层序遍历 【和102基本一样，但每个节点不止是左右节点，而是可能有多个节点】

104easy 二叉树的最大深度 【和102基本一样，记录层数即可】

111easy 二叉树的最小深度 【和104基本一样，当左右子节点均为null的时候，直接返回res，否则继续下探】

## 翻转二叉树

226easy 翻转二叉树 【遍历到当前节点时，交换该节点的左右子节点】

https://leetcode.cn/problems/invert-binary-tree/

【ck 前序/后续遍历+递归】

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root == null) return null;
        TreeNode leftNode = invertTree(root.left);
        TreeNode rightNode = invertTree(root.right);
        root.left = rightNode;
        root.right = leftNode;
        return root;
    }
}
```

【前序/后续遍历+递归】

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root == null) return root;
        //递归，前序遍历
        swapChildren(root); //中
        invertTree(root.left); //左 
        invertTree(root.right); //右
        return root;       
    }
    private void swapChildren(TreeNode node) {
        TreeNode temp = node.left;
        node.left = node.right;
        node.right = temp;
    }
}
```

【层序遍历】

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root == null) return root;
        //层序遍历
         Deque<TreeNode> que = new ArrayDeque<>();
         que.addLast(root);
         while(!que.isEmpty()) {
             int size = que.size();
             while(size-- > 0) {
                 TreeNode node = que.pollFirst();
                 swapChildren(node);
                 if(node.left != null) que.addLast(node.left);
                 if(node.right != null) que.addLast(node.right);
             }
         }
         return root;
    }
    private void swapChildren(TreeNode node) {
        TreeNode temp = node.left;
        node.left = node.right;
        node.right = temp;
    }
}
```

## 对称二叉树

101easy 对称二叉树 

https://leetcode.cn/problems/symmetric-tree/

【后序遍历+递归（只能用后序），比较传入节点的左右子节点，再比较左右子节点的内侧/外侧子节点】

<img src="E:\2021java\Typora\typora-user-images\20210203144624414.png" alt="101. 对称二叉树1" style="zoom:50%;" />

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return compare(root.left, root.right);
    }
    private boolean compare(TreeNode left, TreeNode right){
        if(left == null && right != null) return false;
        if(left != null && right == null) return false;
        if(left == null && right == null) return true;
        if(left.val != right.val) return false; //这行和上三行顺序不能换，因为先判断完非空
        boolean outside = compare(left.left, right.right);
        boolean inside = compare(left.right, right.left);
        return outside && inside;
    }
}
```

100easy 相同的树 

https://leetcode.cn/problems/same-tree/

【和101基本一样，但比较的是左右子节点的同侧子节点的值】

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        return compare(p, q);
    }
    boolean compare(TreeNode left, TreeNode right) {
        if(left == null && right != null) return false;
        if(left != null && right == null) return false;
        if(left == null && right == null) return true;
        if(left.val != right.val) return false;
        boolean leftside = compare(left.left, right.left);
        boolean rightside = compare(left.right, right.right);
        return leftside && rightside;
    }
}
```



## 二叉树的最大深度

104easy 二叉树的最大深度

https://leetcode.cn/problems/maximum-depth-of-binary-tree/

【后序遍历+递归（快）】

【自下往上求高度，用后序遍历+递归，求出来的根节点的高度即为树的最大深度】

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) return 0;
        //后序遍历+递归
        int leftHeight = maxDepth(root.left);  //左
        int rightHeight = maxDepth(root.right); //右
        int height = Math.max(leftHeight, rightHeight) + 1; //中
        return height;
    }
}
```

【层序遍历求深度（慢）】

```java
class Solution {
    public int maxDepth(TreeNode root) {        
        if(root == null) return 0;
        int res = 0;
        Deque<TreeNode> que = new ArrayDeque<>();
        que.addLast(root);
        while(!que.isEmpty()) {
            int size = que.size();
            while(size-- > 0) { //遍历完当前层
                TreeNode node = que.pollFirst();
                if(node.left != null) que.addLast(node.left);
                if(node.right != null) que.addLast(node.right);
            }
            res++;
        }
        return res;
    }
}
```

559easy N 叉树的最大深度

https://leetcode.cn/problems/maximum-depth-of-n-ary-tree/

【递归，更简单做法】

```java
class Solution {
    public int maxDepth(Node root) {
        if(root == null) return 0;
        List<Node> children = root.children;
        int maxRes = 0;
        for(int i = 0; i < children.size(); i++) {
            maxRes = Math.max(maxRes, maxDepth(children.get(i)));
        }
        return maxRes + 1;
    }
}
```

【递归，和104基本一样】

```java
class Solution {
    public int maxDepth(Node root) {
        if(root == null) return 0;
        //后序遍历+递归
        int[] heights = new int[root.children.size()];
        int maxHeight = 0;
        //遍历多个子节点（左、右）
        for(int i = 0; i < root.children.size(); i++) {
            heights[i] = maxDepth(root.children.get(i));
        }
        //找出多个子节点的最大深度
        for(int height : heights) {
            maxHeight = Math.max(maxHeight, height);
        }
        return maxHeight + 1; //中
    }
}
```

## 二叉树的最小深度

111easy 二叉树的最小深度

https://leetcode.cn/problems/minimum-depth-of-binary-tree/

【和104基本一样，递归+后序遍历，但要判断节点的子节点是否为null，如果是空，则不返回该节点的高度】

<img src="E:\2021java\Typora\typora-user-images\20210203155800503.png" alt="111.二叉树的最小深度" style="zoom:50%;" />

【后序遍历+递归（慢）】

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) return 0;
        //递归+后序遍历
        int leftHeight = minDepth(root.left); //左
        int rightHeight = minDepth(root.right); //右
        //判断节点的子节点是否为null，如果是一侧null，则返回另一侧的高度
        if(root.left == null && root.right != null) return rightHeight + 1;
        if(root.left != null && root.right == null) return leftHeight + 1;
        int height = Math.min(leftHeight, rightHeight) + 1;
        return height;
    }
}
```

【层序遍历, 快】

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) return 0;
        int res = 0;
        Deque<TreeNode> que = new ArrayDeque<>();
        que.addLast(root);
        while(!que.isEmpty()) {
            int size = que.size();
            res++; //遇到新的一层就+1
            while(size-- > 0) {
                TreeNode node = que.pollFirst();
                if(node.left == null && node.right == null) return res;
                if(node.left != null) que.addLast(node.left);
                if(node.right != null) que.addLast(node.right);
            }
        }
        return res;
    }
}
```

## 完全二叉树的节点个数

222mid 完全二叉树的节点个数

https://leetcode.cn/problems/count-complete-tree-nodes/

完全二叉树只有两种情况，情况一：就是满二叉树，情况二：最后一层叶子节点没有满。

【对于情况一，可以直接用 2^树深度 - 1 来计算，注意这里根节点深度为1】

【对于情况二，分别递归左孩子，和右孩子，递归到某一深度一定会有左孩子或者右孩子为满二叉树，然后依然可以按照情况1来计算】

移位运算：

1 << 2  // 4, 即 2的2次方
1 << 10 // 1024, 即 2的10次方

2 << leftDepth 表示 2乘(2的leftDepth次方)

【方法一：层序遍历/递归】

【方法二：利用完全二叉树的特点，计算满二叉树的节点数，再传递给上一个根节点（后序遍历）】

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null) return 0;        
        TreeNode leftNode = root.left;
        TreeNode rightNode = root.right;
        int leftDepth = 0, rightDepth = 0;
        while(leftNode != null) {
            leftDepth++;
            leftNode = leftNode.left;
        }
        while(rightNode != null) {
            rightDepth++;
            rightNode = rightNode.right;
        }
        if(leftDepth == rightDepth) {
            int depth = leftDepth + 1; //当前节点为根节点的满二叉树的节点总数
            return (1 << depth) - 1; //2^树深度 - 1
        }
        int leftCount = countNodes(root.left);
        int rightCount = countNodes(root.right);
        return leftCount + rightCount + 1;
    }
}
```

【方法三：当成普通二叉树去递归，后序遍历】

```java
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null) return 0;
        int leftSum = countNodes(root.left);
        int rightSum = countNodes(root.right);
        return leftSum + rightSum + 1;
    }
}
```



## 平衡二叉树

110easy 平衡二叉树

https://leetcode.cn/problems/balanced-binary-tree/

【递归+后序遍历，求出左右子树高度差，若大于1，则返回-1】

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if(root == null) return true;
        int res = getHeight(root);
        return res != -1;
    }
    private int getHeight(TreeNode node) {
        if(node == null) return 0;
        int leftHeight = getHeight(node.left);
        if (leftHeight == -1) return -1;
        
        int rightHeight = getHeight(node.right);
        if (rightHeight == -1) return -1;
        
        if (Math.abs(leftHeight - rightHeight) > 1) return -1;
        int res = Math.max(leftHeight, rightHeight) + 1;
        return res;
    }
}
```

## 二叉树的所有路径

257easy 二叉树的所有路径

https://leetcode.cn/problems/binary-tree-paths/

【从根节点到叶子的路径，所以需要前序遍历，这样才方便让父节点指向孩子节点，找到对应的路径】

【递归+前序遍历+回溯】

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if(root == null) return res;
        List<Integer> path = new ArrayList<>();
        traversal(root, path, res);
        return res;
    }
    //递归+前序遍历
    private void traversal(TreeNode node, List<Integer> path, List<String> res) {
        path.add(node.val); //中
        //终止条件：遍历到叶子节点
        if (node.left == null && node.right == null) {
            StringBuilder sb = new StringBuilder();
            for(int i = 0; i < path.size() - 1; i++) {
                sb.append(path.get(i)).append("->");
            }
            sb.append(path.get(path.size() - 1));
            res.add(sb.toString());
        }
        //左
        if(node.left != null) {
            traversal(node.left, path, res);
            path.remove(path.size() - 1); //回溯
        }
        //右
        if(node.right != null) {
            traversal(node.right, path, res);
            path.remove(path.size() - 1); //回溯
        }
    }
}
```

## 左叶子之和

404easy 左叶子之和 

https://leetcode.cn/problems/sum-of-left-leaves/

【其实也没有对“中”的操作，就是把leftValue和rightValue相加并返回】

【后序遍历 + 递归，遍历到叶子结点的父节点就开始判断】

【只有当前遍历的节点是父节点，才能判断其子节点是不是左叶子。 所以如果当前遍历的节点是叶子节点，那其左叶子也必定是0】

<img src="E:\2021java\Typora\typora-user-images\20220902165805.png" alt="图二" style="zoom:50%;" />



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if(root == null) return 0;
        if(root.left == null && root.right == null) return 0;
        int leftValue = 0;
        int rightValue = sumOfLeftLeaves(root.right);
        //如果当前节点的左子节点是左叶子，则取当前的值
        if(root.left != null && root.left.left == null && root.left.right == null) {
            leftValue = root.left.val;
        } else { //如果当前节点的左子节点不是是左叶子，则继续向左下探
            leftValue = sumOfLeftLeaves(root.left);
        }
        return rightValue + leftValue;
    }
}
```

## 找树左下角的值

513mid 找树左下角的值 

https://leetcode.cn/problems/find-bottom-left-tree-value/

【确保先遍历左子节点，再遍历右子节点】

【递归+前序遍历/后序遍历/中序遍历+回溯，都一样，因为没有对“中”的操作。】

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int res = 0;
    int maxDepth = Integer.MIN_VALUE;
    public int findBottomLeftValue(TreeNode root) {
        backtracking(root, 1);
        return res;
    }
    void backtracking(TreeNode node, int depth) {
        //终止条件
        if(node.left == null && node.right == null) {
            if(depth > maxDepth) { //若此结点深度最大
                maxDepth = depth;
                res = node.val;
            }
        }
        //确保先遍历左子节点，再遍历右子节点
        if(node.left != null) {
            depth++;
            backtracking(node.left, depth);
            depth--; //回溯
        }
        if(node.right != null) {
            depth++;
            backtracking(node.right, depth);
            depth--; //回溯
        }
    }
}
```

## 路径总和

112easy 路径总和 

https://leetcode.cn/problems/path-sum/

【递归+回溯】

【思路版 ck】

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if(root == null) return false;
        int sum =  root.val;
        return backtracking(root, targetSum, sum);
    }
    boolean backtracking(TreeNode node, int targetSum, int sum) {
        //终止条件，到叶子结点了
        if(node.left == null && node.right == null) return sum == targetSum;
        if(node.left != null) { //左
            sum += node.left.val;
            boolean left = backtracking(node.left, targetSum, sum);
            if(left) return true;
            sum -= node.left.val; //回溯
        }
        if(node.right != null) { //右
            sum += node.right.val;
            boolean right = backtracking(node.right, targetSum, sum);
            if(right) return true;
            sum -= node.right.val; //回溯
        }
        return false;
    }
}
```

【简洁版（隐藏了回溯）】

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if(root == null) return false;
        targetSum -= root.val; //下探之后再进行减的操作
        if(root.left == null && root.right == null) return targetSum == 0;
        //左
        boolean left = hasPathSum(root.left, targetSum);
        if(left) return true;
        //右
        boolean right = hasPathSum(root.right, targetSum);
        if(right) return right;
        return false;
    }
}
```

113mid 路径总和II

【和112基本一样，但遇到符合条件的路径时（遍历到叶子结点），不能直接返回，而是添加path进res】

```java
class Solution {
    List<List<Integer>> res;
    LinkedList<Integer> path;
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
        res = new ArrayList<>();
        path = new LinkedList<>();
        traversal(root, targetSum);
        return res;
    }
    private void traversal(TreeNode root, int targetSum) {
        if(root == null) return;
        path.offer(root.val);
        targetSum -= root.val; //因为只在处理当下遍历的节点的时候作减值操作，所以这一步不需要回溯
        if(root.left == null && root.right == null && targetSum == 0) res.add(new LinkedList<>(path));
        traversal(root.left, targetSum);
        traversal(root.right, targetSum);
        path.removeLast(); //回溯
    }
}
```

## 从中序与后序遍历序列构造二叉树

106mid 从中序与后序遍历序列构造二叉树 

https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/

【递归：1.从每次传入的后序数组取最后一位作为子树根节点 2.找出后续数组最后一位在中序数组的下标(分割中序数组) 3.//下探到左右子节点】

<img src="E:\2021java\Typora\typora-user-images\20210203154249860.png" alt="106.从中序与后序遍历序列构造二叉树" style="zoom:50%;" />

```java
class Solution {
    Map<Integer, Integer> map;
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        map = new HashMap<>();
        for(int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return findNode(inorder, 0, inorder.length, postorder, 0, postorder.length);
    }
    //[inBegin, inEnd) [postBegin, postEnd) 左闭右开
    TreeNode findNode(int[] inorder, int inBegin, int inEnd, int[] postorder, int postBegin, int postEnd) {
        if(inBegin >= inEnd || postBegin >= postEnd) return null; //空节点
        //先从postorder数组末尾找到根节点
        TreeNode root = new TreeNode(postorder[postEnd - 1]);
        //再在inorder中找到根节点的下标
        int rootIndex = map.get(root.val);
        //在inorder中算出左子树数组的长度
        int leftLen = rootIndex - inBegin;
        //构造-指向左子树
        root.left = findNode(inorder, inBegin, rootIndex, postorder, postBegin, postBegin + leftLen);
        //构造-指向右子树
        root.right = findNode(inorder, rootIndex + 1, inEnd, postorder, postBegin + leftLen, postEnd - 1);
        return root;
    }
}
```

## 最大二叉树

654mid 最大二叉树

https://leetcode.cn/problems/maximum-binary-tree/

【和“从中序与后序遍历序列构造二叉树 ”类似，都是寻找根节点的位置作为分割点】

【凡是构造二叉树的问题都要用前序遍历】

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return findNode(nums, 0, nums.length);
    }
    //[left, right) 左闭右开
    TreeNode findNode(int[] nums, int left, int right) {
        if(left >= right) return null; //空节点
        int maxValue = nums[left]; //初始化数组最大元素
        int maxIndex = left; //初始化数组最大元素对应的下标
        for(int i = left; i < right; i++) {
            if(nums[i] > maxValue) {
                maxValue = nums[i];
                maxIndex = i;
            }
        }
        //初始化根节点
        TreeNode root = new TreeNode(maxValue);
        //指向左子树
        root.left = findNode(nums, left, maxIndex);
        //指向右子树
        root.right = findNode(nums, maxIndex + 1, right);
        return root;
    }
}
```

## 合并二叉树

617easy 合并二叉树 

https://leetcode.cn/problems/merge-two-binary-trees/

【前序遍历】【直接操作第一棵树，改变他的值】

```java
class Solution {
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
        //终止条件
        if(root1 == null) return root2;
        if(root2 == null) return root1;
        //中
        root1.val += root2.val;
        //左
        root1.left = mergeTrees(root1.left, root2.left);
        //右
        root1.right = mergeTrees(root1.right, root2.right);
        return root1;
    }
}
```

## 二叉搜索树中的搜索

700easy 二叉搜索树中的搜索

https://leetcode.cn/problems/search-in-a-binary-search-tree/

【递归+二叉搜索树的特性】

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        if(root == null) return null;
        if(val < root.val) return searchBST(root.left, val); //左
        if(val > root.val) return searchBST(root.right, val); //右
        return root; //中
    }
}
```

【迭代+二叉搜索树的特性】

```java
class Solution {
    public TreeNode searchBST(TreeNode root, int val) {
        while(root != null) {
            if(val < root.val) root = root.left;
            else if(val > root.val) root = root.right;
            else return root;
        }
        return null;
    }
}
```

## 验证二叉搜索树

98mid 验证二叉搜索树 

https://leetcode.cn/problems/validate-binary-search-tree/

【中序遍历下，输出的二叉搜索树节点的数值是有序序列】

【判断当前遍历到的节点值root.val是否比上一个节点值pre.val大，如果大，说明可以继续，否则返回false】

【递归+中序遍历，比较前后遍历的节点的值是否递增】

```java
class Solution {
    TreeNode pre;
    public boolean isValidBST(TreeNode root) {
        //终止条件，空节点也是二叉搜索树
        if(root == null) return true;
        //左
        boolean left = isValidBST(root.left);
        //中
        if(pre != null && pre.val >= root.val) return false;//只要找到一个不符合，就返回false
        else pre = root;
        //右
        boolean right = isValidBST(root.right);
        return left && right;
    }
}
```

## 二叉搜索树的最小绝对差

530easy 二叉搜索树的最小绝对差 

https://leetcode.cn/problems/minimum-absolute-difference-in-bst/

【和“验证二叉搜索树”类似，采用中序遍历，比较相邻两个节点值的差值】

【pre = node;  //不能用else】

【递归+中序遍历】

```java
class Solution {
    TreeNode pre;
    int res = Integer.MAX_VALUE;
    public int getMinimumDifference(TreeNode root) {
        backtracking(root);
        return res;
    }
    void backtracking(TreeNode node) {
        if(node == null) return;
        //左
        backtracking(node.left);
        //中
        if(pre != null) {
            int diff = Math.abs(node.val - pre.val);
            res = Math.min(res, diff);
        } 
        pre = node;  //不能用else
        //右
        backtracking(node.right);
    }
}
```

## 二叉搜索树中的众数

501easy 二叉搜索树中的众数 

https://leetcode.cn/problems/find-mode-in-binary-search-tree/

【中序遍历+递归，在‘中’的环节，处理res结果集】

```java
class Solution {
    List<Integer> res;
    TreeNode pre = null;
    int count = 0; //用来记录当次遍历到的节点的出现次数
    int maxCount = 0; //出现过的最大的次数
    public int[] findMode(TreeNode root) {
        res = new ArrayList<>();
        traversal(root);
        int[] nodes = new int[res.size()];
        for(int i = 0; i < res.size(); i++) {
            nodes[i] = res.get(i);
        }
        return nodes;
    }
    void traversal(TreeNode cur) {
        //终止条件
        if(cur == null) return;
        //左
        traversal(cur.left);
        //中
        if(pre == null) count = 1; //说明当前cur遍历到第一个节点
        else if(pre.val == cur.val) count++;//当前节点值和上一个节点值相等
        else count = 1; //cur指向的节点和上一个不一样，count重置为1
        if(count == maxCount) res.add(cur.val); //更新res
        if(count > maxCount) { //更新res
            maxCount = count;
            res.clear();
            res.add(cur.val);
        }
        pre = cur; //移动pre指针
        //右
        traversal(cur.right);
    }
}
```

## 二叉树的最近公共祖先

236mid 二叉树的最近公共祖先

https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/

【涉及到回溯，要用后序遍历，左-》右-》中】

<img src="E:\2021java\Typora\typora-user-images\20210204151125844.png" alt="236.二叉树的最近公共祖先1" style="zoom:67%;" />

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null) return null;
        if(root.val == p.val || root.val == q.val) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q); //左
        TreeNode right = lowestCommonAncestor(root.right, p, q); //右
        //中
        if(left != null && right != null) return root; 
        if(left != null && right == null) return left;
        if(left == null && right != null) return right;
        return null;
    }
}
```

## 二叉搜索树的最近公共祖先

235mid 二叉搜索树的最近公共祖先

https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/

【递归+后序遍历（利用二叉搜索树自带的遍历顺序）】

【当我们从上向下去递归遍历，第一次遇到 cur节点是数值在[p, q]区间中，那么cur就是 p和q的最近公共祖先】

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //终止条件
        if(root == null) return null;
        //如果p,q的节点值均小于当前遍历的节点值，说明公共祖先在左子树
        if(p.val < root.val && q.val < root.val) {
            TreeNode left = lowestCommonAncestor(root.left, p, q);
            return left;
        }
        //如果p,q的节点值均大于当前遍历的节点值，说明公共祖先在右子树
        if(p.val > root.val && q.val > root.val) {
            TreeNode right = lowestCommonAncestor(root.right, p, q);
            return right;
        }
        //当前遍历的节点值介于p,q节点值之间，当前节点就是公共祖先
        return root;
    }
}
```

【迭代法】

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while(root != null) {
            if(root.val > p.val && root.val > q.val) root = root.left;
            else if(root.val < p.val && root.val < q.val) root = root.right;
            else return root;
        }
        return null;
    }
}
```

## 二叉搜索树中的插入操作

701mid 二叉搜索树中的插入操作

https://leetcode.cn/problems/insert-into-a-binary-search-tree/

【将值val插入叶子结点即可，递归 + 后序遍历】

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if(root == null) { //遇到叶子结点
            TreeNode node = new TreeNode(val);
            return node;
        }
        if(val < root.val) root.left = insertIntoBST(root.left, val); //明确左指向
        if(val > root.val) root.right = insertIntoBST(root.right, val); //明确右指向
        return root;
    }
}
```

【迭代法 ck】

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if(root == null) return new TreeNode(val);
        TreeNode cur = root;
        while(cur != null) {
            if(val < cur.val) {
                if(cur.left == null) {
                    cur.left = new TreeNode(val);
                    return root;
                }
                cur = cur.left;
            } else if(val > cur.val) {
                if(cur.right == null) {
                    cur.right = new TreeNode(val);
                    return root;
                }
                cur = cur.right;
            }
        }
        return null;
    }
}
```



## 删除二叉搜索树中的节点

450mid 删除二叉搜索树中的节点

https://leetcode.cn/problems/delete-node-in-a-bst/

【1.没找到删除的点 2.叶子结点，左右为空 3.左不空，右空 4.左空，右不空 5.左不空，右不空】

对于【5.左不空，右不空】的情况，如图所示

```java
//非叶子结点，且左、右子节点均不为空
            if(root.left != null && root.right != null) {
                TreeNode cur = root.right;
                while(cur.left != null) {
                    cur = cur.left;
                }
                cur.left = root.left;
                return root.right;
            }    
```

<img src="E:\2021java\Typora\typora-user-images\image-20221203203759658.png" alt="image-20221203203759658" style="zoom:50%;" />

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null) return null;
        if(root.val == key) {
            //叶子节点
            if(root.left == null && root.right == null) return null;
            //非叶子结点，但右子节点为空
            if(root.left != null && root.right == null) return root.left;
            //非叶子结点，但左子节点为空
            if(root.left == null && root.right != null) return root.right;
            //非叶子结点，且左、右子节点均不为空
            if(root.left != null && root.right != null) {
                TreeNode cur = root.right;
                while(cur.left != null) {
                    cur = cur.left;
                }
                cur.left = root.left;
                return root.right;
            }            
        }
        if(key < root.val) root.left = deleteNode(root.left, key);
        if(key > root.val) root.right = deleteNode(root.right, key);
        return root;
    }
}
```

## 修剪二叉搜索树

669mid 修剪二叉搜索树 

https://leetcode.cn/problems/trim-a-binary-search-tree/

【递归，1.遍历到叶子结点 2.遍历到节点值小于low 3.遍历到节点值大于high】

```java
class Solution {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if(root == null) return null;
        //当前节点值小于low
        if(root.val < low) return trimBST(root.right, low, high);
        //当前节点值大于high
        if(root.val > high) return trimBST(root.left,  low, high);
        //当前节点值在范围内
        root.left = trimBST(root.left, low, high); //左
        root.right = trimBST(root.right, low, high); //右
        return root;
    }
}
```

## 将有序数组转换为二叉搜索树

108easy 将有序数组转换为二叉搜索树 

https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/

【要求高度平衡，即从数组中间开始定义根节点】

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return traversal(nums, 0, nums.length - 1);
    }
    private TreeNode traversal(int[] nums, int left, int right) {
        if(left > right) return null; //终止条件
        int mid = left + (right - left) / 2; //找到数组中间位置的坐标
        TreeNode root = new TreeNode(nums[mid]); //将数组中间的元素定义为根节点
        root.left = traversal(nums, left, mid - 1); //定义根节点的左指向
        root.right = traversal(nums, mid + 1, right); //定义根节点的右指向
        return root;
    }
}
```

## 把二叉搜索树转换为累加树

538mid 把二叉搜索树转换为累加树 

https://leetcode.cn/problems/convert-bst-to-greater-tree/

【定义pre指针】

【递归，右中左的遍历方式，倒序叠加并赋值】

```java
class Solution {
    int pre = 0;
    public TreeNode convertBST(TreeNode root) {      
        traversal(root);
        return root;
    }
    void traversal(TreeNode node) {
        if(node == null) return; //终止条件
        traversal(node.right); //右
        node.val += pre; //更新当前节点值
        pre = node.val; //更新pre值
        traversal(node.left); //左
    }
}
```

# 回溯算法

**横向去重（可排序/不可排序）：都可以用set，如果可以排序，用used更好**

90mid 子集II 、491mid 递增子序列 、47mid 全排列II 

**纵向去重（可排序/不可排序）：用used**

46mid 全排列 、47mid 全排列II 

## 回溯算法理论基础

![回溯算法理论基础](https://img-blog.csdnimg.cn/20210130173631174.png)

基本模板

```java
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```



## 组合问题

77mid 组合 

https://leetcode.cn/problems/combinations/

【1.确立终止条件 2.每一层是for，向下是递归】

![77.组合](E:\2021java\Typora\typora-user-images\20201123195223940.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> combine(int n, int k) {
        backtracking(n, k, 1);
        return res;
    }
    private void backtracking(int n, int k, int startIndex) {
        //终止条件
        if(path.size() == k) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = startIndex; i <= n; i++) {
            path.add(i);
            backtracking(n, k, i + 1);
            path.removeLast();
        }
    }
}
```

## 组合（优化）

77mid 组合 【1.确立终止条件 2.每一层是for，向下是递归 3.剪枝操作】 

剪枝操作，n - (k - path.size()) + 1表示最多从这里开始，例如n=4,k=3，当path的长度为0，即还没取值的时候，

n - (k - path.size()) + 1 = 4 - （3 - 0）+ 1 = 2，即最多从2开始，可以取到【2,3,4】满足条件。

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> combine(int n, int k) {
        backtracking(n, k, 1);
        return res;
    }
    private void backtracking(int n, int k, int startIndex) {
        //终止条件
        if(path.size() == k) {
            res.add(new ArrayList<>(path));
            return;
        }
        //i <= n - (k - path.size()) + 1为剪枝
        for(int i = startIndex; i <= n - (k - path.size()) + 1; i++) {
            path.add(i);
            backtracking(n, k, i + 1);
            path.removeLast();
        }
    }
}
```

## 组合总和III

216mid 组合总和III 

https://leetcode.cn/problems/combination-sum-iii/

【1.确立终止条件 2.每一层是for，向下是递归 3.剪枝操作】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum3(int k, int n) {
        backtracking(k, n, 1);
        return res;
    }
    void backtracking(int k, int n, int startIndex) {
        if(sum > n) return; //剪枝
        if(path.size() == k && sum == n) { //终止条件
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = startIndex; i <= 9; i++) {
            path.add(i);
            sum += i;
            backtracking(k, n, i + 1);
            path.removeLast(); //回溯
            sum -= i; //回溯
        }
    }
}
```

## 电话号码的字母组合

17mid 电话号码的字母组合 

https://leetcode.cn/problems/letter-combinations-of-a-phone-number/

【1.构造数组映射 2.递归+回溯 3.每层遍历的是不同的数组】

![17. 电话号码的字母组合](E:\2021java\Typora\typora-user-images\20201123200304469.png)

【用数组存储映射】

```java
class Solution {
    List<String> res = new ArrayList<>();
    StringBuilder sb = new StringBuilder();
    public List<String> letterCombinations(String digits) {
        if(digits == null || digits.length() == 0) return res;
        String[] numString = new String[]{"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        backtracking(digits, numString, 0);
        return res;
    }
    private void backtracking(String digits, String[] numString, int index) {
        if(index == digits.length()) { //终止条件
            res.add(sb.toString());
            return;
        }
        int digit = digits.charAt(index) - '0'; //获取遍历到digits某一位的数字字符所在的下标
        String str = numString[digit];
        for(int i = 0; i < str.length(); i++) { //对于现在这串字符串，从头到尾遍历
            sb.append(str.charAt(i));
            backtracking(digits, numString, index + 1);
            sb.deleteCharAt(sb.length() - 1); //回溯
        }        
    }
}
```

【用map存储映射 ck】

```java
class Solution {
    Map<Character, String> map = new HashMap<>();
    List<String> res = new ArrayList<>();
    StringBuilder sb = new StringBuilder();
    int index = 0;//当前遍历的字符串的下标
    public List<String> letterCombinations(String digits) {
        if(digits.length() == 0) return res;
        map.put('2', "abc");
        map.put('3', "def");
        map.put('4', "ghi");
        map.put('5', "jkl");
        map.put('6', "mno");
        map.put('7', "pqrs");
        map.put('8', "tuv");
        map.put('9', "wxyz");
        backtracking(digits, 0);
        return res;
    }
    void backtracking(String digits, int index) { 
        //终止条件
        if(index == digits.length()) {
            res.add(sb.toString());
            return;
        }
        char charNum = digits.charAt(index); //获取当前遍历到的digits的数字字符
        String str = map.get(charNum); //获取数字字符对应的英文字符串
        for(int i = 0; i < str.length(); i++) {
            sb.append(str.charAt(i));
            backtracking(digits, index + 1);
            sb.deleteCharAt(sb.length() - 1); //回溯
        }
    }
}
```



## 组合总和

39mid 组合总和 

https://leetcode.cn/problems/combination-sum/

【1.递归+回溯 2.每一层下探的时候，是从i开始而不是i+1，因为本层遍历到的数值下一层还可以重复】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    int sum = 0;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        backtracking(candidates, target, 0);
        return res;
    }
    void backtracking(int[] candidates, int target, int index) {
        if(sum > target) return; //剪枝
        if(sum == target) { //终止条件
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = index; i < candidates.length; i++) {
            path.add(candidates[i]);
            sum += candidates[i];
            backtracking(candidates, target, i); //注意这里是i，不是i+1
            path.removeLast(); //回溯
            sum -= candidates[i]; //回溯
        }
    }
}
```

## 组合总和II

40mid 组合总和II  

https://leetcode.cn/problems/combination-sum-ii/

【数组内含有重复元素，需要对排列的组合去重】

【1.递归+回溯 2.树层剪枝，去重 3.一开始要对nums数组进行排序Arrays.sort(candidates)】

![40.组合总和II](E:\2021java\Typora\typora-user-images\20201123202736384.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    int sum = 0;
    boolean[] used; //标志这个位置的数字有没有被使用
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates); //排序，使数组内的相同元素都挨在一起
        used = new boolean[candidates.length];
        backtracking(candidates, target, 0);
        return res;
    }
    void backtracking(int[] candidates, int target, int startIndex) {
        if(sum > target) return; //纵向剪枝
        if(sum == target) { //终止条件
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = startIndex; i < candidates.length; i++) {
            //回溯后重复的情况，剪枝
            if(i > 0 && candidates[i] == candidates[i - 1] && !used[i - 1]) continue;
            path.add(candidates[i]);
            sum += candidates[i];
            used[i] = true;
            backtracking(candidates, target, i + 1);
            path.removeLast(); //回溯
            sum -= candidates[i]; //回溯
            used[i] = false; //回溯
        }
    }
}
```

## 分割回文串

131mid 分割回文串 

https://leetcode.cn/problems/palindrome-partitioning/

【1.递归+回溯 2.定义一个判断回文串的方法isPalindrome 3.每层内随着i向后截取，startIndex就是分割线】

```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    LinkedList<String> path = new LinkedList<>();
    public List<List<String>> partition(String s) {
        backtracking(s, 0);
        return res;
    }
    void backtracking(String s, int startIndex) {
        //终止条件：分割完后startIndex == s.length()
        if(startIndex >= s.length()) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = startIndex; i < s.length(); i++) {
            String str = s.substring(startIndex, i + 1);
            if(isValid(str)) path.add(str); //如果该字符串满足回文子串，则加进path
            else continue; //否则i继续右移，寻找下一个截取点
            backtracking(s, i + 1);
            path.removeLast(); //回溯
        }
    }
    //判断回文子串的方法
    boolean isValid(String s) {
        int left = 0, right = s.length() - 1;
        while(left < right) {
            if(s.charAt(left) != s.charAt(right)) return false;
            left++;
            right--;
        }
        return true;
    }
}
```

## 复原IP地址

93mid 复原IP地址 

https://leetcode.cn/problems/restore-ip-addresses/

【1.递归+回溯 2.对添加的“.”的个数进行判断，如果为3，则判断最后一部分字符串是否满足要求】

```java
class Solution {
    List<String> res = new ArrayList<>();
    int pointNum = 0; //记录插入的点的个数    
    public List<String> restoreIpAddresses(String s) {
        backtracking(s, 0);
        return res;
    }
    void backtracking(String s, int startIndex) {
        if(pointNum == 3) {
            String str = s.substring(startIndex);
            if(isValid(str)) res.add(s);
            return;
        }
        for(int i = startIndex; i < s.length(); i++) {
            String str = s.substring(startIndex, i + 1);
            if(isValid(str)) {
                s = s.substring(0, i + 1) + "." + s.substring(i + 1);
                pointNum++;
                backtracking(s, i + 2);
                s = s.substring(0, i + 1) + s.substring(i + 2); //回溯
                pointNum--; //回溯
            } else break; //剪枝，当前截取的已经不满足要求了，再往后扩大截取范围更不满足了
        }
    }
    boolean isValid(String s) {
        if("".equals(s)) return false;
        if(s.length() > 3) return false;
        if(s.length() > 1 && s.charAt(0) == '0') return false;        
        if(Integer.valueOf(s) > 255) return false;
        return true;
    }
}
```

## 子集问题

78mid 子集 

【无横向去重，也无纵向去重】

https://leetcode.cn/problems/subsets/

【1.递归+回溯 2.空集也是子集 3.要在每个节点处将此时的path添加到结果集res】

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```



```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> subsets(int[] nums) {
        res.add(new ArrayList(path)); //加入空集
        backtracking(nums, 0);
        return res;
    }
    private void backtracking(int[] nums, int startIndex) {
        if(startIndex >= nums.length) return; //这个终止条件可有可无
        for(int i = startIndex; i < nums.length; i++) {
            path.add(nums[i]);
            res.add(new ArrayList(path));
            backtracking(nums, i + 1);
            path.removeLast(); //回溯
        }
    }
}
```

【ck 新写法 20230210】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path=  new LinkedList<>();
    public List<List<Integer>> subsets(int[] nums) {
        backtracking(nums, 0);
        return res;
    }
    void backtracking(int[] nums, int startIndex) {
        //每次进入方法就添加一次path进res
        res.add(new ArrayList<>(path));

        for(int i = startIndex; i < nums.length; i++) {
            path.add(nums[i]);
            backtracking(nums, i + 1);
            path.removeLast();
        }
    }
}
```



## 子集II

90mid 子集II  

【可排序，横向去重，用used/set，如果数组不能排序，则只能用set】

https://leetcode.cn/problems/subsets-ii/

【数组内含有重复元素，需要对排列的组合去重】

【1.递归+回溯 2.树层剪枝，去重 3.一开始要对nums数组进行排序Arrays.sort(candidates) 4.要在每个节点处将此时的path添加到结果集res】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    boolean[] used;
    public List<List<Integer>> subsetsWithDup(int[] nums) {        
        res.add(new ArrayList<>(path)); //添加空集
        used = new boolean[nums.length];
        Arrays.sort(nums); //一定要排序
        backtracking(nums, 0);
        return res;
    }
    private void backtracking(int[] nums, int startIndex) {
        if(startIndex >= nums.length) return; //该终止条件可有可无
        for(int i = startIndex; i < nums.length; i++) {
            if(i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue; //树层剪枝，去重
            path.add(nums[i]);
            used[i] = true;
            res.add(new ArrayList<>(path));
            backtracking(nums, i+1);
            path.removeLast(); //回溯
            used[i] = false; //回溯
        }
    }
}
```

## 递增子序列

491mid 递增子序列 

【所给数组无序，且不能排序，且需要横向去重，用set】

https://leetcode.cn/problems/increasing-subsequences/

【排除子序列每层所选元素重复的情况，用set去存储每层选过的元素，再回溯后遍历下一个元素，如果set里有这个元素，说明该层这个数值在这个位置被选用过了，continue即可】

【1.递归+回溯 2.比较当前遍历到的值，如果比path最后一个数小，则去除 3.树层剪枝，用set去重】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> findSubsequences(int[] nums) {
        backtacking(nums, 0);
        return res;
    }
    private void backtacking(int[] nums, int startIndex){
        if(startIndex >= nums.length) return; //该终止条件可有可无
        Set<Integer> set = new HashSet<>(); //每层开始重新定义set，防止该层所选元素出现重复元素
        for(int i = startIndex; i < nums.length; i++) {
            if(!path.isEmpty() && nums[i] < path.get(path.size() - 1)) continue; //去除不是递增的情况
            if(set.contains(nums[i])) continue; //树层剪枝，用set
            path.add(nums[i]);
            set.add(nums[i]);
            if(path.size() > 1) res.add(new ArrayList<>(path));
            backtacking(nums, i + 1);
            path.removeLast(); //回溯
        }
    }
}
```

## 全排列

46mid 全排列  

【只有纵向去重，用used即可；无横向去重】

https://leetcode.cn/problems/permutations/

【所给数组内不含重复元素】【排列与组合不同】

【1.递归+回溯 2.用used[]标志元素是否用过】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    boolean[] used; //标志元素是否用过，排除纵向添加重复元素
    public List<List<Integer>> permute(int[] nums) {
        used = new boolean[nums.length];
        backtracking(nums, used);
        return res;
    }
    private void backtracking(int[] nums, boolean[] used) {
        if(path.size() == nums.length) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < nums.length; i++) {
            if(used[i]) continue; //该元素用过，跳过
            path.add(nums[i]);
            used[i] = true; 
            backtracking(nums, used);
            path.removeLast(); //回溯
            used[i] = false; //回溯
        }
    }
}
```

## 全排列II

47mid 全排列II  

https://leetcode.cn/problems/permutations-ii/

【可排序，既有横向去重（set或used均可），也有纵向去重（used）】

【数组内含重复元素 】

【1.递归+回溯 2.用used[]标志元素是否用过 3.要对数组进行排序 4.树层剪枝，去重】

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    LinkedList<Integer> path = new LinkedList<>();
    boolean[] used;
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        used = new boolean[nums.length];
        backtracking(nums);
        return res;
    }
    void backtracking(int[] nums) {
        if(path.size() == nums.length) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < nums.length; i++) {           
            if(i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;//横向去重
            if(used[i]) continue; //纵向去重,该元素用过，跳过
            path.add(nums[i]);
            used[i] = true;
            backtracking(nums);
            path.removeLast(); //回溯
            used[i] = false; //回溯
        }
    }
}
```

## N皇后

51hard N皇后  【棋盘搜索问题】

https://leetcode.cn/problems/n-queens/

【1.递归+回溯 2.初始化棋盘，对每一层进行树层遍历 3.通过判断该点是否符合条件进行剪枝】

```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    public List<List<String>> solveNQueens(int n) {
        char[][] chessboard = new char[n][n];
        for(char[] c : chessboard) { //初始化棋盘，填充'.'
            Arrays.fill(c, '.');
        }
        backtracking(chessboard, 0, n);
        return res;
    }
    void backtracking(char[][] chessboard, int row, int n) {
        if(row == n) { //终止条件，搜索完棋盘（搜完最后一行）
            res.add(array2List(chessboard));
            return;
        }
        for(int col = 0; col < n; col++) {
            if(isValid(chessboard, row, col, n)) { //剪枝
                chessboard[row][col] = 'Q';
                backtracking(chessboard, row + 1, n);
                chessboard[row][col] = '.'; //回溯
            }
        }
    }
    //判断该点是否合法的方法
    boolean isValid(char[][] chessboard, int row, int col, int n) {
        //判断该列往上有无Q
        for(int i = row - 1; i >= 0; i--) {
            if(chessboard[i][col] == 'Q') return false;
        }
        //判断左上45度有无Q
        for(int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if(chessboard[i][j] == 'Q') return false;
        }
        //判断右上45度有无Q
        for(int i = row - 1, j = col + 1; i >= 0 && j <= n - 1; i--, j++) {
            if(chessboard[i][j] == 'Q') return false;
        }
        return true;
    }
    //将棋盘的每行数据连城字符串，并添加到list返回的方法
    List<String> array2List(char[][] chessboard) {
        List<String> list = new ArrayList<>();
        for(char[] c1 : chessboard) {
            StringBuilder sb = new StringBuilder();
            for(char c2 : c1) {
                sb.append(c2);
            }
            list.add(sb.toString());
        }
        return list;
    }
}
```

## 解数独

37hard 解数独 【棋盘搜索问题】

https://leetcode.cn/problems/sudoku-solver/

【只取一个节点的结果即可，此时backtracking的返回值是boolean，发现有满足条件的结果，立刻向上返回true】

【1.递归+回溯（二维递归） 2.判断数字是否重复（是否合法），如果合法，立马向上返回true】

```java
class Solution {
    public void solveSudoku(char[][] board) {
        backtracking(board);
    }
    private boolean backtracking(char[][] board) {
        //二维递归
        for(int i = 0; i < 9; i++) {
            for(int j = 0; j < 9; j++) {
                if(board[i][j] != '.') continue;
                for(char k = '1'; k <= '9'; k++) {
                    if(isValid(i, j, k, board)) { //该数字合法
                        board[i][j] = k;
                        boolean flag = backtracking(board);
                        if(flag) return true; //如果递归返回上来的是true，表明符合条件，立马向上返回true
                        board[i][j] = '.'; //回溯
                    }
                }
                return false; //这个格子里，9个数字都不满足，向上返回false，停止递归，剪枝
            }
        }
        return true; //遍历完棋盘所有格子，没有不满足的情况，即棋盘填满，返回true，表明有结果了
    }
    private boolean isValid(int row, int col, char k, char[][] board) {
        //判断该行有无重复元素
        for(int j = 0; j < 9; j++) { //j < 9 而不是 j < col
            if(board[row][j] == k) return false;
        }
        //判断该列有无重复元素
        for(int i = 0; i < 9; i++) { //i < 9 而不是 i < row
            if(board[i][col] == k) return false;
        }
        //判断3X3宫内有无重复元素
        int startRow = (row / 3) * 3;
        int startCol = (col / 3) * 3;
        for(int i = startRow; i < startRow + 3; i++) {
            for(int j = startCol; j < startCol + 3; j++) {
                if(board[i][j] == k) return false;
            }
        }
        //没有出现不满足的情况
        return true;
    }
}
```

# 贪心算法

## 分发饼干

455easy 分发饼干 

https://leetcode.cn/problems/assign-cookies/

【先排序】【小饼干先满足小胃口】【饼干数量要大于胃口】

```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        //小饼干先满足小胃口
        Arrays.sort(g);
        Arrays.sort(s);
        int count = 0;
        int start = 0; //标记胃口g的下标
        for(int i = 0; i < s.length && start < g.length; i++) { //s饼干少，对饼干进行遍历
            if(s[i] >= g[start]) {
                count++;
                start++;
            }
        }
        return count;
    }
}
```

## 摆动序列

376mid 摆动序列 

https://leetcode.cn/problems/wiggle-subsequence/

【局部最优：删除单调坡度上的节点（不包括单调坡度两端的节点），那么这个坡度就可以有两个局部峰值。】

【整体最优：整个序列有最多的局部峰值，从而达到最长摆动序列。】

![376.摆动序列](E:\2021java\Typora\typora-user-images\20201124174327597.png)

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        int count = 0; //count为上升或下降的线段数
        int pre = 0;
        for(int i = 1; i < nums.length; i++) {
            int diff = nums[i] - nums[i - 1];
            if((diff < 0 && pre >= 0) || (diff > 0 && pre <= 0)) {
                count++;
                pre = diff;
            }
        }
        return count + 1; //点数为线段数+1
    }
}
```

## 最大子序和

53mid 最大子数组和 【子数组 是数组中的一个连续部分】

https://leetcode.cn/problems/maximum-subarray/

简短解法

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];
        int pre = 0;
        for(int num : nums) {
            pre = Math.max(pre + num, num);//如果pre<0,则pre重置为num。如果pre>=0,则pre=pre+num
            res = Math.max(res, pre);
        }
        return res;
    }
}
```

清晰解法

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int sum = Integer.MIN_VALUE;
        int count = 0;
        for(int i = 0; i < nums.length; i++) {
            count += nums[i];
            sum = Math.max(sum, count);
            if(count <= 0) count = 0;
        }
        return sum;
    }
}
```

清晰解法 ck

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];
        int count = nums[0];
        for(int i = 1; i < nums.length; i++) {
            //如果前面的累加值为负数，则重置为当前值
            if(count < 0) count = nums[i];
            //如果前面的累加值非负数，则加上当前值
            else count += nums[i];
            res = Math.max(res, count); //比较出最大值
        }
        return res;
    }
}
```



## 买卖股票的最佳时机II

122mid 买卖股票的最佳时机II 【只收集正利润】

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/

<img src="E:\2021java\Typora\typora-user-images\2020112917480858.png" alt="122.买卖股票的最佳时机II" style="zoom: 50%;" />

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 1; i < prices.length; i++) {
            int diff = prices[i] - prices[i - 1];
            if(diff > 0) res += diff;
        }
        return res;
    }
}
```

## 跳跃游戏

55mid 跳跃游戏 

https://leetcode.cn/problems/jump-game/

【贪心算法局部最优解：每次取最大跳跃步数（取最大覆盖范围），整体最优解：最后得到整体最大覆盖范围，看是否能到终点】

```java
class Solution {
    public boolean canJump(int[] nums) {
        int cover = 0;
        for(int i = 0; i <= cover; i++) {
            cover = Math.max(cover, i + nums[i]);
            if(cover >= nums.length - 1) return true;
        }
        return false;
    }
}
```

【反向贪心（快一点）】

```java
class Solution {
    public boolean canJump(int[] nums) {
        int endReachable = nums.length - 1;
        for(int i = nums.length - 1; i >= 0; i--) {
            if(i + nums[i] >= endReachable) endReachable = i;
        }
        return endReachable == 0;
    }
}
```

## 跳跃游戏II

45mid 跳跃游戏II  

https://leetcode.cn/problems/jump-game-ii/

【题目说了，肯定能到终点】【用最少的跳跃次数，就是尽量跨最大步长】

【移动下标只要遇到当前覆盖最远距离的下标，直接步数加一，不考虑是不是终点的情况】

【只要让移动下标，最大只能移动到nums.size - 2的地方就可以了】

【因为当移动下标指向nums.size - 2时，如果移动下标等于当前覆盖最大距离下标， 需要再走一步（即ans++），因为最后一步一定是可以到的终点。（题目假设总是可以到达数组的最后一个位置）】

<img src="E:\2021java\Typora\typora-user-images\20201201232445286.png" alt="45.跳跃游戏II2" style="zoom:50%;" />

【因为当移动下标指向nums.size - 2时，如果移动下标不等于当前覆盖最大距离下标，说明当前覆盖最远距离就可以直接达到终点了，不需要再走一步】

<img src="E:\2021java\Typora\typora-user-images\20201201232338693.png" alt="45.跳跃游戏II1" style="zoom:50%;" />

```java
class Solution {
    public int jump(int[] nums) {
        int curDistance = 0;
        int nextDistance = 0;
        int res = 0;
        for(int i = 0; i < nums.length - 1; i++){//注意这里是小于nums.size() - 1，这是关键所在
            nextDistance = Math.max(nextDistance, nums[i] + i); //更新下一步覆盖的最远距离下标
            if(i == curDistance) { // 遇到当前覆盖的最远距离下标
                curDistance = nextDistance;  // 更新当前覆盖的最远距离下标
                res++; //更新一次覆盖范围就加一步
            }
        }
        return res;
    }
}
```

## K 次取反后最大化的数组和

1005easy K 次取反后最大化的数组和

https://leetcode.cn/problems/maximize-sum-of-array-after-k-negations/

【将数组按绝对值大小，从大到小排序（慢）】

【Integer.compare针对Integer类型数组】

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        Integer[] a = new Integer[nums.length];
        for(int i = 0; i < nums.length; i++) {
            a[i] = nums[i];
        }
        Arrays.sort(a, (i, j) -> Integer.compare(Math.abs(j), Math.abs(i)));
        for(int i = 0; i < a.length; i++) {
            if(k > 0 && a[i] < 0) {
                a[i] = -a[i];
                k--;
            }
            if(k == 0) break;
        }
        //k还没用完，如果k是偶数，说明最后不需要反转；如果k是奇数，最后相当于反转一次
        if(k % 2 == 1) a[a.length - 1] = -a[a.length - 1];
        int res = 0;
        for(int num : a) {
            res += num;
        }
        return res;
    }
}
```

【将数组从小到大排序】

```java
class Solution {
    public int largestSumAfterKNegations(int[] nums, int k) {
        if(nums.length == 1) return (k % 2 == 0) ? nums[0] : -nums[0];
        Arrays.sort(nums);
        int idx = 0;
        int res = 0;
        for(int i = 0; i < k; i++) { 
            if(i < nums.length - 1 && nums[idx] < 0) { //nums.length - 1的-1很重要
                nums[idx] = -nums[idx];
                if(nums[idx] >= Math.abs(nums[idx + 1])) idx++; //是>= 而不是 >
                continue;
            }
            nums[idx] = -nums[idx];
        }
        for(int i = 0; i < nums.length; i++) {
            res += nums[i];
        }
        return res;
    }
}
```

## 加油站

134mid 加油站

https://leetcode.cn/problems/gas-station/

【如果总油量减去总消耗totalSum大于等于零那么一定可以跑完一圈，说明 各个站点的加油站 剩油量rest[i]相加一定是大于等于零的】

【每个加油站的剩余量rest[i]为gas[i] - cost[i]】

【i从0开始累加rest[i]，和记为curSum，一旦curSum小于零，说明[0, i]区间都不能作为起始位置，起始位置从i+1算起，再从0计算curSum】

<img src="E:\2021java\Typora\typora-user-images\20201213162821958.png" alt="134.加油站" style="zoom:50%;" />

```java
class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int curSum = 0;
        int totalSum = 0;
        int start = 0;
        for(int i = 0; i < gas.length; i++) {
            curSum += gas[i] - cost[i];
            totalSum += gas[i] - cost[i];
            if(curSum < 0) {
                start = i + 1; //起点至少要从i的下一个位置开始
                curSum = 0; //curSum重置为0，从下一个坐标重新开始计算
            }
        }
        if(totalSum < 0) return -1;
        return start;
    }
}
```

## 分发糖果

135hard 分发糖果

https://leetcode.cn/problems/candy/

【遇到两个维度权衡的时候，一定要先确定一个维度，再确定另一个维度】

【两次贪心，从局部最优推出了全局最优，即：相邻的孩子中，评分高的孩子获得更多的糖果】

【一次是从左到右遍历，只比较右边孩子评分比左边大的情况】

【一次是从右到左遍历，只比较左边孩子评分比右边大的情况】

```java
class Solution {
    public int candy(int[] ratings) {
        int len = ratings.length;
        int[] count = new int[len];
        count[0] = 1;
        //先从左往右，找出右孩子>左孩子的情况
        for(int i = 1; i < len; i++) { //从左数第二个元素开始，作为右孩子，去与左孩子比较
            if(ratings[i] > ratings[i - 1]) count[i] = count[i - 1] + 1;
            else count[i] = 1;
        }
        //再从右往左，找出左孩子>右孩子的情况
        for(int i = len - 2; i >= 0; i--) { //从右数第二个位置元素开始，作为左孩子，去与右孩子比较
            if(ratings[i] > ratings[i + 1]) count[i] = Math.max(count[i], count[i + 1] + 1);
        }
        int res = 0;
        for(int c : count) {
            res += c;
        }
        return res;
    }
}
```

## 柠檬水找零

860easy 柠檬水找零

https://leetcode.cn/problems/lemonade-change/

【情况一：账单是5，直接收下】

【情况二：账单是10，消耗一个5，增加一个10】

【情况三：账单是20，优先消耗一个10和一个5，如果不够，再消耗三个5】

```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0, ten = 0, twenty = 0;
        for(int bill : bills) {
            if(bill == 5) five++;
            if(bill == 10) {
                if(five <= 0) return false;
                five--;
                ten++;
            }
            if(bill == 20) {
                if(five > 0 && ten > 0) { //优先消耗10美元，因为5美元的找零用处更大，能多留着就多留着
                    five--;
                    ten--;
                } else if(five >= 3) { //10美元不够，但5美元数量够
                    five -=3;
                } else return false;
            }
        }
        return true;
    }
}
```

## 根据身高重建队列

406mid 根据身高重建队列

https://leetcode.cn/problems/queue-reconstruction-by-height/

【和“分发糖果”类似，先根据身高从大到小排序（身高相等的，按k从小到大拍），再根据k作为插入的下标位置】

【局部最优：优先按身高高的people的k来插入。插入操作过后的people满足队列属性】

【全局最优：最后都做完插入操作，整个队列满足题目队列属性】

<img src="E:\2021java\Typora\typora-user-images\20201216201851982.png" alt="406.根据身高重建队列" style="zoom:67%;" />

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        Arrays.sort(people, (a, b) -> {
                        if(a[0] == b[0]) return a[1] - b[1]; //同等身高，按第二个数从小到大排序
                        return b[0] - a[0]; //若身高不同，则按身高从大到小排序
                    });
        LinkedList<int[]> que = new LinkedList<>();
        for(int[] p : people) {
            que.add(p[1], p); //将数组p插入下标为p[1]的位置
        }
        return que.toArray(new int[people.length][]); //将链表转为数组输出
    }
}
```

相关知识1：lambda表达式

```java
Arrays.sort(people, (a, b) -> {
                        if(a[0] == b[0]) return a[1] - b[1]; //同等身高，按第二个数从小到大排序
                        return b[0] - a[0]; //若身高不同，则按身高从大到小排序
                    });
```

相关知识2：LinkedList队列按位置插入

```java
que.add(p[1], p); //将数组p插入下标为p[1]的位置
```

相关知识3：将链表转为数组输出

```java
return que.toArray(new int[people.length][]); //将链表转为数组输出
```

## 用最少数量的箭引爆气球

452mid 用最少数量的箭引爆气球

https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/

【对二维数组根据points[0]从小到大排序，找重叠】

<img src="E:\2021java\Typora\typora-user-images\20201123101929791.png" alt="452.用最少数量的箭引爆气球" style="zoom:50%;" />

```
//对于最大值和最小值的测试用例无法通过
Arrays.sort(points, (a, b) -> a[0] - b[0]); 
```



```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        if(points.length == 1) return 1;
        Arrays.sort(points, (a, b) -> Integer.compare(a[0], b[0]));
        int count = 1;
        for(int i = 1; i < points.length; i++) {
            if(points[i][0] > points[i - 1][1]) count++; //当前气球没有和前一个气球有重叠
            else points[i][1] = Math.min(points[i][1], points[i - 1][1]); //更新当前气球的右边界
        }
        return count;
    }
}
```

## 无重叠区间

435mid 无重叠区间 【此时问题就是要求非交叉区间的最大个数】

https://leetcode.cn/problems/non-overlapping-intervals/

【对题目“用最少数量的箭引爆气球”稍加改动即可，这列边界相等的情况视作不重叠，可以算出对于不重叠区间需要的箭的数量count，再用总数量减去count，得到的就是重叠的数量，即需要移除的区间数量】

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if(intervals.length == 1) return 0;
        int count = 1;
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        for(int i = 1; i < intervals.length; i++) {
            if(intervals[i][0] >= intervals[i - 1][1]) count++;
            else intervals[i][1] = Math.min(intervals[i][1], intervals[i - 1][1]);
        }
        //count是不重叠的的区间数量，所以要移除的就是总数量-不重叠数量，即重叠数量
        return intervals.length - count; 
    }
}
```

## 划分子母区间

736mid 划分子母区间 

https://leetcode.cn/problems/partition-labels/

【统计每一个字符最后出现的位置，用map存储】

【关键在于每遍历一个字符，都要更新一次最远右边界】

【从头遍历字符，并更新字符的最远出现下标，如果找到字符最远出现位置下标和当前下标相等了，则找到了分割点】

![763.划分字母区间](E:\2021java\Typora\typora-user-images\20201222191924417.png)

```java
//更新最远右边界
right = Math.max(right, map.get(s.charAt(i)));
```



```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        List<Integer> res = new ArrayList<>();
        Map<Character, Integer> map = new HashMap<>();
        int start = 0; //初始化该片段的左边界
        int right = 0; //初始化该片段最远右边界
        for(int i = 0; i < s.length(); i++) {
            map.put(s.charAt(i), i); //统计每一个字符最后出现的位置
        }
        for(int i = 0; i < s.length(); i++) {
            right = Math.max(right, map.get(s.charAt(i))); //更新最远右边界
            if(i == right) { //当i遇到最远右边界，即找到这个片段的结束点
                int len = right - start + 1;
                res.add(len);
                start = i + 1; //更新下一个片段的左边界
            }
        }
        return res;
    }
}
```

## 合并区间

56mid 合并区间

https://leetcode.cn/problems/merge-intervals/

【先按左边界排序，再判断现在段的左边界是否大于前一段的右边界】

【如果当前区间和此前的[start, end]没有重叠，则更新start和end；如果有重叠，就更新end】

<img src="E:\2021java\Typora\typora-user-images\20201223200632791.png" alt="56.合并区间" style="zoom:50%;" />

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> res = new ArrayList<>();
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0])); //按左边界从小到大排序
        int start = intervals[0][0];
        int end = intervals[0][1];
        for(int i = 1; i < intervals.length; i++) {
            if(intervals[i][0] > end) { //没有重叠，无需合并
                res.add(new int[]{start, end}); //因为没有重叠，将前一个数组添加进res
                start = intervals[i][0]; //更新左边界
                end = intervals[i][1]; //更新右边界
            } else end = Math.max(end, intervals[i][1]); //有重叠，更新右边界
        }
        //会有最后一个新的左右边界数据没有添加进res，所以要添加进去
        res.add(new int[]{start, end});
        return res.toArray(new int[res.size()][]);
    }
}
```

## 单调递增的数字

738mid 单调递增的数字

https://leetcode.cn/problems/monotone-increasing-digits/

【例如：98，一旦出现strNum[i - 1] > strNum[i]的情况（非单调递增），首先想让strNum[i - 1]--，然后strNum[i]给为9，这样这个整数就是89，即小于98的最大的单调递增整数】

```java
//将数组转为字符串，再转为整型数
Integer.parseInt(String.valueOf(c));
```



```java
class Solution {
    public int monotoneIncreasingDigits(int n) {
        String s = String.valueOf(n);
        char[] c = s.toCharArray();
        int start = c.length;
        for(int i = c.length - 1; i > 0; i--) {
            if(c[i] < c[i - 1]) {
                c[i - 1]--; //如果c[i - 1]比后面的数大，肯定就不是0，可以放心减一
                start = i; //将当前i的位置标记为替换'9'的位置
            }
        }
        //替换'9'
        for(int i = start; i < c.length; i++) {
            c[i] = '9';
        }
        return Integer.parseInt(String.valueOf(c)); //将数组转为字符串，再转为整型数
    }
}
```

## 买卖股票的最佳时机含手续费

714mid 买卖股票的最佳时机含手续费 

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

【不能简单取每个区间的最大利润相加】

【建议用动态规划做】

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int res = 0;
        int minPrice = prices[0];
        for(int i = 1; i < prices.length; i++) {
            if(prices[i] < minPrice) minPrice = prices[i];
            else {
                int diff = prices[i] - minPrice - fee;
                if(diff <= 0) continue;
                else {
                    res += diff;
                    minPrice = prices[i] - fee; //玄学，关键
                }
            }
        }
        return res;
    }
}
```

## 监控二叉树

968hard 监控二叉树

# 动态规划

对背包问题，要区分“背包容量为j”和“填满背包容量j”两种情况

背包容量为j：dp[j] = dp[j - weight[i]] + value[i]

填满背包容量j：dp[j] += dp[j - coins[i]];

对背包问题，要区分“求组合数”和“求组合里最小元素数量/最大元素数量”

```java
//求组合里最小元素数量/最大元素数量
if (dp[j - coins[i]] != Integer.MAX_VALUE) {
                    dp[j] = Math.min(dp[j], dp[j - coins[i]] + 1); 
```



## 动态规划理论基础

背包问题、打家劫舍、股票问题、子序列问题

关键五步

【1.确定dp数组（dp table）以及下标的含义 

​	2.确定递推公式 

​	3.dp数组如何初始化 

​	4.确定遍历顺序 

​	5.打印dp数组】

## 斐波那契数

509easy 斐波那契数 【1.特殊情况处理 2.确定递推公式 3.dp数组初始化】

https://leetcode.cn/problems/fibonacci-number/

```java
class Solution {
    public int fib(int n) {
        if(n == 0 || n == 1) return n;
        int[] dp = new int[n + 1];
        dp[0] = 0; //dp数组初始化
        dp[1] = 1; //dp数组初始化
        for(int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2]; //递推公式
        }
        return dp[n];
    }
}
```

## 爬楼梯

70easy 爬楼梯 【1.特殊情况处理 2.确定递推公式 3.dp数组初始化】

https://leetcode.cn/problems/climbing-stairs/

```java
class Solution {
    public int climbStairs(int n) {
        if(n == 1 || n == 2) return n;
        int[] dp = new int[n + 1];
        dp[1] = 1; //从1阶开始初始化
        dp[2] = 2;
        for(int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

## 使用最小花费爬楼梯

746easy 使用最小花费爬楼梯

https://leetcode.cn/problems/min-cost-climbing-stairs/

【1.定义dp[i]为到达第i层所需要的花费 2.可以有两个途径得到dp[i]，一个是dp[i-1] 一个是dp[i-2] 3.初始化dp[0]和dp[1]】

![image-20221120223341253](E:\2021java\Typora\typora-user-images\image-20221120223341253.png)

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int len = cost.length;
        int[] dp = new int[len + 1]; //dp[i]表示到达第i层需要的花费
        dp[0] = 0; //初始化
        dp[1] = 0; //初始化
        for(int i = 2; i <= len; i++) {
            dp[i] = Math.min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]); //选择最小的花费
        }
        return dp[len];
    }
}
```

## 不同路径

62mid 不同路径  

https://leetcode.cn/problems/unique-paths/

【1. 定义dp数组含义 2.确定递推公式 3. 初始化dp数组】

【当前节点的路径数等于左边节点和上边节点路径数之和】

<img src="E:\2021java\Typora\typora-user-images\20201209113631392.png" alt="62.不同路径1" style="zoom:50%;" />

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n]; //表示到达i,j位置的路径数
        for(int i = 0; i < m; i++) dp[i][0] = 1;//初始化最左边一列的数字
        for(int j = 0; j < n; j++) dp[0][j] = 1;//初始化最上面一行数字
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

## 不同路径II

63mid 不同路径II 

https://leetcode.cn/problems/unique-paths-ii/

【1. 定义dp数组含义 2.确定递推公式 3. 初始化dp数组（遇到障碍，停止初始化）4. 遇到障碍，跳过赋值dp数组】

```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length; //行数
        int n = obstacleGrid[0].length; //列数
        int[][] dp = new int[m][n]; //表示到达i,j位置的路径数
        for(int i = 0; i < m; i++) { //初始化最左边列数字
            if(obstacleGrid[i][0] == 1) break; //遇到障碍，停止初始化
            dp[i][0] = 1;
        }
        for(int j = 0; j < n; j++) { //初始化最上面一行数字
            if(obstacleGrid[0][j] == 1) break; //遇到障碍，停止初始化
            dp[0][j] = 1;
        }
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                if(obstacleGrid[i][j] == 1)  continue; //遇到障碍，跳过
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

## 整数拆分

343mid 整数拆分 

https://leetcode.cn/problems/integer-break/

【1.dp[i]表示拆分数字i所得到的最大乘积 2. 递推公式 dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j]));】

【严格从dp[i]的定义来说，dp[0] dp[1] 就不应该初始化，也就是没有意义的数值】

【也可以这么理解，j * (i - j) 是单纯的把整数拆分为两个数相乘，而j * dp[i - j]是拆分成两个以及两个以上的个数相乘】

【那么在取最大值的时候，为什么还要比较dp[i]呢？

​	因为在递推公式推导的过程中，每次计算dp[i]，取最大的而已】

```java
class Solution {
    public int integerBreak(int n) {
        if(n == 2) return 1;
        int[] dp = new int[n + 1]; //定义dp[n]为拆分n的最大乘积
        dp[0] = 0;
        dp[1] = 0;
        dp[2] = 1;
        for(int i = 3; i <= n; i++) {
            for(int j = 1; j <= i/2; j++) {
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), j * dp[i - j])); //递推公式
            }
        }
        return dp[n];
    }
}
```

## 不同的二叉搜索树

96mid 不同的二叉搜索树

https://leetcode.cn/problems/unique-binary-search-trees/

【1. dp[i] ：1到i为节点组成的二叉搜索树的个数为dp[i] 

2. 递推关系， dp[i] += dp[以j为头结点左子树节点数量] * dp[以j为头结点右子树节点数量]
3. 确定遍历顺序】

【递推公式：dp[i] += dp[j - 1] * dp[i - j]; ，j-1 为j为头结点左子树节点数量，i-j 为以j为头结点右子树节点数量】



【ck，总共有i个节点，除了根节点外，子树的节点总数为i - 1，所以左子树节点数有可能为0,1,2,....,(i - 1)，则右子树节点数对应为(i - 1), (i - 2),...,2,1,0】

```java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n + 1]; //1到i为节点组成的二叉搜索树的个数为dp[i]
        dp[0] = 1; //初始化
        dp[1] = 1; //初始化
        for(int i = 2; i <= n; i++) { //树的节点总数，从1到n分情况遍历。也代表根结点占一个节点数
            for(int j = 0; j <= i - 1; j++) { //左子树节点数从0开始遍历
                //dp[j]代表有j个节点的树的种类放在左子树，右边dp[i - 1 - j]同理
                dp[i] += dp[j] * dp[i - 1 - j]; 
            }
        }
        return dp[n];
    }
}
```



```java
class Solution {
    public int numTrees(int n) {
        int[] dp = new int[n + 1]; //1到i为节点组成的二叉搜索树的个数为dp[i]
        dp[0] = 1; //初始化
        dp[1] = 1; //初始化
        for(int i = 2; i <= n; i++) { //树的节点总数，从2到n分情况遍历
            for(int j = 1; j <= i; j++) { //树的根节点为j，从1到i分情况遍历
                dp[i] += dp[j - 1] * dp[i - j];//dp[j - 1]为左子树的种数，dp[i - j]为右子树的种数
            }
        }
        return dp[n];
    }
}
```

## 0-1背包理论基础（二）

1. 确定dp数组的定义：在一维dp数组中，dp[j]表示：容量为j的背包，所背的物品价值可以最大为dp[j]
2. 一维dp数组的递推公式

```java
dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
```

3. 一维dp数组如何初始化：初始化为0
4. 一维dp数组遍历顺序

```java
//遍历顺序：先遍历物品，再遍历背包容量
        for (int i = 0; i < wLen; i++){
            for (int j = bagWeight; j >= weight[i]; j--){
                dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);//保证每个物品仅被添加一次
            }
        }
```

## 分割等和子集

416mid 分割等和子集 

https://leetcode.cn/problems/partition-equal-subset-sum/

【1.背包的体积为sum / 2 

2.背包要放入的商品（集合里的元素）重量为 元素的数值，价值也为元素的数值

3.背包如果正好装满，说明找到了总和为 sum / 2 的子集

4.背包中每一个元素是不可重复放入】

【1.dp[j]表示 背包总容量是j，最大可以凑成j的子集总和为dp[j]

2.相当于背包里放入数值，那么物品i的重量是nums[i]，其价值也是nums[i]

3.如果dp[j] == j 说明，集合中的子集总和正好可以凑成总和j】

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int len = nums.length;
        int sum = 0;
        for(int num : nums) {
            sum += num;
        }
        if(sum % 2 == 1) return false; //sum为奇数，不可平分
        int target = sum / 2;
        int[] dp = new int[target + 1];
        for(int i = 0; i < len; i++) { //先遍历物品
            for(int j = target; j >= nums[i]; j--) { //再遍历背包
                //物品重量是nums[i]，价值也是nums[i]
                dp[j] = Math.max(dp[j], dp[j - nums[i]] + nums[i]);
            }
        }
        return dp[target] == target;
    }
}
```

## 最后一块石头的重量 II

1049mid 最后一块石头的重量 II 

https://leetcode.cn/problems/last-stone-weight-ii/

【尽可能将石头分成重量相等或接近的两部分】

【dp[target]为一半，sum - dp[target]为另一半】

【由题目的提示可知，最大总重量不超过30*100=1500，因此定义dp数组长度为1501】

![image-20221208164112998](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20221208164112998.png)



```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int[] dp = new int[1501];
        int sum = 0;
        for(int stone : stones) {
            sum += stone;
        }
        int target = sum / 2; //尽可能将石头分成重量相等或接近的两部分
        for(int i = 0; i < stones.length; i++) {
            for(int j = target; j >= stones[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - stones[i]] + stones[i]);
            }
        }
        //dp[target]为一半石头重量，sum - dp[target]为另一半石头重量
        return sum - dp[target] - dp[target];
    }
}
```

## 目标和 *

494mid 目标和 

https://leetcode.cn/problems/target-sum/

【既然为target，那么就一定有 left组合 - right组合 = target

​	left + right = sum，而sum是固定的。right = sum - left

​	公式来了， left - (sum - left) = target 推导出 **left = (target + sum)/2** 

​	target是固定的，sum是固定的，left就可以求出来

​	**此时问题就是在集合nums中找出和为left的组合**】

【这次和之前遇到的背包问题不一样了，之前都是求容量为j的背包，最多能装多少】

【例如：dp[j]，j 为5，

已经有一个1（nums[i]） 的话，有 dp[4]种方法 凑成 容量为5的背包。
已经有一个2（nums[i]） 的话，有 dp[3]种方法 凑成 容量为5的背包。
已经有一个3（nums[i]） 的话，有 dp[2]中方法 凑成 容量为5的背包
已经有一个4（nums[i]） 的话，有 dp[1]中方法 凑成 容量为5的背包
已经有一个5 （nums[i]）的话，有 dp[0]中方法 凑成 容量为5的背包
那么凑整dp[5]有多少方法呢，也就是把 所有的 dp[j - nums[i]] 累加起来】

【1. dp[j]：表示**装满**容量为j的背包有dp[j]种方法 2. 递推公式：dp[j] += dp[j - nums[i]] 3. 初始化：dp[0] = 1】

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for(int num : nums) {
            sum += num;
        }
        if((sum + target) % 2 == 1) return 0; //没有方案
        int left = (sum + target) / 2; //装满容量为left的背包
        if(left < 0) return 0; //没有方案
        int[] dp = new int[left + 1];
        dp[0] = 1; //初始化dp数组
        for(int i = 0; i < nums.length; i++) { //先遍历物品
            for(int j = left; j >= nums[i]; j--) { //再遍历背包
                dp[j] += dp[j - nums[i]]; //递推公式
            }
        }
        return dp[left];
    }
}
```

## 一和零

474mid 一和零

https://leetcode.cn/problems/ones-and-zeroes/

【1. strs 数组里的元素就是物品，每个物品都是一个 2. m 和 n相当于是一个背包，两个维度的背包】

【递推公式：dp[i] [j] = max(dp[i] [j], dp[i - zeroNum] [j - oneNum] + 1);

​	01背包的递推公式：dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);

​	对比一下就会发现，字符串的zeroNum和oneNum相当于物品的重量（weight[i]），字符串本身的个数相当于物品的价值（value[i]）】

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];//最多有i个0和j个1的strs的最大子集的大小为dp[i][j]
        for(String str : strs) { //先遍历物品
            int zeroNum = 0;
            int oneNum = 0;
            //统计当前遍历到的字符串（物品）的字符'0'和字符'1'的个数
            for(int idx = 0; idx < str.length(); idx++) {
                if(str.charAt(idx) == '0') zeroNum++;
                else oneNum++;
            }
            //遍历背包，两个维度
            for(int i = m; i >= zeroNum; i--) {
                for(int j = n; j >= oneNum; j--) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - zeroNum][j - oneNum] + 1); //递推公式
                }
            }
        }
        return dp[m][n];
    }
}
```

## 完全背包理论基础

完全背包和01背包问题唯一不同的地方就是，每种物品有无限件

01背包和完全背包唯一不同就是体现在遍历顺序上

```java
for (int i = 0; i < weight.length; i++){ // 遍历物品
        for (int j = weight[i]; j <= bagWeight; j++){ // 遍历背包容量
            dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
        }
    }
```

## 零钱兑换 II

518mid 零钱兑换 II 【完全背包问题】【求组合】

https://leetcode.cn/problems/coin-change-ii/

【dp[j]：凑成总金额j的货币组合数为dp[j]】

【dp[0]初始化为1，表示什么也不装】

【递推公式：dp[j] += dp[j - coins[i]]】

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1]; //dp[j]表示装满容量为j的背包的情况种数
        dp[0] = 1; //j = 0时只有一种情况，表示什么也不装
        for(int i = 0; i < coins.length; i++) { //先遍历物品
            for(int j = coins[i]; j <= amount; j++) { //再遍历背包容量
                dp[j] += dp[j - coins[i]];
            }
        }
        return dp[amount];
    }
}
```

## 组合总和 Ⅳ

377mid 组合总和 Ⅳ  【完全背包】【求排列】

https://leetcode.cn/problems/combination-sum-iv/

【如果求组合数就是外层for循环遍历物品，内层for遍历背包】

【如果求排列数就是外层for遍历背包，内层for循环遍历物品】

【本题求的是排列，所以先遍历背包容量，再遍历物品】

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1; //j = 0时只有一种情况，表示什么也不装
        for(int i = 0; i <= target; i++) { //求排列，先遍历背包容量
            for(int j = 0; j < nums.length; j++) { //再遍历物品
                if(nums[j] <= i) dp[i] += dp[i - nums[j]];
            }
        }
        return dp[target];
    }
}
```

## 爬楼梯（进阶版）

70easy 爬楼梯 【用完全背包解决】【求排列，先遍历背包，再遍历物品】

https://leetcode.cn/problems/climbing-stairs/

【ck 新】

```java
class Solution {
    public int climbStairs(int n) {
        if(n == 1 || n == 2) return n;
        int[] dp = new int[n + 1]; //爬到有i个台阶的楼顶，有dp[i]种方法
        dp[1] = 1; //初始化
        dp[2] = 2; //初始化
        for(int j = 3; j <= n; j++) {//先遍历背包
            for(int i = 1; i <= 2; i++) { //再遍历物品,物品重量有两种,分别是1和2
                if(j >= i) dp[j] += dp[j - i]; //填满背包
            }
        }
        return dp[n];
    }
}
```



```java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1]; //爬到有i个台阶的楼顶，有dp[i]种方法
        int[] weight = {1, 2};
        dp[0] = 1; //初始化
        for(int i = 0; i <= n; i++) { //先遍历背包
            for(int j = 0; j < weight.length; j++) { //再遍历物品
                if(i >= weight[j]) dp[i] += dp[i - weight[j]]; 
            }
        }
        return dp[n];
    }
}  
```

## 零钱兑换

322mid 零钱兑换 【完全背包问题】【求组合】

https://leetcode.cn/problems/coin-change/

【dp[j]：凑足总额为j所需钱币的最少个数为dp[j]】

【递推公式：dp[j] = min(dp[j - coins[i]] + 1, dp[j])】

【考虑到递推公式的特性，dp[j]必须初始化为一个最大的数，否则就会在min(dp[j - coins[i]] + 1, dp[j])比较的过程中被初始值覆盖】

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, Integer.MAX_VALUE); //初始化为最大值
        dp[0] = 0; //凑足金额为0所需要钱币个数为0
        for(int i = 0; i < coins.length; i++) { //先遍历物品
            for(int j = coins[i]; j <= amount; j++) { //再遍历背包容量
                //只有dp[j-coins[i]]不是初始最大值时，该位才有选择的必要
                if (dp[j - coins[i]] != Integer.MAX_VALUE) {
                    dp[j] = Math.min(dp[j], dp[j - coins[i]] + 1); //递推公式
                }
            }
        }
        return dp[amount] == Integer.MAX_VALUE ? -1 : dp[amount];
    }
}
```

## 完全平方数

279mid 完全平方数  【完全背包问题，求组合，先遍历物品，再遍历背包容量】

https://leetcode.cn/problems/perfect-squares/

【dp[j]：和为j的完全平方数的最少数量为dp[j]】

【递推公式：dp[j] = min(dp[j - i * i] + 1, dp[j])】

【初始化：dp[0] = 0;  为了递推公式成立】

```java
class Solution {
    public int numSquares(int n) {
        int[] dp = new int[n + 1]; //dp[j]：和为j的完全平方数的最少数量为dp[j]
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;  //初始化
        for(int i = 1; i * i <= n; i++) { //先遍历物品
            for(int j = i * i; j <= n; j++) { //再遍历背包
              if(dp[j - i * i] != Integer.MAX_VALUE) dp[j] = Math.min(dp[j], dp[j - i * i] + 1);
            }
        }
        return dp[n];
    }
}
```

## 单词拆分

139mid 单词拆分 【完全背包问题，将字符串看作背包，字符串数组里的字符串看作物品】【装满背包】

https://leetcode.cn/problems/word-break/

【而本题其实我们求的是排列数，为什么呢。 拿 s = "applepenapple", wordDict = ["apple", "pen"] 举例。

"apple", "pen" 是物品，那么我们要求 物品的组合一定是 "apple" + "pen" + "apple" 才能组成 "applepenapple"。

"apple" + "apple" + "pen" 或者 "pen" + "apple" + "apple" 是不可以的，那么我们就是强调物品之间顺序。

所以说，本题一定是 先遍历 背包，再遍历物品。】

【确定dp数组以及下标的含义：字符串长度为i的话，dp[i]为true，表示可以拆分为一个或多个在字典中出现的单词】

【确定递推公式：如果确定dp[j] 是true，且 [j, i] 这个区间的子串出现在字典里，那么dp[i]一定是true。（j < i ）。所以递推公式是 if([j, i] 这个区间的子串出现在字典里 && dp[j]是true) 那么 dp[i] = true。】

```java
dp[i] && set.contains(str)
//其中dp[i]表示i之前的字符串满足条件，set.contains(str)表示截取出来的字符串也存在于字典中
```

![image-20221208214824002](C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20221208214824002.png)

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int len = s.length();
        boolean[] dp = new boolean[len + 1]; //dp[j]表示长度为j的字符串是否能拆解
        Set<String> set = new HashSet<>(wordDict);
        dp[0] = true;
        //先遍历背包容量，再遍历物品
        for(int j = 1; j <= len; j++) { // 遍历背包
            for(int i = 0; i < j; i++) { //遍历物品
                String str = s.substring(i, j);
                if(dp[i] && set.contains(str)) dp[j] = true;
            }
        }
        return dp[len];
    }
}
```

## 背包问题总结

<img src="C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20221208215258992.png" alt="image-20221208215258992" style="zoom:50%;" />

01背包，一维数组：先遍历物品，再遍历背包，背包从大到小遍历

完全背包，一维数组：背包从小到大遍历【组合：先物品，再背包】【排列：先背包，再物品】

## 打家劫舍

198mid 打家劫舍 【偷相邻屋子会报警，换言之不能偷相邻屋子】

https://leetcode.cn/problems/house-robber/

【确定dp数组含义：考虑下标i（包括i）以内的房屋，最多可以偷窃的金额为dp[i]】

【递推公式：dp[i] = max(dp[i - 2] + nums[i], dp[i - 1])】

【dp数组初始化：dp[0] = nums[0]; dp[1] = Math.max(nums[0], nums[1]);】

```java
class Solution {
    public int rob(int[] nums) {
        if(nums.length == 1) return nums[0];
        int[] dp = new int[nums.length]; //dp[i]表示偷盗下标为i以及i以下的房屋的最大金额
        dp[0] = nums[0];
        dp[1] = Math.max(nums[0], nums[1]);
        for(int i = 2; i < nums.length; i++) {
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        return dp[nums.length - 1];
    }
}
```

## 打家劫舍II

213mid 打家劫舍II 【所有屋子成环形】

https://leetcode.cn/problems/house-robber-ii/

【将198题的方法抽离成一个方法，分析不同情况】

情况一：考虑不包含首尾元素

<img src="E:\2021java\Typora\typora-user-images\20210129160748643.jpg" alt="213.打家劫舍II" style="zoom:50%;" />

情况二：考虑包含首元素，不包含尾元素

<img src="E:\2021java\Typora\typora-user-images\20210129160821374.jpg" alt="213.打家劫舍II1" style="zoom:50%;" />

情况三：考虑包含尾元素，不包含首元素

<img src="E:\2021java\Typora\typora-user-images\20210129160842491.jpg" alt="213.打家劫舍II2" style="zoom:50%;" />

【情况二 和 情况三 都包含了情况一了，所以只考虑情况二和情况三就可以了】

```java
class Solution {
    public int rob(int[] nums) {
        if(nums.length == 1) return nums[0];
        int res1 = getMax(nums, 0, nums.length - 2);
        int res2 = getMax(nums, 1, nums.length - 1);
        return Math.max(res1, res2);
    }
    int getMax(int[] nums, int start, int end) {
        if(start == end) return nums[start];
        int[] dp = new int[end + 1];        
        dp[start] = nums[start];
        dp[start + 1] = Math.max(nums[start], nums[start + 1]);
        for(int i = start + 2; i <= end; i++) {
            dp[i] = Math.max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[end];
    }
}
```

## 打家劫舍III

337mid 打家劫舍III 【树形dp】 【后序遍历】【dp[0]和dp[1]分别表示不偷、偷】

https://leetcode.cn/problems/house-robber-iii/

【dp数组（dp table）以及下标的含义：下标为0记录不偷该节点所得到的的最大金钱，下标为1记录偷该节点所得到的的最大金钱】

```java
class Solution {
    public int rob(TreeNode root) {
        int[] res = robAction(root);
        return Math.max(res[0], res[1]);
    }
    private int[] robAction(TreeNode root) {        //后序遍历
        if(root == null) return new int[]{0, 0}; //终止条件
        int[] res = new int[2]; // res[0]表示当前节点不偷，res[1]表示当前节点偷
        int[] res1 = robAction(root.left);
        int[] res2 = robAction(root.right);
        //当前节点不偷，左孩子可偷可不偷，右孩子可偷可不偷
        res[0] = Math.max(res1[0], res1[1]) + Math.max(res2[0], res2[1]);
        //当前节点偷，左右孩子均不偷
        res[1] = res1[0] + res2[0] + root.val;
        return res;
    }
}
```

## 买卖股票的最佳时机

121easy 买卖股票的最佳时机 【只买卖一次】

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/

【动态规划（慢）】

```java
//dp[i][0]表示第i天持有股票时手上的现金数，dp[i][1]表示第i天不持有股票时手上的现金数
```

```java
//dp[i - 1][0]表示前一天就持有股票了；-prices[i]表示前一天不持有股票，今天买入
dp[i][0] = Math.max(dp[i - 1][0], -prices[i]);
```

```java
//dp[i - 1][1]表示前一天就不持有股票；dp[i - 1][0] + prices[i]表示前一天持有股票，今天卖出
dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len == 1) return 0;
        //dp[i][0]表示第i天持有股票时手上的现金数，dp[i][1]表示第i天不持有股票时手上的现金数
        int[][] dp = new int[len][2];
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        for(int i = 1; i < len; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], -prices[i]); //只买卖一次，所以是-prices[i]
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
        }
        return dp[len - 1][1];
    }
}
```

【动态规划-压缩成一维数组】

```java
class Solution {
    public int maxProfit(int[] prices) {        
        int len = prices.length;
        int[] dp = new int[2];
        dp[0] = -prices[0];
        dp[1] = 0;
        for(int i = 1; i < len; i++) {
            dp[0] = Math.max(dp[0], - prices[i]);
            dp[1] = Math.max(dp[0] + prices[i], dp[1]);
        }
        return dp[1];
    }
}
```

【贪心（快）】

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len == 1) return 0;
        int res = 0;
        int minPrice = prices[0];
        for(int i = 0; i < len; i++) {
            if(prices[i] < minPrice) minPrice = prices[i];
            res = Math.max(res, prices[i] - minPrice);
        }
        return res;
    }
}
```

## 买卖股票的最佳时机 II

122mid 买卖股票的最佳时机 II 【可以买卖多次】

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/

【动态规划（慢）】

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        if(len == 1) return 0;
        int[][] dp = new int[len][2];
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        for(int i = 1; i < len; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] + prices[i]);
        }
        return dp[len - 1][1];
    }
}
```

【动态规划-压缩成一维数组】

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length == 1) return 0;
        int[] dp = new int[2];
        dp[0] = -prices[0];
        dp[1] = 0;
        for(int i = 1; i < prices.length; i++) {
            dp[0] = Math.max(dp[0], dp[1] - prices[i]);
            dp[1] = Math.max(dp[1], dp[0] + prices[i]);
        }
        return dp[1];
    }
}
```

【贪心（快）】

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 1; i < prices.length; i++) {
            int diff = prices[i] - prices[i - 1];
            if(diff > 0) res += diff;
        }
        return res;
    }
}
```

## 买卖股票的最佳时机 III

123hard 买卖股票的最佳时机 III

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/

【dp[i] [j]中的i表示第i天，j为[0-4]五个状态，dp[i] [j]表示第i天状态j所剩最大现金】

【0:没有操作 1:第一次买入 2:第一次卖出 3:第二次买入 4:第二次卖出】

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        int[][] dp = new int[len][5];
        //状态 0:没有操作 1:第一次买入 2:第一次卖出 3:第二次买入 4:第二次卖出
        dp[0][1] = -prices[0];
        dp[0][3] = -prices[0];
        for(int i = 1; i < len; i++) {
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
            dp[i][2] = Math.max(dp[i - 1][2], dp[i - 1][1] + prices[i]);
            dp[i][3] = Math.max(dp[i - 1][3], dp[i - 1][2] - prices[i]);
            dp[i][4] = Math.max(dp[i - 1][4], dp[i - 1][3] + prices[i]);
        }
        return dp[len - 1][4];
    }
}
```

## 买卖股票的最佳时机 IV

188hard 买卖股票的最佳时机 IV 【和123类似，但是可以k次交易】

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/

【ck 慢】

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int len = prices.length;
        if(len == 1) return 0;
        int[][] dp = new int[len][k * 2 + 1];
        for(int i = 0; i <= k * 2; i++) {
            if(i % 2 == 1) dp[0][i] = -prices[0];
        }
        for(int i = 1; i < len; i++) {
            for(int j = 1; j <= k * 2; j++) {
                if(j % 2 == 1) dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - 1] - prices[i]);
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - 1] + prices[i]);
            }
        }
        return dp[len - 1][k * 2];
    }
}
```

【快】

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int len = prices.length;
        int[][] dp = new int[len][k * 2 + 1];
        for(int i = 1; i < k * 2; i += 2) {
            dp[0][i] = -prices[0];
        }
        for(int i = 1; i < len; i++) {
            for(int j = 0; j <= k * 2 - 2; j += 2) { //j+1表示买入，j+2表示卖出
                dp[i][j + 1] = Math.max(dp[i - 1][j + 1], dp[i - 1][j] - prices[i]);
                dp[i][j + 2] = Math.max(dp[i - 1][j + 2], dp[i - 1][j + 1] + prices[i]);
            }
        }
        return dp[len - 1][k * 2];
    }
}
```

## 最佳买卖股票时机含冷冻期

309mid 最佳买卖股票时机含冷冻期

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/

【0:当天持有股票：前一天持有、前一天不持有今天买入、前一天为冷冻期今天买入 

​	1:当天不持有股票(不包含卖出/冷冻期)：前一天持有、前一天为冷冻期

​	2:当天卖出股票 

​	3:当天为冷冻期】

<img src="E:\2021java\Typora\typora-user-images\image-20221123110354104.png" alt="image-20221123110354104" style="zoom: 50%;" />

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len = prices.length;
        //0:持有股票 1:不持有股票(不包含卖出/冷冻期) 2:当天卖出股票 3:当天为冷冻期
        int[][] dp = new int[len][4]; //dp[i][j]表示第i天状态为j的时候，手持现金数
        dp[0][0] = -prices[0];
        for(int i = 1; i < len; i++) {            
            dp[i][0] = Math.max(dp[i - 1][0], Math.max(dp[i - 1][3] - prices[i], dp[i - 1][1] - prices[i]));
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][3]);
            dp[i][2] = dp[i - 1][0] + prices[i];
            dp[i][3] = dp[i - 1][2];
        }
        return Math.max(dp[len - 1][1], Math.max(dp[len - 1][2], dp[len - 1][3]));//手上没有股票的时候
    }
}
```

## 买卖股票的最佳时机含手续费

714mid 买卖股票的最佳时机含手续费

https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

【动态规划，和“买卖股票的最佳时机 II”基本一样，但是卖出时要算上手续费】

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int len = prices.length;
        //0:持有股票 1:不持有股票
        int[][] dp = new int[len][2];
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        for(int i = 1; i < len; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] + prices[i] - fee);
        }
        return dp[len - 1][1];
    }
}
```

【压缩成一维数组（快一点）】

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int len = prices.length;
        //0:持有股票 1:不持有股票
        int[] dp = new int[2];
        dp[0] = -prices[0];
        dp[1] = 0;
        for(int i = 1; i < len; i++) {
            dp[0] = Math.max(dp[0], dp[1] - prices[i]);
            dp[1] = Math.max(dp[1], dp[0] + prices[i] - fee);
        }
        return Math.max(dp[0], dp[1]);
    }
}
```

## 最长递增子序列

300mid 最长递增子序列 【动态规划】【序列可以不为连续数字，没有延续性，用两层for循环】

https://leetcode.cn/problems/longest-increasing-subsequence/

【dp[i]的定义：dp[i]表示i之前**包括i的以nums[i]结尾**最长上升子序列的长度】

【状态转移方程：位置i的最长升序子序列等于j从0到i-1各个位置的最长升序子序列 + 1 的最大值

if(nums[j] < nums[i]) dp[i] = max(dp[i], dp[j] + 1);】

【dp[i]的初始化：每一个i，对应的dp[i]（即最长上升子序列）起始大小至少都是1】

<img src="https://img-blog.csdnimg.cn/20210110170945618.jpg" alt="300.最长上升子序列" style="zoom: 50%;" />

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int res = 1;
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        for(int i = 1; i < nums.length; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[j] < nums[i]) dp[i] = Math.max(dp[i], dp[j] + 1);
            }
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

## 最长连续递增序列

674easy 最长连续递增序列 【与“最长递增子序列”类似，区别是求连续的】【动态规划】

https://leetcode.cn/problems/longest-continuous-increasing-subsequence/

【dp[i]：以下标i为结尾的数组的连续递增的子序列长度为dp[i]】

【确定递推公式：如果 nums[i + 1] > nums[i]，那么以 i+1 为结尾的数组的连续递增的子序列长度 一定等于 以i为结尾的数组的连续递增的子序列长度 + 1，只用一层for循环

dp[i + 1] = dp[i] + 1】

```
dp[i] = dp[i - 1] + 1不能写成dp[i] = Math.max(dp[i], dp[i - 1] + 1);
因为题目求的是连续数组，所以一旦遇到不满足if(nums[i] > nums[i - 1])的情况，直接保留dp[i] = 1即可
```

【dp[i]的初始化：每一个i，对应的dp[i]（即最长上升子序列）起始大小至少都是1】

```java
class Solution {
    public int findLengthOfLCIS(int[] nums) {
        int res = 1;
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        for(int i = 1; i < nums.length; i++) {
            if(nums[i] > nums[i - 1]) dp[i] = dp[i - 1] + 1;
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

## 最长重复子数组

718mid 最长重复子数组 【求子数组，即是求连续子序列】【动态规划】

https://leetcode.cn/problems/maximum-length-of-repeated-subarray/

【dp[i] [j] ：以下标i - 1为结尾的A，和以下标j - 1为结尾的B，最长重复子数组长度为dp[i] [j]。 （特别注意： “以下标i - 1为结尾的A” 标明一定是 以A[i-1]为结尾的字符串 ）】

【递推公式：A[i - 1] 和B[j - 1]相等的时候，dp[i] [j] = dp[i - 1] [j - 1] + 1，根据递推公式可以看出，遍历i 和 j 要从1开始】

【初始化为0】

```java
class Solution {
    public int findLength(int[] nums1, int[] nums2) {
        int res = 0;
        int len1 = nums1.length;
        int len2 = nums2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 1; i <= len1; i++) {
            for(int j = 1; j <= len2; j++) {
                if(nums1[i - 1] == nums2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                res = Math.max(res, dp[i][j]);
            }
        }
        return res;
    }
}
```

## 最长公共子序列

1143mid 最长公共子序列 【不用连续】

https://leetcode.cn/problems/longest-common-subsequence/

【dp[i] [j]：长度为[0, i - 1]的字符串text1与长度为[0, j - 1]的字符串text2的最长公共子序列为dp[i] [j]】

【递推公式：主要就是两大情况： text1[i - 1] 与 text2[j - 1]相同，text1[i - 1] 与 text2[j - 1]不相同

如果text1[i - 1] 与 text2[j - 1]相同，那么找到了一个公共元素，所以dp[i] [j] = dp[i - 1] [j - 1] + 1;

如果text1[i - 1] 与 text2[j - 1]不相同，那就看看text1[0, i - 2]与text2[0, j - 1]的最长公共子序列 和 text1[0, i - 1]与text2[0, j - 2]的最长公共子序列，取最大的。

即：dp[i] [j] = max(dp[i - 1] [j], dp[i] [j - 1]);】

```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        char[] t1 = text1.toCharArray();
        char[] t2 = text2.toCharArray();
        int len1 = t1.length;
        int len2 = t2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(t1[i - 1] == t2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[len1][len2];
    }
}
```

## 不相交的线

1035mid 不相交的线 【和“最长公共子序列”一样】

https://leetcode.cn/problems/uncrossed-lines/

```java
class Solution {
    public int maxUncrossedLines(int[] nums1, int[] nums2) {
        int len1 = nums1.length;
        int len2 = nums2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(nums1[i - 1] == nums2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[len1][len2];
    }
}
```

## 最大子序和(贪心/动规)

53mid 最大子数组和 

https://leetcode.cn/problems/maximum-subarray/

【动态规划 ck 快】

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int len = nums.length;        
        int[] dp = new int[len];
        dp[0] = nums[0];
        int res = nums[0];
        for(int i = 1; i < len; i++) {
            if(dp[i - 1] < 0) dp[i] = nums[i];
            else dp[i] = dp[i - 1] + nums[i];
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

【动态规划（慢）】

【dp[i]：包括下标i之前的最大连续子序列和为dp[i]】

【递推公式：dp[i]只有两个方向可以推出来

1.dp[i - 1] + nums[i]，即：nums[i]加入当前连续子序列和

2.nums[i]，即：从头开始计算当前连续子序列和

所以：dp[i] = max(dp[i - 1] + nums[i], nums[i])】

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int res = dp[0];
        for(int i = 1; i < nums.length; i++) {
            dp[i] = Math.max(dp[i - 1] + nums[i], nums[i]);//加入当前连续子序列和/从头开始计算当前连续子序列和
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

【动态规划，将dp数组压缩为一个变量dp，滚动赋值（快）】

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int dp = 0;
        int res = nums[0];
        for(int i = 0; i < nums.length; i++) {
            dp = Math.max(dp + nums[i], nums[i]);//加入当前连续子序列和/从头开始计算当前连续子序列和
            res = Math.max(res, dp);
        }
        return res;
    }
}
```

【贪心（快）】

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];
        int count = nums[0];
        for(int i = 1; i < nums.length; i++) {
            //如果前面的累加值为负数，则重置为当前值
            if(count < 0) count = nums[i];
            //如果前面的累加值非负数，则加上当前值
            else count += nums[i];
            res = Math.max(res, count); //比较出最大值
        }
        return res;
    }
}
```

## 判断子序列

392easy 判断子序列 【动态规划，参考“不相交的线”】

https://leetcode.cn/problems/is-subsequence/

【dp[i] [j] 表示以下标i-1为结尾的字符串s，和以下标j-1为结尾的字符串t，相同子序列的长度为dp[i] [j]】

【递推公式 情况一：charS[i - 1] == charT[j - 1]】

```java
if(charS[i - 1] == charT[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
```

【递推公式 情况二：charS[i - 1] != charT[j - 1]，就是字符串t的charT[j - 1]元素可以剔除，那么dp[i][j] 的数值就是 看s[i - 1]与 t[j - 2]的比较结果，即：dp[i] [j] = dp[i] [j - 1]】

```java
else dp[i][j] = dp[i][j - 1];
```

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        char[] charS = s.toCharArray();
        char[] charT = t.toCharArray();
        int count = 0;
        int len1 = charS.length;
        int len2 = charT.length;
        int[][] dp = new int[len1 + 1][len2 + 2];
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(charS[i - 1] == charT[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = dp[i][j - 1];//不用Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return dp[len1][len2] == len1;
    }
}
```

## 不同的子序列(编辑距离)

115hard 不同的子序列

https://leetcode.cn/problems/distinct-subsequences/

【dp[i] [j]：以i-1为结尾的s子序列中出现以j-1为结尾的t的个数为dp[i] [j]】

【递推公式】

【**当s[i - 1] 与 t[j - 1]相等时**，dp[i] [j]可以有两部分组成

**一部分是用s[i - 1]来匹配**，那么个数为dp[i - 1] [j - 1]

**一部分是不用s[i - 1]来匹配**，个数为dp[i - 1] [j] （删除s[i - 1]，但这里不需要删除操作，只是不用这个来匹配）

**当s[i - 1] 与 t[j - 1]不相等时**，dp[i] [j] = dp[i - 1] [j]

所以dp[i] [j] = dp[i - 1] [j - 1] + dp[i - 1] [j];】

【初始化：

dp[i] [0]一定都是1，因为也就是把以i-1为结尾的s，删除所有元素，出现空字符串的个数就是1

dp[0] [j]一定都是0，s如论如何也变成不了t】

```java
class Solution {
    public int numDistinct(String s, String t) {
        char[] charS = s.toCharArray();
        char[] charT = t.toCharArray();
        int len1 = charS.length;
        int len2 = charT.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 0; i < dp.length; i++) {
            dp[i][0] = 1;
        }
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(charS[i - 1] == charT[j - 1]) dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                else dp[i][j] = dp[i - 1][j]; //s[i - 1]与t[j - 1]不相等，删除s[i - 1]
            }
        }
        return dp[len1][len2];
    }
}
```

## 两个字符串的删除操作(编辑距离)

583mid 两个字符串的删除操作

https://leetcode.cn/problems/delete-operation-for-two-strings/

【思路一：和“最长公共子序列”基本一样，求出公共子序列的最大长度，然后分别用两个字符串减去公共字符串得到的值相加 return (len1 - dp[len1] [len2]) + (len2 - dp[len1] [len2]);】

```java
class Solution {
    public int minDistance(String word1, String word2) {
        char[] char1 = word1.toCharArray();
        char[] char2 = word2.toCharArray();
        int len1 = char1.length;
        int len2 = char2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(char1[i - 1] == char2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return (len1 - dp[len1][len2]) + (len2 - dp[len1][len2]);
    }
}
```

【思路二：用编辑距离的思路解决】

```java
class Solution {
    public int minDistance(String word1, String word2) {
        char[] w1 = word1.toCharArray();
        char[] w2 = word2.toCharArray();
        int len1 = w1.length;
        int len2 = w2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 0; i <= len1; i++) {
            dp[i][0] = i;
        }
        for(int j = 0; j <= len2; j++) {
            dp[0][j] = j;
        }
        for(int i = 1; i <= len1; i++) {
            for(int j = 1; j <= len2; j++) {
                if(w1[i - 1] == w2[j - 1]) dp[i][j] = dp[i - 1][j - 1];
                else dp[i][j] = Math.min(dp[i - 1][j] + 1, Math.min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 2));
            }
        }
        return dp[len1][len2];
    }
}
```



## 编辑距离

72hard 编辑距离 【操作数，包括删除、添加、替换】

https://leetcode.cn/problems/edit-distance/

【定义：dp[i] [j] 表示以下标i-1为结尾的字符串word1，和以下标j-1为结尾的字符串word2，最近编辑距离为dp[i] [j]】

【递推公式】

```java
if (word1[i - 1] == word2[j - 1])
    不操作
if (word1[i - 1] != word2[j - 1])
    增
    删
    换
```

<img src="E:\2021java\Typora\typora-user-images\image-20221124111218963.png" alt="image-20221124111218963" style="zoom: 25%;" />

递推公式如下

```java
if(char1[i - 1] == char2[j - 1]) dp[i][j] = dp[i - 1][j - 1];
else dp[i][j] = Math.min(dp[i - 1][j] + 1, Math.min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 1));
```

初始化

```java
for(int i = 0; i < len1 + 1; i++) dp[i][0] = i;
for(int j = 0; j < len2 + 1; j++) dp[0][j] = j;
```

```java
class Solution {
    public int minDistance(String word1, String word2) {
        char[] char1 = word1.toCharArray();
        char[] char2 = word2.toCharArray();
        int len1 = char1.length;
        int len2 = char2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 0; i < len1 + 1; i++) dp[i][0] = i;
        for(int j = 0; j < len2 + 1; j++) dp[0][j] = j;
        for(int i = 1; i < len1 + 1; i++) {
            for(int j = 1; j < len2 + 1; j++) {
                if(char1[i - 1] == char2[j - 1]) dp[i][j] = dp[i - 1][j - 1];
                else dp[i][j] = Math.min(dp[i - 1][j] + 1, Math.min(dp[i][j - 1] + 1, dp[i - 1][j - 1] + 1));
            }
        }
        return dp[len1][len2];
    }
}
```

##  回文子串

647mid 回文子串  【回文字符串 是正着读和倒过来读一样的字符串】 【5.最长回文子串】

https://leetcode.cn/problems/palindromic-substrings/

【子字符串 是字符串中的由**连续**字符组成的一个序列】

【动态规划】

【确定dp数组（dp table）以及下标的含义：布尔类型的dp[i] [j]：表示区间范围[i,j] （注意是左闭右闭）的子串是否是回文子串，如果是dp[i] [j]为true，否则为false】

【递推公式：

1.当s[i]与s[j]不相等，那没啥好说的了，dp[i] [j]一定是false

2.当s[i]与s[j]相等时，有以下三种情况

情况一：下标i 与 j相同，同一个字符例如a，当然是回文子串 （j - i == 0）

情况二：下标i 与 j相差为1，例如aa，也是回文子串 （j - i == 1）

情况三：下标：i 与 j相差大于1的时候，例如cabac，此时s[i]与s[j]已经相同了，我们看i到j区间是不是回文子串就看aba是不是回文就可以了，那么aba的区间就是 i+1 与 j-1区间，这个区间是不是回文就看dp[i + 1] [j - 1]是否为true】

【dp[i] [j]初始化为false】

【遍历顺序，dp[i] [j]依赖于dp[i + 1] [j - 1]，所以 i 倒序遍历，j 顺序遍历】

```java
class Solution {
    public int countSubstrings(String s) {
        int res = 0;
        char[] ch = s.toCharArray();
        boolean[][] dp = new boolean[ch.length][ch.length];
        for(int i = ch.length - 1; i >= 0; i--) {
            for(int j = i; j < ch.length; j++) {
                if(ch[i] == ch[j]) {
                    if(j - i <= 1) { //情况一、情况二
                        res++;
                        dp[i][j] = true;
                    } else if(dp[i + 1][j - 1]) { //情况三
                        res++;
                        dp[i][j] = true;
                    }
                }
            }
        }
        return res;
    }
}
```

## 最长回文子串

5mid 最长回文子串 

https://leetcode.cn/problems/longest-palindromic-substring/

【dp数组和递推公式与647回文子串基本一样，记录回文子串的最大长度，并记录下标start和end】

```java
class Solution {
    public String longestPalindrome(String s) {
        char[] ch = s.toCharArray();
        int len = ch.length;
        int lenString = 0;
        int start = 0, end = 0;
        boolean[][] dp = new boolean[len][len];
        for(int i = len - 1; i >= 0; i--) {
            for(int j = i; j < len; j++) {
                if(ch[i] == ch[j]) {
                    if(j - i <= 1) {
                        dp[i][j] = true;
                        if(lenString < j - i + 1) {
                            lenString = j - i + 1;
                            start = i;
                            end = j;
                        }
                    } else if(dp[i + 1][j - 1]) {
                        dp[i][j] = true;
                        if(lenString < j - i + 1) {
                            lenString = j - i + 1;
                            start = i;
                            end = j;
                        }
                    }
                }
            }
        }
        return s.substring(start, end + 1);
    }
}
```



## 最长回文子序列

516mid 最长回文子序列 【回文子串是要连续的，回文子序列可不是连续的，本题是可以不连续】

https://leetcode.cn/problems/longest-palindromic-subsequence/

【dp[i] [j]：字符串s在[i, j]范围内最长的回文子序列的长度为dp[i] [j]】

【递推公式：如果s[i]与s[j]相同，那么dp[i] [j] = dp[i + 1] [j - 1] + 2】

<img src="E:\2021java\Typora\typora-user-images\20210127151350563.jpg" alt="516.最长回文子序列" style="zoom:50%;" />

【递推公式：如果s[i]与s[j]不相同，说明s[i]和s[j]的同时加入 并不能增加[i,j]区间回文子串的长度，那么分别加入s[i]、s[j]看看哪一个可以组成最长的回文子序列

1.加入s[j]的回文子序列长度为dp[i + 1] [j]

2.加入s[i]的回文子序列长度为dp[i] [j - 1]

那么dp[i] [j]一定是取最大的，即：dp[i] [j] = max(dp[i + 1] [j], dp[i] [j - 1]);】

<img src="E:\2021java\Typora\typora-user-images\20210127151420476.jpg" alt="516.最长回文子序列1" style="zoom:50%;" />

【初始化：当i与j相同，那么dp[i] [j]一定是等于1的】

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        char[] ch = s.toCharArray();
        int len = ch.length;
        int[][] dp = new int[len][len];
        for(int i = 0; i < len; i++){
            dp[i][i] = 1;
        }
        for(int i = len - 1; i >= 0; i--) {
            for(int j = i + 1; j < len; j++) { //如果j = i开始遍历，dp[i + 1][j - 1]会越界
                if(ch[i] == ch[j]) dp[i][j] = dp[i + 1][j - 1] + 2;
                else dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
            }
        }
        return dp[0][len - 1];
    }
}
```

# 单调栈

通常是一维数组，要寻找任一个元素的右边或者左边第一个比自己大或者小的元素的位置，此时我们就要想到可以用单调栈了

## 每日温度

739mid 每日温度

https://leetcode.cn/problems/daily-temperatures/

【本题其实就是找到一个元素右边第一个比自己大的元素】

【单调栈里只需要存放元素的下标i就可以了，如果需要使用对应的元素，直接T[i]就可以获取】

<img src="E:\2021研二上\微信图片_20221210184956.jpg" alt="微信图片_20221210184956" style="zoom:50%;" />

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int len = temperatures.length;
        int[] res = new int[len];
        Deque<Integer> stack = new LinkedList<>();
        stack.push(0);
        for(int i = 1; i < len; i++) {
            //栈不为空且当前遍历到的元素比顶部元素作为下标的元素大
            while(!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                res[stack.peek()] = i - stack.peek();
                stack.pop();
            }
            stack.push(i);
        }
        return res;
    }
}
```

## 下一个更大元素 I

496easy 下一个更大元素 I 

https://leetcode.cn/problems/next-greater-element-i/

【和“每日温度”类似，要使用map存nums1的值与下标，同时判断nums2的值是否存在于nums1，如果存在，则可以存进stack，否则不存】

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        int[] res = new int[nums1.length];
        Arrays.fill(res, -1);
        for(int i = 0; i < nums1.length; i++) { //映射nums1值与下标
            map.put(nums1[i], i);
        }
        Deque<Integer> stack = new LinkedList<>(); //存nums2下标
        for(int i = 0; i < nums2.length; i++) {
            while(!stack.isEmpty() && nums2[i] > nums2[stack.peek()]) {
                int key = nums2[stack.peek()];
                res[map.get(key)] = nums2[i];
                stack.pop();
            }
            if(map.containsKey(nums2[i])) stack.push(i);
        }
        return res;
    }
}
```

## 下一个更大元素 II

503mid 下一个更大元素 II

https://leetcode.cn/problems/next-greater-element-ii/

【和“每日温度”类似，但相当于两个数组拼接起来】

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int len = nums.length;
        int[] res = new int[len];
        Arrays.fill(res, -1);
        Deque<Integer> stack = new LinkedList<>();
        for(int i = 0; i < len * 2; i++) {
            while(!stack.isEmpty() && nums[i % len] > nums[stack.peek()]) {
                res[stack.peek()] = nums[i % len];
                stack.pop();
            }
            stack.push(i % len);
        }
        return res;
    }
}
```

## 接雨水

42hard 接雨水 【要注意第一个柱子和最后一个柱子不接雨水】

https://leetcode.cn/problems/trapping-rain-water/

【求出每列所接雨水的高度：min(lHeight, rHeight) - height，其中lHeight是左侧最高柱子高度，rHeight是右侧最高柱子高度】

【双指针（慢 5%）】

```java
class Solution {
    public int trap(int[] height) {
        int res = 0;
        for(int i = 0; i < height.length; i++) {
            if(i == 0 || i == height.length - 1) continue;
            int rHeight = 0, lHeight = 0;
            for(int j = i + 1; j < height.length; j++) {
                rHeight = Math.max(rHeight, height[j]);
            }
            for(int j = i - 1; j >= 0; j--) {
                lHeight = Math.max(lHeight, height[j]);
            }
            int h = Math.min(lHeight, rHeight) - height[i];
            if(h > 0) res += h;
        }
        return res;
    }
}
```

【动态规划（快 77%）】

【不要忘了初始化maxLeft[0] = height[0] 和 maxRight[len - 1] = height[len - 1]】

```java
class Solution {
    public int trap(int[] height) {
        int res = 0;
        int len = height.length;
        int[] maxLeft = new int[len]; //当前柱子左边最大高度
        int[] maxRight = new int[len]; //当前柱子右边最大高度
        maxLeft[0] = height[0];
        for(int i = 1; i < len; i++) {
            maxLeft[i] = Math.max(height[i], maxLeft[i - 1]); //递推
        }
        maxRight[len - 1] = height[len - 1];
        for(int i = len - 2; i >= 0; i--) {
            maxRight[i] = Math.max(height[i], maxRight[i + 1]); //递推
        }
        for(int i = 0; i < len; i++) {
            if(i == 0 || i == len - 1) continue;
            int h = Math.min(maxLeft[i], maxRight[i]) - height[i];
            if(h > 0) res += h;
        }
        return res;
    }
}
```

【单调栈 （稍快 40%）】

【先将下标0的柱子加入到栈中】

【如果当前遍历的元素（柱子）高度小于栈顶元素的高度，就把这个元素加入栈中，因为栈里本来就要保持从小到大的顺序（从栈头到栈底）】

【如果当前遍历的元素（柱子）高度等于栈顶元素的高度，要跟更新栈顶元素，因为遇到相相同高度的柱子，需要使用最右边的柱子来计算宽度】

【如果当前遍历的元素（柱子）高度大于栈顶元素的高度，此时就出现凹槽了】

<img src="E:\2021java\Typora\typora-user-images\2021022309321229.png" alt="42.接雨水4" style="zoom:33%;" />

```java
class Solution {
    public int trap(int[] height) {
        int res = 0;
        Deque<Integer> stack = new LinkedList<>();
        stack.push(0);
        for(int i = 1; i < height.length; i++) {
            if(height[i] < height[stack.peek()]) stack.push(i);
            else if(height[i] == height[stack.peek()]) {
                stack.pop();
                stack.push(i);
            } else {
                while(!stack.isEmpty() && height[i] > height[stack.peek()]) {
                    int mid = stack.pop();
                    if(!stack.isEmpty()) {
                        int h = Math.min(height[stack.peek()], height[i]) - height[mid];
                        int w = i - stack.peek() - 1;
                        res += h * w;
                    }
                }
                stack.push(i);
            }
        }
        return res;
    }
}
```

## 柱状图中最大的矩形

84hard 柱状图中最大的矩形

https://leetcode.cn/problems/largest-rectangle-in-histogram/

【动态规划 （快）】

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int res = 0;
        int len = heights.length;
        int[] minLeftIndex = new int[len];// 记录每个柱子 左边第一个小于该柱子的下标
        int[] minRightIndex = new int[len];// 记录每个柱子 右边第一个小于该柱子的下标
        minLeftIndex[0] = -1;// 注意这里初始化，防止下面while死循环
        for(int i = 1; i < len; i++) {
            int t = i - 1;
            // 这里不是用if，而是不断向左寻找的过程
            while(t >= 0 && heights[t] >= heights[i]) t = minLeftIndex[t];
            minLeftIndex[i] = t;
        }
        minRightIndex[len - 1] = len;// 注意这里初始化，防止下面while死循环
        for(int i = len -2; i >= 0; i--) {
            int t = i + 1;
            // 这里不是用if，而是不断向右寻找的过程
            while(t <= len - 1 && heights[t] >= heights[i]) t = minRightIndex[t];
            minRightIndex[i] = t;
        }
        for(int i = 0; i < len; i++) {
            int h = heights[i];
            int w = minRightIndex[i] - minLeftIndex[i] - 1;
            res = Math.max(res, h * w);
        }
        return res;
    }
}
```

【单调栈 （慢）】

<img src="E:\2021java\Typora\typora-user-images\20210223155303971.jpg" alt="84.柱状图中最大的矩形" style="zoom:33%;" />

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int res = 0;
        int len = heights.length;
        int[] newHeights = new int[len + 2];
        newHeights[0] = 0;
        newHeights[len + 1] = 0;
        System.arraycopy(heights, 0, newHeights, 1, len);
        heights = newHeights;
        Deque<Integer> stack = new LinkedList<>();
        stack.push(0);
        for(int i = 1; i < heights.length; i++) {
            if(heights[i] > heights[stack.peek()]) stack.push(i);
            else if(heights[i] == heights[stack.peek()]) {
                stack.pop();
                stack.push(i);
            } else {
                while(!stack.isEmpty() && heights[i] < heights[stack.peek()]) {
                    int mid = stack.pop();
                    if(!stack.isEmpty()) {
                        int left = stack.peek();
                        int right = i;
                        int s = heights[mid] * (right - left - 1);
                        res = Math.max(res, s);
                        System.out.println(s);
                    }
                }
                stack.push(i);
            }
        }
        return res;
    }
}
```

# 深度优先搜索

## 所有可能的路径

797mid 所有可能的路径

https://leetcode.cn/problems/all-paths-from-source-to-target/

![img](https://assets.leetcode.com/uploads/2020/09/28/all_2.jpg)

展开成树形图如下

<img src="C:\Users\陈康瑞\AppData\Roaming\Typora\typora-user-images\image-20221211134452494.png" alt="image-20221211134452494" style="zoom:50%;" />

```java
class Solution {
    LinkedList<Integer> path = new LinkedList<>();
    List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        path.add(0);
        backtracking(graph, 0);
        return res;
    }
    void backtracking(int[][] graph, int x) {
        if(x == graph.length - 1) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < graph[x].length; i++) {
            path.add(graph[x][i]);
            backtracking(graph, graph[x][i]);
            path.removeLast(); //回溯
        }
    }
}
```

## 岛屿数量

200mid 岛屿数量 【深度优先搜索：纵向遍历，递归】

https://leetcode.cn/problems/number-of-islands/

![Image](E:\2021java\Typora\typora-user-images\640.png)

```java
class Solution {
    boolean isVisited[][];
    int[][] dir = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    int m = 0, n = 0; //标记grid的长度
    public int numIslands(char[][] grid) {
        m = grid.length;
        n = grid[0].length;
        isVisited = new boolean[m][n];
        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(!isVisited[i][j] && grid[i][j] == '1') {
                    res++;
                    dfs(grid, i, j); //每遍历一个点，就把周围4个点都遍历了
                }
            }
        }
        return res;
    }
    void dfs(char[][] grid, int x, int y) {
        if(isVisited[x][y]) return;
        isVisited[x][y] = true;
        for(int i = 0; i < 4; i++) {
            int nextX = x + dir[i][0];
            int nextY = y + dir[i][1];
            if(nextX < 0 || nextX >= m || nextY < 0 || nextY >= n) continue;
            if(isVisited[nextX][nextY] || grid[nextX][nextY] == '0') continue;
            dfs(grid, nextX, nextY);
        }
    }
}
```



# 广度优先搜索

## 岛屿数量

200mid 岛屿数量 【广度优先搜索，用队列表示每层，没有递归】

https://leetcode.cn/problems/number-of-islands/

![Image](E:\2021java\Typora\typora-user-images\640.png)

```java
class Solution {
    int[][] dir = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    boolean[][] isVisited; //标记访问过的节点，不要重复访问
    int res = 0;
    public int numIslands(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        isVisited = new boolean[m][n];
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(!isVisited[i][j] && grid[i][j] == '1') { //如果节点没被访问过且节点是可遍历的
                    bfs(grid, i, j);
                    res++;
                }
            }
        }
        return res;
    }
    void bfs(char[][] grid, int x, int y) {
        Deque<int[]> que = new ArrayDeque<>();
        que.addLast(new int[]{x, y});// 起始节点加入队列
        isVisited[x][y] = true;// 只要加入队列，立刻标记为访问过的节点
        while(!que.isEmpty()) {
            int[] node = que.pollFirst();// 从队列取元素
            int curX = node[0]; //横坐标
            int curY = node[1]; //纵坐标
            for(int i = 0; i < 4; i++) {
                int nextX = curX + dir[i][0]; //获取四个方向的坐标
                int nextY = curY + dir[i][1];                
                if(nextX < 0 || nextX >= grid.length || nextY < 0 || nextY >= grid[0].length) continue; //下标越界，跳过
                //如果节点没被访问过且节点是可遍历的
                if(!isVisited[nextX][nextY] && grid[nextX][nextY] == '1') {
                    isVisited[nextX][nextY] = true;// 只要加入队列立刻标记，避免重复访问
                    que.addLast(new int[]{nextX, nextY});// 队列添加该节点为下一轮要遍历的节点 
                }                
            }            
        }
    }
}
```

# 笔试准备

## 最长无重复字符序列

3mid 无重复字符的最长子串 【每次判断是否出现重复字符，如果出现，判断是否需要更新左边界start】

https://leetcode.cn/problems/longest-substring-without-repeating-characters/

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0) return 0;
        Map<Character, Integer> map = new HashMap<>();
        int res = 1; //初始化结果，最小值为1
        int start = 0;
        char[] ch = s.toCharArray();
        for(int i = 0; i < ch.length; i++) {
            if(map.containsKey(ch[i])) {
                int index = map.get(ch[i]);
                if(index >= start) start = index + 1; //如果重复字符出现在字符串中间
                // start = start; //如果重复字符在字符串外，start不用改变
            }
            map.put(ch[i], i);
            res = Math.max(res, i - start + 1); //每次遍历都更新最大长度
        }
        return res;
    }
}
```



# CodeTop

## 最长无重复字符序列

3mid 无重复字符的最长子串 【每次判断是否出现重复字符，如果出现，判断是否需要更新左边界start】

https://leetcode.cn/problems/longest-substring-without-repeating-characters/

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0) return 0;
        Map<Character, Integer> map = new HashMap<>();
        int res = 1; //初始化结果，最小值为1
        int start = 0;
        char[] ch = s.toCharArray();
        for(int i = 0; i < ch.length; i++) {
            if(map.containsKey(ch[i])) {
                int index = map.get(ch[i]);
                if(index >= start) start = index + 1; //如果重复字符出现在字符串中间
                // start = start; //如果重复字符在字符串外，start不用改变
            }
            map.put(ch[i], i); //每次遍历都要更新这个字符出现的最新位置
            res = Math.max(res, i - start + 1); //每次遍历都更新最大长度
        }
        return res;
    }
}
```



## 快排

### 双路快排

912mid 排序数组

https://leetcode.cn/problems/sort-an-array/

让等于pivot的数字平均分配到数组两侧

<img src="E:\2021java\Typora\typora-user-images\image-20221226185111230.png" alt="image-20221226185111230" style="zoom:50%;" />

<img src="E:\2021java\Typora\typora-user-images\image-20221226185150582.png" alt="image-20221226185150582" style="zoom:50%;" />

```java
import java.util.Random;

class Solution {
    private final static Random random = new Random(System.currentTimeMillis());
    public int[] sortArray(int[] nums) {
        int len = nums.length;
        quickSort(nums, 0, len - 1);
        return nums;
    }
    //递归排序
    void quickSort(int[] nums, int left, int right) {
        if(left >= right) return;
        int pivotIndex = partition(nums, left, right);
        quickSort(nums, left, pivotIndex - 1);
        quickSort(nums, pivotIndex + 1, right);
    }
    //让等于pivot的数字平均分配到数组两侧
    int partition(int[] nums, int left, int right) {
        int randomIndex = left + random.nextInt(right - left + 1); //定义随机数下标
        swap(nums, left, randomIndex);
        int pivot = nums[left];
        
        int le = left + 1; //左侧指针，寻找小于等于pivot的元素
        int ge = right; //右侧指针，寻找大于等于pivot的元素

        while(true) {
            while(le <= ge && nums[le] < pivot) {
                le++;
            }
            while(le <= ge && nums[ge] > pivot) {
                ge--;
            }
            if(le >= ge) break; //终止条件
            swap(nums, le, ge); //如果不终止，此时应该是左边找到了>=pivot的元素，右边找到了<=pivot的元素，交换
            le++; //指针右移，继续寻找
            ge--; //指针左移，继续寻找
        }
        swap(nums ,left, ge);//最后将pivot和第一个数组的最后一个元素交换，[小于pivot的元素，pivot，大于pivot的元素]
        return ge; //返回pivot的下标
    }
    //交换数组其中两个元素
    void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```



### 三路快排

912mid 排序数组

https://leetcode.cn/problems/sort-an-array/

![image-20221226220357012](E:\2021java\Typora\typora-user-images\image-20221226220357012.png)

```java
import java.util.Random;

class Solution {
    private final static Random random = new Random(System.currentTimeMillis());
    public int[] sortArray(int[] nums) {
        int len = nums.length;
        quickSort(nums, 0, len - 1);
        return nums;
    }
    //不用partition了，直接把逻辑写在quickSort
    void quickSort(int[] nums, int left, int right) {
        if(left >= right) return;
        int randomIndex = left + random.nextInt(right - left + 1);
        swap(nums, left, randomIndex);
        int pivot = nums[left];
        int lt = left + 1; //左侧指针，(left, lt)为严格小于pivot的元素，最后交换一次，就是[left, lt - 1)
        int i = left + 1; //左侧指针，遍历所有元素
        int gt = right; //右侧指针 (gt, right]为严格大于pivot的元素

        while(i <= gt) {
            if(nums[i] < pivot) {
                swap(nums, i, lt);
                i++;
                lt++;
            } else if(nums[i] == pivot) {
                i++;
            } else {
                swap(nums, i, gt);
                gt--;
            }
        }
        //此时排好的顺序为
        //[left, lt)
        //[lt, gt]
        //(gt, right]
        
        //要将left位置的元素和lt - 1位置的元素交换
        swap(nums ,left, lt - 1);
        quickSort(nums, left, lt - 2);
        quickSort(nums, gt + 1, right);
    }
    void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```

## 数组中的第K个最大元素

215mid 数组中的第K个最大元素

https://leetcode.cn/problems/kth-largest-element-in-an-array/

【排序后的数组，第K个最大元素的下标是nums.length - k】

【利用快排，找出每次排序的pivotIndex，与target进行对比】

```java
import java.util.Random;
class Solution {
    private final static Random random = new Random(System.currentTimeMillis());
    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        int target = len - k;
        int left = 0, right = len - 1;
        while(true) {
            int pivotIndex = partition(nums, left, right);
            if(pivotIndex < target) left = pivotIndex + 1; //更新left边界
            else if(pivotIndex > target) right = pivotIndex - 1; //更新right边界
            else return nums[pivotIndex];
        }
    }
    
    //双路快排
    int partition(int[] nums, int left, int right) {
        int randomIndex = left + random.nextInt(right - left + 1);
        swap(nums, left, randomIndex);
        int pivot = nums[left];
        int le = left + 1;
        int ge = right;

        while(true){
            while(le <= ge && nums[le] < pivot) { //nums[le] < pivot
                le++;
            }
            while(le <= ge && nums[ge] > pivot) { //nums[ge] > pivot
                ge--;
            }
            if(le >= ge) break;
            swap(nums, le, ge);
            le++;
            ge--;
        }
        swap(nums, left, ge);
        return ge;
    }
    void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```

## 字符串相加

415easy 字符串相加

https://leetcode.cn/problems/add-strings/

【倒序对每位数字相加，插入到sb，最后再返回sb.reverse().toString()】

<img src="E:\2021java\Typora\typora-user-images\image-20221228021258685.png" alt="image-20221228021258685" style="zoom:50%;" />

```java
class Solution {
    public String addStrings(String num1, String num2) {
        StringBuilder sb = new StringBuilder();
        int tmp = 0; //表示对应位数字相加，加进位的和
        int carry = 0; //表示进位
        int i = num1.length() - 1;
        int j = num2.length() - 1;
        while(i >= 0 || j >= 0) {
            int n1 = 0;
            int n2 = 0;
            int res = 0;
            if(i >= 0) n1 = num1.charAt(i) - '0';
            if(j >= 0) n2 = num2.charAt(j) - '0';
            tmp = n1 + n2 + carry;
            carry = tmp / 10;
            res = tmp % 10;
            sb.append(res);
            i--;
            j--;
        } 
        if(carry == 1) sb.append(1); //如果最高位还有进位，要补1
        return sb.reverse().toString();
    }
}
```

## 二叉树的右视图

199mid 二叉树的右视图 【层序遍历，把每层最后一个元素（队列最后一个元素）加进结果集】

https://leetcode.cn/problems/binary-tree-right-side-view/

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        LinkedList<Integer> list = new LinkedList<>();
        Deque<TreeNode> que = new ArrayDeque<>();
        if(root != null)que.addLast(root);
        while(!que.isEmpty()) {
            int size = que.size();
            while(size-- > 0) {
                TreeNode node = que.pollFirst();                
                if(size == 0) list.add(node.val);
                if(node.left != null) que.addLast(node.left);
                if(node.right != null) que.addLast(node.right);
            }
        }
        return new ArrayList<>(list);
    }
}
```



## 合并两个有序链表

21easy 合并两个有序链表

https://leetcode.cn/problems/merge-two-sorted-lists/

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while(list1 != null && list2 != null) {
            if(list1.val <= list2.val) {
                cur.next = list1;
                list1 = list1.next;
            } else {
                cur.next = list2;
                list2 = list2.next;
            }
            cur = cur.next;
        }
        if(list1 == null) cur.next = list2;
        if(list2 == null) cur.next = list1;
        return dummyHead.next;
    }
}
```

## 排序链表

148mid 排序链表

https://leetcode.cn/problems/sort-list/

【归并（递归）】时间复杂度是O(nlogn)，空间复杂度为O(n)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode slow = head, fast = head.next;
        //找到中间点，若节点数为奇数，为中间点；若节点数为偶数，为中间左侧节点
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode tmp = slow.next; //标记第二段的头结点
        slow.next = null; //切断
        ListNode left = sortList(head);
        ListNode right = sortList(tmp);
        ListNode res = new ListNode(0);
        ListNode h = res;
        while(left != null && right != null) {
            if(left.val <= right.val) {
                h.next = left;
                left = left.next;
            } else {
                h.next = right;
                right = right.next;
            }
            h = h.next;
        }
        if(left != null) h.next = left;
        if(right != null) h.next = right;
        return res.next;
    }
}
```



【归并（迭代），遍历切割单元长度】时间复杂度O(nlogn)，空间复杂度O(1)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        if(head == null || head.next == null) return head;
        int len = 0; //链表长度
        ListNode cur = head;
        while(cur != null) {
            len++;
            cur = cur.next;
        }
        int step = 1; //最小单元长度为1
        ListNode dummyHead = new ListNode(0, head); //虚拟头结点
        while(step < len) {
            ListNode tail = dummyHead;
            cur = dummyHead.next;
            while(cur != null) {
                ListNode left = cur;
                ListNode right = cut(left, step);//按step在left处切割
                cur = cut(right, step); //按step在right处切割，相当于把left和right分出来了
                tail.next = merge(left, right);
                while(tail.next != null) {
                    tail = tail.next;
                }
            }
            step = step * 2; //单元长度乘2
        }
        return dummyHead.next;
    }
    //切割方法
    ListNode cut(ListNode start, int step) {
        step--;
        while(start != null && step > 0) {
            start = start.next;
            step--;
        }
        //当start == null时，说明step超出链表长度
        if(start == null) return null;
        //否则，没有超出长度,start停在前端末尾
        ListNode tmp = start.next;
        start.next = null; //断开
        return tmp; //返回后段
    }
    //合并方法
    ListNode merge(ListNode list1, ListNode list2) {
        ListNode dummyHead = new ListNode(0);
        ListNode cur = dummyHead;
        while(list1 != null && list2 != null) {
            if(list1.val <= list2.val) {
                cur.next = list1;
                list1 = list1.next;
            } else {
                cur.next = list2;
                list2 = list2.next;
            }
            cur = cur.next;
        }
        if(list1 == null) cur.next = list2;
        if(list2 == null) cur.next = list1;
        return dummyHead.next;
    }
}
```



【双路快排】【以中间节点作为pivot】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        return quickSort(head);
    }
    //双路快排
    ListNode quickSort(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode h1 = new ListNode(); //存<pivot元素部分
        ListNode h2 = new ListNode(); //存=pivot元素部分
        ListNode h3 = new ListNode(); //存>pivot元素部分
        ListNode t1 = h1;
        ListNode t2 = h2;
        ListNode t3 = h3;
        int pivot = getMid(head).val; //以中间节点作为pivot
        ListNode cur = head;
        while(cur != null) {
            ListNode next = cur.next;
            if(cur.val < pivot) {
                cur.next = null;
                t1.next = cur;
                t1 = t1.next;
            } else if(cur.val == pivot) {
                cur.next = null;
                t2.next = cur;
                t2 = t2.next;
            } else {
                cur.next = null;
                t3.next = cur;
                t3 = t3.next;
            }
            cur = next;
        }
        h1 = quickSort(h1.next); //递归
        h3 = quickSort(h3.next); //递归

        h2 = h2.next; //此时h2还是虚拟头结点，h2.next才是这一段的头结点
        t2.next = h3; //第2,3段连接
        if(h1 == null) { //如果第一段为null
            return h2;
        } else { //如果第一段不为null，要重新寻找t1，因为有可能t1是null            
            t1 = h1;
            while(t1.next != null) {
                t1 = t1.next;
            }
            t1.next = h2;
            return h1;
        }        
    }
    //寻找中间节点
    ListNode getMid(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode slow = head;
        ListNode fast = head.next;
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```

## 删除排序链表中的重复元素 II

82mid 删除排序链表中的重复元素 II 【前后指针pre和cur，始终保持pre.next = cur】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummyHead = new ListNode(0, head);
        ListNode pre = dummyHead;
        ListNode cur = pre.next;
        while(cur != null && cur.next != null) {
            //进入这个循环，说明cur和cur.next值不同，顺序移动pre和cur，再continue即可
            if(cur.next.val != cur.val)  {
                pre = pre.next;
                cur = pre.next;
                continue;
            }
            //没有进入上面的if，说明有重复元素
            while(cur.next != null && cur.val == cur.next.val) {//因为在循环，所以要判断cur.next
                cur = cur.next;
                pre.next = cur.next;
            }
            cur = pre.next;
            
        }
        return dummyHead.next;
    }
}
```

## 下一个更大元素 III

556mid 下一个更大元素 III 【单调栈解决】

https://leetcode.cn/problems/next-greater-element-iii/

```
利用单调栈解决；

如: 1236541;
从末尾遍历到3 (i=2)时，栈内不再递增，栈为[1,4,5,6[; 右边是栈顶
开始出栈，依次出栈6,5,4. 此时count=3;
交换3(i=2)和4(i=2+3=5)，得到1246531；
从3(i=2)开始 reverse剩余数字；124-6531 => 124-1356;

返回1241356；
```

```java
class Solution {
    public int nextGreaterElement(int n) {
        String str = String.valueOf(n);
        char[] ch = str.toCharArray();
        int len = ch.length;
        int count = 0;
        Deque<Integer> stack = new LinkedList<>();
        stack.push(len - 1);
        for(int i = len - 2; i >= 0; i--) {
            if(ch[i] >= ch[stack.peek()]) stack.push(i); //要用>=
            else{
                while(!stack.isEmpty() && ch[i] < ch[stack.peek()]) {
                    stack.pop();
                    count++;
                }
                char temp = ch[i + count];
                ch[i + count] = ch[i];
                ch[i] = temp;
                int left = i + 1, right = len - 1;
                while(left < right) {
                    char temp1 = ch[right];
                    ch[right] = ch[left];
                    ch[left] = temp1;
                    left++;
                    right--;
                }
                break;
            }        
        }
        long res = Long.parseLong(String.valueOf(ch));
        if(res > Integer.MAX_VALUE) return -1;
        if(res > n) return (int)res;
        return -1;
    }
}
```



## 下一个排列

31mid 下一个排列 【和“556mid 下一个更大元素 III”原理一样，但是可以不用单调栈来做】

https://leetcode.cn/problems/next-permutation/

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int start = 0, end = 0;
        for(int i = nums.length - 1; i > 0; i--) {
            if(nums[i - 1] >= nums[i]) continue;
            else {
                start = i - 1;
                break;
            }
        }
        for(int i = nums.length - 1; i > start; i--) {
            if(nums[i] > nums[start]) {
                end = i;
                break;
            }
        }
        if(start == 0 && end == 0) {
            reverse(nums, 0, nums.length - 1);
            return;
        }
        swap(nums, start, end);
        reverse(nums, start + 1, nums.length - 1);
    }
    void swap(int[]nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
    void reverse(int[] nums, int left, int right) {
        while(left < right) {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}
```

## 两数相加

2mid 两数相加

https://leetcode.cn/problems/add-two-numbers/

【构建虚拟头结点，返回一个新的头结点】

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(-1); //构建虚拟头结点
        ListNode cur = dummyHead;
        ListNode cur1 = l1;
        ListNode cur2 = l2;
        int carry = 0; //进位
        int sum = 0;
        while(cur1 != null || cur2 != null) {            
            if(cur1 == null) sum = cur2.val + carry;
            else if(cur2 == null) sum = cur1.val + carry;
            else sum = cur1.val + cur2.val + carry;
            int sub = sum % 10;
            if(sub == sum) carry = 0; //余数等于本身，说明没有进位
            else carry = 1; //余数不等于本身，说明进位
            cur.next = new ListNode(sub);
            cur = cur.next;
            if(cur1 != null) cur1 = cur1.next;
            if(cur2 != null) cur2 = cur2.next;            
        }
        
        if(carry == 1) cur.next = new ListNode(1);
        return dummyHead.next;
    }
}
```

## 反转每对括号间的子串

1190mid 反转每对括号间的子串

https://leetcode.cn/problems/reverse-substrings-between-each-pair-of-parentheses/

【预处理括号，时间复杂度为O(n)】

```java
class Solution {
    public String reverseParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        int len = s.length();
        int[] pair = new int[len]; //构建数组存储括号下标映射
        for(int i = 0; i < len; i++) {
            if(s.charAt(i) == '(') {
                stack.push(i);
            }
            if(s.charAt(i) == ')') {
                int j = stack.pop();
                pair[i] = j;
                pair[j] = i;
            }
        }
        StringBuilder sb = new StringBuilder();
        int index = 0;
        int dir = 1;//初始化指针遍历方向，向右遍历
        while(index < len) {
            if(s.charAt(index) == '(' || s.charAt(index) == ')') { //遇到括号
                index = pair[index]; //指针跳变到对应的括号位置
                dir = -dir; //改变指针遍历方向
            } else {
                sb.append(s.charAt(index));
            }
            index += dir;
        }
        return sb.toString();
    }
}
```

【栈遍历，时间复杂度为O(n2)】

```java
class Solution {
    public String reverseParentheses(String s) {
        Deque<String> stack = new ArrayDeque<>();
        StringBuilder sb = new StringBuilder();
        char[] ch = s.toCharArray();
        for(char c : ch) {
            if(c == '(') { //遇到'('
                stack.push(sb.toString()); //存到的字符串压入栈
                sb.delete(0, sb.length()); //再清空字符串变量
            } else if(c == ')') { //遇到')'
                sb.reverse(); //将现在字符串变量反转
                sb.insert(0, stack.pop()); //再弹出栈顶元素插在字符串前面
            } else {
                sb.append(c); //都不是，就将字符存入字符串sb
            }
        }
        return sb.toString();
    }
}
```



## 字符串转换整数 (atoi)

8mid 字符串转换整数 (atoi)

https://leetcode.cn/problems/string-to-integer-atoi/

【注意判断溢出】【先把前导空格去掉】【如果首位不是正负号或者数字，随后又有数字，就返回0】

```java
class Solution {
    public int myAtoi(String s) {
        StringBuilder sb = new StringBuilder();
        char[] ch = s.toCharArray();
        int start = 0;
        int sign = 1;
        for(int i = 0; i < ch.length; i++) {
            if(ch[i] != ' ') {
                start = i;
                break;
            }
        }
        for(int i = start; i < ch.length; i++) {
            if(i == start && ch[i] == '-') {
                sign = -1;
            } else if(i == start && ch[i] == '+') {
                sign = 1;
            } else if(ch[i] <= '9' && ch[i] >= '0') {
                sb.append(ch[i]);
                if(sign * Long.parseLong(sb.toString()) >= Integer.MAX_VALUE) return Integer.MAX_VALUE;
                if(sign * Long.parseLong(sb.toString()) <= Integer.MIN_VALUE) return Integer.MIN_VALUE;
            } else {
                break;
            }
        }
        if (sb.length() == 0) return 0;
        return sign * Integer.valueOf(sb.toString());
    }
}
```



## 链表中倒数第k个节点

剑指 Offer 22easy. 链表中倒数第k个节点 【和“19mid 删除链表的倒数第N个结点”基本一样】

https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode dummyHead = new ListNode(0, head);
        ListNode fast = dummyHead;
        while(k-- > 0) {
            fast = fast.next;
        }
        ListNode slow = dummyHead;
        while(fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }
        return slow.next;
    }
}
```

## 比较版本号

165mid 比较版本号 

https://leetcode.cn/problems/compare-version-numbers/

【split按“.”分割成字符串数组，双指针遍历比较】

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        String[] arr1 = version1.split("\\.");
        String[] arr2 = version2.split("\\.");
        int len1 = arr1.length;
        int len2 = arr2.length;
        int i = 0, j = 0;
        while(i < len1 && j < len2) {
            if(Integer.parseInt(arr1[i]) > Integer.parseInt(arr2[j])) return 1;
            if(Integer.parseInt(arr1[i]) < Integer.parseInt(arr2[j])) return -1;
            i++;
            j++;
        }
        while(i < len1) {
            if(Integer.parseInt(arr1[i]) > 0) return 1;
            i++;
        }
        while(j < len2) {
            if(Integer.parseInt(arr2[j]) > 0) return -1;
            j++;
        }
        return 0;
    }
}
```

【直接双指针在两个字符串内遍历，遇到小数点就停止，比较两个小数点之间字符串对应的整型数（快）】

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        int len1 = version1.length();
        int len2 = version2.length();
        int i = 0, j = 0;
        while(i < len1 || j < len2) {
            int x = 0;
            while(i < len1 && version1.charAt(i) != '.') { //遇到小数点就停止
                x = x * 10 + version1.charAt(i) - '0'; //更新字符串对应整型数
                i++;
            }
            int y = 0;
            while(j < len2 && version2.charAt(j) != '.') { //遇到小数点就停止
                y = y * 10 + version2.charAt(j) - '0'; //更新字符串对应整型数
                j++;
            }
            if(x > y) return 1;
            if(x < y) return -1;
            i++;
            j++;
        }
        return 0;
    }
}
```



## 字符串相乘

43mid 字符串相乘

https://leetcode.cn/problems/multiply-strings/ 

【将num2的每位数字单独拆分出来和num1相乘，再利用字符串相加的方法将每次相乘结果加起来】

```java
//提取字符数组一定要 - '0'
if(i >= 0) n1 = num1.charAt(i) - '0';
if(j >= 0) n2 = num2.charAt(j) - '0';
```

```java
class Solution {
    public String multiply(String num1, String num2) {
        if(num1.equals("0") || num2.equals("0")) return "0";
        int len1 = num1.length();
        int len2 = num2.length();
        String res = "0"; //初始化结果
        for(int i = len2 - 1; i >= 0; i--) { 
            StringBuilder sb = new StringBuilder();
            for(int j = len2 - 1; j > i; j--) {
                sb.append(0); //当前乘数num2[i] * num1结果末尾补0
            }
            int n2 = num2.charAt(i) - '0';
            int carry = 0;
            for(int j = len1 - 1; j >= 0; j--) {                
                int n1 = num1.charAt(j) - '0';
                int mul = n2 * n1 + carry;
                sb.append(mul % 10);
                carry = mul / 10;
            }
            if(carry > 0) sb.append(carry);
            res = addStrings(res, sb.reverse().toString());
        }
        return res;
    }
    String addStrings(String num1, String num2) {
        StringBuilder sb = new StringBuilder();
        int i = num1.length() - 1;
        int j = num2.length() - 1;
        int carry = 0;
        int sum = 0;
        while(i >= 0 || j >= 0) {
            int n1 = 0, n2 = 0;
            if(i >= 0) n1 = num1.charAt(i) - '0';
            if(j >= 0) n2 = num2.charAt(j) - '0';
            sum = n1 + n2 + carry;
            sb.append(sum % 10);
            carry = sum / 10;
            i--;
            j--;
        }
        if(carry == 1) sb.append(1);
        return sb.reverse().toString();
    }
}
```

## 最小栈

155mid 最小栈

https://leetcode.cn/problems/min-stack/submissions/

【构建辅助栈minStack】

【dataStack每次都压入数据，minStack则对比每次需要压入的数据是<=栈顶元素，如果是，就压入minStack】

```java
class MinStack {
    private Stack<Integer> dataStack;
    private Stack<Integer> minStack;

    public MinStack() {
        dataStack = new Stack<>();
        minStack = new Stack<>();
    }
    
    public void push(int val) {
        dataStack.push(val);
        if(minStack.isEmpty() || val <= minStack.peek()) minStack.push(val);
    }
    
    public void pop() {
        int x = dataStack.pop();
        if(x == minStack.peek()) minStack.pop();
    }
    
    public int top() {
        return dataStack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(val);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

## 求根节点到叶节点数字之和

129mid 求根节点到叶节点数字之和 

https://leetcode.cn/problems/sum-root-to-leaf-numbers/

【递归+回溯】

```java
class Solution {
    int res = 0;
    StringBuilder sb = new StringBuilder();
    public int sumNumbers(TreeNode root) {
        backtracking(root);
        return res;
    }
    void backtracking(TreeNode node) {
        //终止条件，遍历到叶子结点
        if(node.left == null && node.right == null) {
            sb.append(node.val);
            res += Integer.parseInt(sb.toString());
            return;
        }
        //中
        sb.append(node.val);
        //左
        if(node.left != null) {
            backtracking(node.left);
            sb.deleteCharAt(sb.length() - 1); //回溯
        }
        //右
        if(node.right != null) {
            backtracking(node.right);
            sb.deleteCharAt(sb.length() - 1); //回溯
        }
    }
}
```

【迭代（层序遍历），用两个同步的队列，一个队列存储节点，一个队列存储此时的和】

【构建双端队列ArrayDeque，其中使用offerLast、pollFirst表示双端队列】

【如果当做单端队列处理，就是用offer、poll表示添加队尾元素和弹出队首元素，但效率慢】

```java
class Solution {
    public int sumNumbers(TreeNode root) {
        int res = 0;
        Deque<TreeNode> nodeQue = new LinkedList<>();
        Deque<Integer> numQue = new LinkedList<>();
        nodeQue.offerLast(root);
        numQue.offerLast(root.val);
        while(!nodeQue.isEmpty()) {
            TreeNode node = nodeQue.pollFirst();//弹出队列首部节点
            int num = numQue.pollFirst();//弹出队列首部节点
            if(node.left == null && node.right == null) res += num; //如果是叶子结点，加到res
            if(node.left != null) { //继续向左子节点更新和值
                nodeQue.offerLast(node.left);
                numQue.offerLast(num * 10 + node.left.val);
            }
            if(node.right != null) { //继续向右子节点更新和值
                nodeQue.offerLast(node.right);
                numQue.offerLast(num * 10 + node.right.val);
            }
        }
        return res;
    }
}
```



## 二叉树的直径

543easy 二叉树的直径 【不一定是从父节点到子节点的顺序路径】

https://leetcode.cn/problems/diameter-of-binary-tree/

![image-20230105012226269](E:\2021java\Typora\typora-user-images\image-20230105012226269.png)

```java
class Solution {
    int res = 0; //当前节点的两侧最高子树节点+本节点所得的的节点数
    public int diameterOfBinaryTree(TreeNode root) {
        getDepth(root);
        return res - 1; //求的是连接线的数目
    }
    int getDepth(TreeNode root) {
        if(root == null) return 0;
        int leftHeight = getDepth(root.left);
        int rightHeight = getDepth(root.right);
        res = Math.max(res, leftHeight + rightHeight + 1); //更新路径串起来的节点数
        return Math.max(leftHeight, rightHeight) + 1; //返回当前节点的高度
    }
}
```



## 二叉树中的最大路径和

124hard 二叉树中的最大路径和 【和“543easy 二叉树的直径”类似】

https://leetcode.cn/problems/binary-tree-maximum-path-sum/

```java
class Solution {
    int res = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        getMax(root);
        return res;
    }
    int getMax(TreeNode root) {
        if(root == null) return 0;
        int leftMax = Math.max(getMax(root.left), 0);   //如果大于0，就取这个值和root.val相加
        int rightMax = Math.max(getMax(root.right), 0); //如果大于0，就取这个值和root.val相加
        res = Math.max(res, leftMax + rightMax + root.val); //实时更新最大值
        return Math.max(leftMax, rightMax) + root.val; //返回当前从叶子结点到当前节点所能累计的最大值
    }
}
```



## 最小路径和

64mid 最小路径和 【动态规划 dp[] []】

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n]; //当前格子所累积的最小和
        for(int i = 0; i < m; i++) { //初始化最左列
            if(i == 0) dp[i][0] = grid[i][0];
            else dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for(int j = 0; j < n; j++) { //初始化最顶行
            if(j == 0) dp[0][j] = grid[0][j];
            else dp[0][j] = dp[0][j - 1] + grid[0][j];
        }
        for(int i = 1; i < m; i++) {
            for(int j = 1; j < n; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```



## 旋转图像

48mid 旋转图像 【先斜对角交换，再上下对称交换】

https://leetcode.cn/problems/rotate-image/

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        //先以主对角线为对称轴反转
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        //上下对称交换
        for(int i = 0; i < n; i++) {
            swap(matrix[i]);
        }
    }
    //处理数组交换
    void swap(int[] nums) {
        int left = 0, right = nums.length - 1;
        while(left < right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }
    }
}
```



## 回文链表

234easy 回文链表 

https://leetcode.cn/problems/palindrome-linked-list/

【用快慢指针寻找中间左侧节点】

【反转第一段链表】

【然后同时遍历两端链表 ，比较它们的值是否相同】

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        if(head.next == null) return true;
        ListNode slow = head;
        ListNode fast = head.next;
        while(fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }//此时slow的位置为链表中点左侧
        //第二段的头指针        
        ListNode secondHead = null;
        if(fast.next != null) { //链表个数为奇数
            secondHead = slow.next.next;
        } else {                //链表个数为偶数
            secondHead = slow.next;
        }
        ListNode midtemp = slow.next;
        ListNode pre = null;
        ListNode cur = head;
        //反转第一段链表
        while(cur != midtemp) {
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        //slow为反转后链表的头结点
        ListNode firstHead = slow;
        while(firstHead != null) {
            if(firstHead.val != secondHead.val) return false;
            firstHead = firstHead.next;
            secondHead = secondHead.next;
        }
        return true;
    }
}
```



## 多数元素

169easy 多数元素

https://leetcode.cn/problems/majority-element/

【投票法，快】【如果这个数多余n/2，那和其他数一一抵消的话，最后还是会剩下】

```java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 1;
        int candidateNum = nums[0];
        for(int i = 1; i < nums.length; i++) {
            if(nums[i] == candidateNum) count++;
            else if(--count == 0) {
                candidateNum = nums[i];
                count = 1;
            }
        }
        return candidateNum;
    }
}
```

【快排】

```java
import java.util.Random;
class Solution {
    private final static Random random = new Random(System.currentTimeMillis());
    public int majorityElement(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums[nums.length / 2];
    }
    void quickSort(int[] nums, int left, int right) {
        if(left >= right) return;
        int pivotIndex = partition(nums, left, right);
        quickSort(nums, left, pivotIndex - 1);
        quickSort(nums, pivotIndex + 1, right);
    }
    int partition(int[] nums, int left, int right) {
        int randomIndex = left + random.nextInt(right - left + 1);
        swap(nums, left, randomIndex);
        int pivot = nums[left];

        int le = left + 1;
        int ge = right;
        while(true) {
            while(le <= ge && nums[le] < pivot) {
                le++;
            }
            while(le <= ge && nums[ge] > pivot) {
                ge--;
            }
            if(le >= ge) break;
            swap(nums, le, ge);
            le++;
            ge--;
        }
        swap(nums, left, ge);
        return ge;
    }
    void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```



最大正方形

221mid 最大正方形 

https://leetcode.cn/problems/maximal-square/

【动态规划，dp[i] [j]表示如果该点为1，则以该点为右下角的正方形的边长为dp[i] [j]】

![image.png](E:\2021java\Typora\typora-user-images\8c4bf78cf6396c40291e40c25d34ef56bd524313c2aa863f3a20c1f004f32ab0-image.png)

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int len1 = matrix.length;
        int len2 = matrix[0].length;
        int maxSideLen = 0; //最大变长
        int[][] dp = new int[len1 + 1][len2 + 1];
        for(int i = 1; i <= len1; i++) {
          for(int j = 1; j <= len2; j++) {
             if(matrix[i - 1][j - 1] == '1') {
              dp[i][j] = Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1])) + 1;
              maxSideLen = Math.max(maxSideLen, dp[i][j]);//更新最大变长
             }
          }
        }
        return maxSideLen * maxSideLen;
    }
}
```



## 用 Rand7() 实现 Rand10()

470mid 用 Rand7() 实现 Rand10()

【尽可能利用范围剩余的数】

https://leetcode.cn/problems/implement-rand10-using-rand7/

```
已知 rand_N() 可以等概率的生成[1, N]范围的随机数
那么：
(rand_X() - 1) × Y + rand_Y() ==> 可以等概率的生成[1, X * Y]范围的随机数
即实现了 rand_XY()

要实现rand10()，就需要先实现rand_N()，并且保证N大于10且是10的倍数。这样再通过rand_N() % 10 + 1 就可以得到[1,10]范围的随机数了。
```

```java
/**
 * The rand7() API is already defined in the parent class SolBase.
 * public int rand7();
 * @return a random integer in the range 1 to 7
 */
class Solution extends SolBase {
    public int rand10() {        
        while(true) {
            int num = (rand7() - 1) * 7 + rand7();
            if(num <= 40) return num % 10 + 1;

            int rand9 = num - 40; //剩余[1, 9]
            int a = (rand9 - 1) * 7 + rand7();
            if(a <= 60) return a % 10 + 1;

            int rand3 = a - 60; //剩余[1, 3]
            int b = (rand3 - 1) * 7 + rand7();
            if(b <= 20) return b % 10 + 1;
        }
    }
}
```



## 最长公共前缀

14easy 最长公共前缀 【将字符串按长度从小到大排序，取第一个为基准去比较】

https://leetcode.cn/problems/longest-common-prefix/

<img src="E:\2021java\Typora\typora-user-images\image-20230106004615693.png" alt="image-20230106004615693" style="zoom:67%;" />

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        Arrays.sort(strs, (a, b) -> a.length() - b.length()); //把短字符串排在前
        StringBuilder sb = new StringBuilder();
        String str0 = strs[0];
        for(int i = 0; i < str0.length(); i++) {
            for(int j = 1; j < strs.length; j++) {
                if(strs[j].charAt(i) != str0.charAt(i)) { //遇到不同字符，返回现有结果
                    return sb.toString();
                }
            }
            sb.append(str0.charAt(i));
        }
        return sb.toString();
    }
}
```



## 最长有效括号

32hard 最长有效括号 【动态规划】

https://leetcode.cn/problems/longest-valid-parentheses/



https://leetcode.cn/problems/longest-valid-parentheses/solution/dong-tai-gui-hua-si-lu-xiang-jie-c-by-zhanganan042/

```java
ch[i] == ')'
这时，s[i]s[i] 无法和其之前的元素组成有效的括号对，所以，dp[i] = 0dp[i]=0

ch[i] == ')'
情况一：ch[i - 1] == '('
if(i == 1 && ch[i - 1] == '(') dp[i] = 2;
if(i >= 2 && ch[i - 1] == '(') dp[i] = dp[i - 2] + 2;
```

<img src="E:\2021java\Typora\typora-user-images\image-20230106020537545.png" alt="image-20230106020537545" style="zoom:67%;" />



```java
情况二：ch[i - 1] == ')'
if(ch[i - 1] == ')') { //最复杂的情况
   if(i - dp[i - 1] - 1 >= 0 && ch[i - dp[i - 1] - 1] == '(') {
        if(i - dp[i - 1] - 2 >= 0) dp[i] = dp[i - 1] + 2 + dp[i - dp[i - 1] - 2];
        else dp[i] = dp[i - 1] + 2;
   }
}
```

<img src="E:\2021java\Typora\typora-user-images\image-20230106020745584.png" alt="image-20230106020745584" style="zoom:67%;" />

```java
class Solution {
    public int longestValidParentheses(String s) {
        char[] ch = s.toCharArray();
        int len = ch.length;
        int[] dp = new int[len];
        int res = 0;
        for(int i = 1; i < len; i++) {
          if(ch[i] == ')') { //遇到')'才有可能满足条件
            if(i == 1 && ch[i - 1] == '(') dp[i] = 2;
            if(i >= 2 && ch[i - 1] == '(') dp[i] = dp[i - 2] + 2;
            if(ch[i - 1] == ')') { //最复杂的情况
              if(i - dp[i - 1] - 1 >= 0 && ch[i - dp[i - 1] - 1] == '(') {
                if(i - dp[i - 1] - 2 >= 0) dp[i] = dp[i - 1] + 2 + dp[i - dp[i - 1] - 2];
                else dp[i] = dp[i - 1] + 2;
              }
            }
            res = Math.max(res, dp[i]);
          }
        }
        return res;
    }
}
```



## 字符串解码

394mid 字符串解码  【用栈，遇到'['就入栈，遇到']'就出栈】

https://leetcode.cn/problems/decode-string/

题解 https://leetcode.cn/problems/decode-string/solution/decode-string-fu-zhu-zhan-fa-di-gui-fa-by-jyd/

```java
class Solution {
    public String decodeString(String s) {
        LinkedList<String> strStack = new LinkedList<>();
        LinkedList<Integer> numStack = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        int multi = 0;
        for(char c : s.toCharArray()) {
            if(c >= '0' && c <= '9') { //更新累计到的倍数值
                multi = multi * 10 + c - '0';
            } else if (c == '[') { //遇到[,将累积的res和multi入栈，并清空res和multi
                strStack.addLast(res.toString());
                numStack.addLast(multi);
                multi = 0;
                res = new StringBuilder();
            } else if (c == ']') {
                StringBuilder tmp = new StringBuilder();
                String str = strStack.pollLast();
                int curMulti = numStack.pollLast();
                for(int i = 0; i < curMulti; i++) {                    
                    tmp.append(res); //根据弹出的curMulti，复制res
                }
                res = new StringBuilder(str + tmp); //再和弹出的str结合
            } else {
                res.append(c);
            }
        }
        return res.toString();
    }
}
```



## 颜色分类

75mid 颜色分类 【题目要求只能遍历一次】【三路快排的一次partition】

https://leetcode.cn/problems/sort-colors/

```java
class Solution {
    public void sortColors(int[] nums) {
        partition(nums, 0, nums.length - 1);
    }
    void partition(int[] nums, int left, int right) {
        int i = left;
        int lt = left;
        int gt = right;
        while(i <= gt) {
            if(nums[i] == 0) {
                swap(nums, i, lt);
                lt++;
                i++;
            } else if (nums[i] == 1) {
                i++;
            } else {
                swap(nums, i, gt);
                gt--;
            }
        }
    }
    void swap(int[] nums, int index1, int index2) {
        int tmp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = tmp;
    }
}
```



## 单词搜索

79mid 单词搜索 【递归+回溯，DFS】【used去重】

https://leetcode.cn/problems/word-search/

```java
class Solution {
    int m = 0, n = 0;
    int len = 0;
    int total = 0; //标记累计符合元素的个数
    boolean[][] used; //标记该点是否用过
    public boolean exist(char[][] board, String word) {
        m = board.length;
        n = board[0].length;
        used = new boolean[m][n];
        len = word.length();
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                boolean flag = backtracking(board, i, j, word);
                if(flag) return true;
            }
        }
        return false; //如果遍历完所有的点作为单词开头都没有
    }
    boolean backtracking(char[][] board, int i, int j, String word) {
        if(i < 0 || i >= m || j < 0 || j >= n) { //终止条件
            if(total == len) return true;
            return false;
        }
        if(total == len) return true; //终止条件
        if(!used[i][j] && board[i][j] == word.charAt(total)) {
            used[i][j] = true;
            total++;
            boolean flag1 = backtracking(board, i + 1, j, word); //向下
            if(flag1) return true;
            boolean flag2 = backtracking(board, i - 1, j, word); //向上
            if(flag2) return true;
            boolean flag3 = backtracking(board, i, j + 1, word); //向右
            if(flag3) return true;
            boolean flag4 = backtracking(board, i, j - 1, word); //向左
            if(flag4) return true;
            used[i][j] = false; //回溯
            total--; //回溯
        }
        return false;
    }
}
```



## 二叉树的锯齿形层序遍历

103mid 二叉树的锯齿形层序遍历 【层序遍历】【双端队列】

https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();    
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        if(root == null) return res;
        int dir = -1; //初始化队列方向为向左
        Deque<TreeNode> que = new ArrayDeque<>(); //双端队列
        que.addLast(root);
        while(!que.isEmpty()) {
            LinkedList<Integer> path = new LinkedList<>();
            int size = que.size();
            while(size-- > 0) {
                if(dir == -1) { //左边为队首
                    TreeNode node = que.pollFirst();
                    path.add(node.val);
                    if(node.left != null) que.addLast(node.left);
                    if(node.right != null) que.addLast(node.right);
                }
                if(dir == 1) { //右边为队首
                    TreeNode node = que.pollLast();
                    path.add(node.val);
                    if(node.right != null) que.addFirst(node.right);
                    if(node.left != null) que.addFirst(node.left);
                }
            }
            res.add(new ArrayList<>(path));
            dir = -dir; //结束本层后反转队列方向
        }
        return res;
    }
}
```



## 比特位计数

338easy  比特位计数 【动态规划】

https://leetcode.cn/problems/counting-bits/

```
对于所有的数字，只有两类：
1、奇数：二进制表示中，奇数一定比前面那个偶数多一个 1，因为多的就是最低位的 1。
2、偶数：二进制表示中，偶数中 1 的个数一定和除以 2 之后的那个数一样多。因为最低位是 0，除以 2 就是右移一位，也就是把那个 0 抹掉而已，所以 1 的个数是不变的。
3、另外，0 的 1 个数为 0
```



```java
class Solution {
    public int[] countBits(int n) {
        int[] res = new int[n + 1];
        res[0] = 0; //初始化特殊情况
        for(int i = 1; i <= n; i++) {
            if(i % 2 == 1) { //i为奇数
                res[i] = res[i - 1] + 1;
            } else { //i为偶数
                res[i] = res[i / 2];
            }
        }
        return res;
    }
}
```



## LRU 缓存

146mid LRU 缓存 【构建双向链表+HashMap】

https://leetcode.cn/problems/lru-cache/

```java
class LNode { //自定义节点
    int key;
    int val;
    LNode prev;
    LNode next;
    public LNode(){}
    public LNode(int key, int val) {
        this.key = key;
        this.val = val;
    }
}
class LRUCache {
    private LNode dummyHead;
    private LNode dummyTail;
    private int size;
    private int capacity;
    private HashMap<Integer, LNode> map;
    public LRUCache(int capacity) { 
        this.capacity = capacity;       
        this.size = 0;
        dummyHead = new LNode();
        dummyTail = new LNode();
        dummyHead.next = dummyTail;
        dummyTail.prev = dummyHead;
        map = new HashMap<>();
    }
    
    public int get(int key) {
        if(!map.containsKey(key)) return -1;
        LNode node = map.get(key);
        moveToHead(node); //操作过该节点后，移到头结点
        return node.val;
    }
    
    public void put(int key, int value) {
        if(!map.containsKey(key)) { //key已存在
            LNode node = new LNode(key, value);
            map.put(key, node);
            addToHead(node);
            if(size == capacity) { //容量满了，删除尾结点
                removeTailNode();
            } else {
                size++; //没满，size++
            }
        } else { //key未存在，加到头结点
            LNode node = map.get(key);
            moveToHead(node);
            node.val = value;
        }
    }
    public void moveToHead(LNode node) {
        removeNode(node);
        addToHead(node);
    }
    public void addToHead(LNode node) {
        LNode tmp = dummyHead.next;
        dummyHead.next = node;
        node.next = tmp;
        node.prev = dummyHead;
        tmp.prev = node;
    }
    //删除最后一个节点，map里的也要删除
    public void removeTailNode() {
        LNode tailNode = dummyTail.prev;
        map.remove(tailNode.key);
        removeNode(tailNode);
    }
    public void removeNode(LNode node) {
        LNode prevTmp = node.prev;
        LNode nextTmp = node.next;
        prevTmp.next = nextTmp;
        nextTmp.prev = prevTmp;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```



##  K 个一组翻转链表

25hard   K 个一组翻转链表 【先构建反转链表方法】 【再每k个一组进行翻转】

https://leetcode.cn/problems/reverse-nodes-in-k-group/

<img src="E:\2021java\Typora\typora-user-images\866b404c6b0b52fa02385e301ee907fc015742c3766c80c02e24ef3a8613e5ad-k个一组翻转链表.png" alt="k个一组翻转链表.png" style="zoom:80%;" />

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummyHead = new ListNode(0, head);
        ListNode pre = dummyHead;
        ListNode start = head;
        ListNode end = pre;
        while(end != null) {
            for(int i = 0; i < k && end != null; i++) {
                end = end.next;
            }
            if(end == null) break;
            ListNode next = end.next; //记录下一节的头结点，从这里断开
            end.next = null; //断开
            pre.next = reverse(start);
            start.next = next;
            //更新下一次反转的初始节点位置
            pre = start;
            end = start;
            start = start.next;
        }
        return dummyHead.next;
    }
    ListNode reverse(ListNode head) {
        ListNode pre = null;
        ListNode cur = head;
        while(cur != null) {
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
}
```



## 最长连续序列

128mid 最长连续序列 【HashSet去重】【要求时间复杂度O(n)，但题解很慢】

https://leetcode.cn/problems/longest-consecutive-sequence/

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int res = 0;
        Set<Integer> set = new HashSet<>();
        for(int num : nums) {
            set.add(num);
        }
        for(int num : nums) {
            if(!set.contains(num - 1)) { //如果当前数字为起始数字，再开始往下遍历
                int curNum = num;
                int curLen = 1;
                while(set.contains(curNum + 1)) {
                    curLen++;
                    curNum++;
                }
                res = Math.max(res, curLen);
            }
        }
        return res;
    }
}
```



## 合并K个升序链表

23hard 合并K个升序链表 

https://leetcode.cn/problems/merge-k-sorted-lists/

【优先级队列】

【每次往结果添加结点时，要判断下一个结点是否为空，如果不为空，要把新加结点后面的链表加回到que队列】

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        Queue<ListNode> que = new PriorityQueue<>((a, b) -> a.val - b.val);//优先队列
        for(ListNode list : lists) {
            if(list != null) que.offer(list);
        }
        ListNode dummyHead = new ListNode(-1); //虚拟头结点
        ListNode pre = dummyHead;
        while(!que.isEmpty()) {
            pre.next = que.poll();
            pre = pre.next;
            if(pre.next != null) que.offer(pre.next); //往队列添加结点后的链表
        }
        return dummyHead.next;
    }
}
```

【分治+递归，快】

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return merge(lists, 0, lists.length - 1);
    }
    //递归
    ListNode merge(ListNode[] lists, int left, int right) {
        if(lists.length == 0) return null;
        if(left >= right) return lists[left];
        int mid = left + (right - left) / 2;
        ListNode l1 = merge(lists, left, mid);
        ListNode l2 = merge(lists, mid + 1, right);
        return merge2Lists(l1, l2);
    }
    ListNode merge2Lists(ListNode list1, ListNode list2) {
        ListNode dummyHead = new ListNode(-1);
        ListNode pre = dummyHead;
        while(list1 != null && list2 != null) {
            if(list1.val < list2.val) {
                pre.next = list1;
                list1 = list1.next;
            } else {
                pre.next = list2;
                list2 = list2.next;
            }
            pre = pre.next;
        }
        if(list1 != null) pre.next = list1;
        if(list2 != null) pre.next = list2;
        return dummyHead.next;
    }
}
```



## 只出现一次的数字

136easy 只出现一次的数字 【题目要求不能用额外空间，实现线性时间复杂度】

【时间复杂度为O(n)，其中 n是数组长度。只需要对数组遍历一次】

【空间复杂度为O(1)】

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = nums[0];
        for(int i = 1; i < nums.length; i++) {
            res = res ^ nums[i];
        }
        return res;
    }
}
```



## 二叉树展开为链表

114mid 二叉树展开为链表 【递归，提前标注左子树和右子树的原状态】

https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/

```java
class Solution {
    public void flatten(TreeNode root) {
        preorder(root);
    }
    TreeNode preorder(TreeNode root) {
        if(root == null) return null;
        TreeNode leftTmp = root.left; //保存当前节点的左子树（左指向）
        TreeNode rightTmp = root.right; //保存当前节点的右子树（右指向）
        //递归
        root.right = preorder(leftTmp); //当前节点的右孩子更新为左子树的结果
        root.left = null; //当前节点的左孩子更新为null
        //定义指针，从root出发，到right的结尾
        TreeNode cur = root;
        while(cur.right != null) {
            cur = cur.right;
        }
        //递归
        //再把原来右子树的结果部分接到right后
        cur.right = preorder(rightTmp);
        return root;
    }
}
```



## 汉明距离

461easy 汉明距离 【记住】

https://leetcode.cn/problems/hamming-distance/

```java
class Solution {
    public int hammingDistance(int x, int y) {
        int diff = 0;
        while(x != 0 || y != 0) {
            if(x % 2 != y % 2) diff++;
            x /= 2;
            y /= 2;
        }
        return diff;
    }
}
```



## 寻找重复数

287mid 寻找重复数 【快慢指针，参考“142mid 环形链表II”】

【解决方案必须 不修改 数组 nums 且只用常量级 O(1) 的额外空间。】

https://leetcode.cn/problems/find-the-duplicate-number/

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = 0, fast = 0;
        slow = nums[slow]; //初始化快慢指针
        fast = nums[nums[fast]];
        while(slow != fast) { //一定有环，循环一定能跳出
            slow = nums[slow]; //走1步
            fast = nums[nums[fast]]; //走2步
        }
        int pre1 = 0;
        int pre2 = slow;
        while(pre1 != pre2) {
            pre1 = nums[pre1]; //各走1步
            pre2 = nums[pre2];
        }
        return pre1;
    }
}
```



## 最接近的三数之和

16mid 最接近的三数之和 【和“15mid 三数之和 ”类似】

https://leetcode.cn/problems/3sum-closest/

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int diff = Integer.MAX_VALUE;
        int res = 0;
        Arrays.sort(nums);
        for(int k = 0; k < nums.length - 2; k++) {
            if(k > 0 && nums[k] == nums[k - 1]) continue;
            int i = k + 1, j = nums.length - 1;
            while(i < j) {
                int sum = nums[k] + nums[i] + nums[j];
                if(Math.abs(sum - target) < diff) {
                    diff = Math.abs(sum - target);
                    res = sum;
                } 
                if (sum > target) {
                    j--;
                } else if (sum < target) {
                    i++;
                } else {
                    return sum;
                }
            }
        }
        return res;
    }
}
```



## 复制带随机指针的链表

138mid 复制带随机指针的链表

https://leetcode.cn/problems/copy-list-with-random-pointer/

【1. 在原链表的基础上，每个节点后面添加复制后的新节点

	2. 新节点指向的随机节点就是原随机节点的下一个节点
	2. 分离链表】



原链表

![image-20230210182041472](E:\2021java\Typora\typora-user-images\image-20230210182041472.png)

第一步：根据遍历到的原节点创建对应的新节点，每个新创建的节点是在原节点后面，比如下图中原节点1不再指向原原节点2，而是指向新节点1

![5.jpg](E:\2021java\Typora\typora-user-images\360dbd3b89c25324287f4cef2c22ba8a20e946891ac887f70703b211893aafa0-5.jpg)



第二步：用来设置新链表的随机指针

![image-20230210182127125](E:\2021java\Typora\typora-user-images\image-20230210182127125.png)

第三步：将两个链表分离开，再返回新链表

<img src="E:\2021java\Typora\typora-user-images\image-20230210182215375.png" alt="image-20230210182215375" style="zoom:67%;" />





```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public Node copyRandomList(Node head) {
        //每个节点后面添加复制后的新节点
        if(head == null) return null;
        Node cur = head;
        while(cur != null) {
            int val = cur.val;
            Node node = new Node(val);
            Node tmp = cur.next;
            cur.next = node;
            node.next = tmp;

            cur = tmp;
        }

        //新节点指向的随机节点就是原随机节点的下一个节点
        cur = head;
        while(cur != null) {
            if(cur.random != null) cur.next.random = cur.random.next;
            cur = cur.next.next;
        }

        //分离链表
        Node dummyHead = new Node(-1);
        Node newCur = dummyHead;
        cur = head;
        while(cur != null) {
            newCur.next = cur.next;
            newCur = newCur.next;
            
            cur.next = newCur.next;
            cur = cur.next;
            
        }
        return dummyHead.next;
    }
}
```





## 链表的中间结点

876easy 链表的中间结点

https://leetcode.cn/problems/middle-of-the-linked-list/

【节点数有偶数和奇数之分】

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while(fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        if(fast.next == null) return slow; //节点数为偶数，返回第二个中间节点
        return slow.next; //节点数为奇数
    }
}
```



## 重排链表

143mid 重排链表

https://leetcode.cn/problems/reorder-list/

【寻找链表中点 + 链表逆序 + 合并链表， 快】

【时间复杂度O(n)，空间复杂度O(1)】

```java
class Solution {
    public void reorderList(ListNode head) {
        ListNode mid = getMidNode(head); //寻找中间节点
        ListNode l2 = mid.next;
        mid.next = null; //断开
        l2 = reverse(l2); //反转链表
        mergeList(head, l2); //合并链表
    }
    //合并链表
    void mergeList(ListNode l1, ListNode l2) {
        ListNode cur1 = l1;
        ListNode cur2 = l2;

        while(cur1 != null && cur2 != null) {
            ListNode tmp1 = cur1.next;
            ListNode tmp2 = cur2.next;
            cur1.next = cur2;
            cur2.next = tmp1;

            cur1 = tmp1;
            cur2 = tmp2;
        }        
    }
    //反转链表
    ListNode reverse(ListNode head) {
        ListNode pre = null;
        ListNode cur = head;
        while(cur != null) {
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
    //寻找中间节点
    ListNode getMidNode(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while(fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```



【存储节点到列表，慢】

【时间复杂度O(n)，空间复杂度O(n)】

```java
class Solution {
    public void reorderList(ListNode head) {
        List<ListNode> list = new ArrayList<>();
        ListNode cur = head;
        while(cur != null) {
            list.add(cur);
            cur = cur.next;
        }
        int i = 0, j = list.size() - 1;
        while(i < j) {
            list.get(i).next = list.get(j);
            i++;
            if(i == j) break;
            list.get(j).next = list.get(i);
            j--;
        }
        list.get(i).next = null;
    }
}
```



## 搜索二维矩阵 II

240mid 搜索二维矩阵 II

https://leetcode.cn/problems/search-a-2d-matrix-ii/

【逐行进行二分查找】【时间复杂度 O(mlog(n))】

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        for(int row = 0; row < matrix.length; row++) {
            //当前行最左边的值比target大，说明后续的值都会比target大
            if(matrix[row][0] > target) return false; 
            //当前行的最右边的值比target小，说明该行的值都会比target小
            if(matrix[row][matrix[row].length - 1] < target) continue;
            //寻找二分点
            int mid = binarySearch(matrix[row], target);
            if(mid != -1) return true;
        }
        return false;
    }
    //二分查找
    int binarySearch(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(target > nums[mid]) left = mid + 1;
            else if(target < nums[mid]) right = mid - 1;
            else return mid;
        }
        return -1;
    }
}
```



【从右上角开始遍历， 时间复杂度O(m + n)】

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int row = 0, col = matrix[0].length - 1;
        while(row < matrix.length && col >= 0) {
            if(target < matrix[row][col]) col--;//如果target的值小于当前值，那么就向左走
            else if(target > matrix[row][col]) row++;//如果target的值大于当前值，那么就向下走
            else return true; //找到target
        }
        return false;
    }
}
```

