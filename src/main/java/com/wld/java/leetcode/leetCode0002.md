**1，java的非递归实现**

我们可以把它想象为两个字符串相加，其实原理都是一样的，具体我们可以参照[389，两个超级大数相加](https://mp.weixin.qq.com/s/fbCw49WLo0FImAIFl4pinQ)

```java
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode prev = head;
        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = (l1 != null ? l1.val : 0) + (l2 != null ? l2.val : 0) + carry;//求两个节点相加的值
            carry = sum / 10;//取进位的值
            prev.next = new ListNode(sum % 10);//sum对10求余后放到节点中
            prev = prev.next;
            l1 = l1 != null ? l1.next : l1;
            l2 = l2 != null ? l2.next : l2;
        }
        return head.next;
    }
```

 **2，java的递归实现**

我们还可以把非递归改为递归的方式，代码也比较简单

```java
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        helper(head, l1, l2, 0);
        return head.next;
    }

    private void helper(ListNode result, ListNode l1, ListNode l2, int carry) {
        if (l1 == null && l2 == null && carry == 0)
            return;
        int sum = (l1 != null ? l1.val : 0) + (l2 != null ? l2.val : 0) + carry;
        result.next = new ListNode(0);
        result.next.val = sum % 10;
        carry = sum / 10;
        helper(result.next, l1 != null ? l1.next : null, l2 != null ? l2.next : null, carry);
    }
```