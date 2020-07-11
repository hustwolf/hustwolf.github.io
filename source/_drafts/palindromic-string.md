---
title: palindromic string
date: 2020-06-30 22:55:23
tags: algorithm
---

```java
public class PalindromicString
{
    /**
     * 请判断一个链表是否为回文链表。
     * <p>
     * 示例 1:
     * 输入: 1->2
     * 输出: false
     * <p>
     * 示例 2:
     * 输入: 1->2-> 1 -> 2->1
     * <p>
     * <p>
     * 1 <- 2 <- 2
     * 输出: true
     * 进阶：
     * 你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？
     */

    public static class
    Node
    {
        private int value;
        private Node next;

        public Node(int value, Node next)
        {
            this.value = value;
            this.next = next;
        }

        public Node(int value)
        {
            this(value, null);
        }

        public int getValue()
        {
            return value;
        }

        public Node getNext()
        {
            return next;
        }

        public Node setNext(Node next)
        {
            this.next = next;
            return this;
        }
    }

    public static boolean checkPalindromic(Node node)
    {
        if (node == null) {
            return false;
        }

        if (node.getNext() == null) {
            return true;
        }

        if (node.getNext().getNext() == null) {
            return node.getValue() == node.getNext().getValue();
        }
        boolean odd = true;
        Node oneNode = node;
        Node twoNode = node.getNext();
        Node preNode = null;

        while (true) {
            if (preNode == null) {
                preNode = oneNode;
                oneNode = oneNode.getNext();
            }
            else {
                Node originOneNode = oneNode;
                oneNode = oneNode.getNext();
                originOneNode.setNext(preNode);
                preNode = originOneNode;
            }

            if (twoNode.getNext() == null) {
                odd = false;
                break;
            }
            if (twoNode.getNext().getNext() == null) {

                odd = true;
                break;
            }
            twoNode = twoNode.getNext().getNext();
        }

        if (odd) {
            oneNode = oneNode.getNext();
        }

        while (true) {
            if (oneNode == null) {
                break;
            }

            if (preNode.getValue() != oneNode.getValue()) {
                return false;
            }
            oneNode = oneNode.getNext();
            preNode = preNode.getNext();
        }

        return true;
    }

    public static void main(String[] args)
    {
        final Node root = new Node(1, new Node(2, new Node(3, new Node(3, new Node(2, new Node(1))))));
        System.out.println(checkHuiwen(root));
    }
}

```
