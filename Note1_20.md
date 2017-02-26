# LeetCode 刷题笔记

本文档只为记录 LeetCode 上每一题和好的解决思路，以提高自己的算法能力

## 1. Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.
The return format had been changed to zero-based indices.
> Example:<br>
>Given nums = [2, 7, 11, 15], target = 9,<br>
>Because nums[0] + nums[1] = 2 + 7 =9,<br>
>return [0, 1].

Tags: Array, Hash Table
<pre><code>
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
        HashMap<Integer, Integer> hash = new HashMap<Integer, Integer>();
        for(int i = 0; i < numbers.length; i++) {
            Integer diff = (Integer)(target - numbers[i]);
            if(hash.containsKey(diff)) {
                int toReturn[] = {hash.get(diff), i};
                return toReturn;
            }
            hash.put(numbers[i], i);
        }
        return null;
    }
}
</code></pre>

So the simplest solution using a HashMap is to simply throw all the data in there to start with, 
then iterate through all of the numbers to see if (target-num) is in there, and if it is, return {lower index, higher index}.

However, we can cut down on some runtime and code length by doing it in a single for loop.

We know that the first number is strictly less than the second number (implying not equal), 
so if we are returning {value found in HashMap, current loop iteration} 
then we can be certain that we will never have to insert a value in the HashMap before we check if its difference is. 
Because of this fact, we can do the inserting and checking in the same loop with no issues. 
This saves a bit on runtime and memory because it means 
that you are not guaranteed to have to save all of the data in the HashTable at the start.

## 2. Add Two Numbers
You are given two non-empty linked lists representing two non-negative integers. 
The digits are stored in reverse order and each of their nodes contain a single digit. 
Add the two numbers and return it as a linked list.
You may assume the two numbers do not contain any leading zero, except the number 0 itself.

> Input: (2 -> 4 -> 3) + (5 -> 6 ->4)<br>
>Output: 7 -> 0 -> 8

Tags: Linked List, Math

<pre><code>
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode c1 = l1;
        ListNode c2 = l2;
        ListNode sentinel = new ListNode(0);
        ListNode d = sentinel;
        int sum = 0;
        while(c1 != null || c2 != null) {
        //个人猜测这个判断条件是为了"最低位对齐，最高位不一定对齐"的情况
            sum /= 10;
            if(c1 != null) {
                sum += c1.val;
                c1 = c1.next;
            }
            if(c2 != null) {
                sum += c2.val;
                c2 = c2.next;
            }
            d.next = new ListNode(sum % 10); //进位
            d = d.next;
        }
        if(sum / 10 == 1)
            d.next = new ListNode(1);
            return sentinel.next;
    }
}
</code></pre>

PS: This answer by an SWE in Google.

## 3. Longest Substring Without Repeating Characters
Given a string, find the length of the longest substring without repeating characters.

> Examples:<br>
>Given "abcabcbb", the answer is "abc", which the length is 3.<br>
>Given "bbbbb", the answer is "b", with the length of 1.<br>
>Given "pwwkew", the answer is "wke", with the length of 3.<br>
>Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

Tags: Hash Table, Two Pointers, String


<pre><code>
public int lengthOfLongestSubstring(String s) {
    if(s.length() == 0) return 0;
    HashMap<Character, Integer> map = new HashMap<Character, Integer>();
    int max = 0;
    for(int i = 0, j = 0; i < s.length(); i++) {
        if(map.containsKey(s.charAt(i))) {
            j = Math.max(j, map.get(s.charAt(i))+1);
        }
        map.put(s.charAt(i), i);
        max = Math.max(max, i-j+1);
    }
    return max;
}
</code></pre>

The basic idea is, keep a hashmap which stores the characters in string as keys and their positions as values, 
and keep two pointers which define the max substring. 
Move the right pointer to scan through the string , and meanwhile update the hashmap. 
If the character is already in the hashmap, then move the left pointer to the right of the same character last found. 
Note that the two pointers can only move forward.

## 4. Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively.
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

> Example 1:<br>
> nums1 = [1, 3]<br>
> nums2 = [2]<br>
> The median is 2.0<br>
> Example 2:<br>
> nums1 = [1, 2]<br>
> nums2 = [3, 4]<br>
> The median is (2 + 3)/2 = 2.5

Tags: Binary Search, Array, Divide and Conquer


To solve this problem, we need to understand "What is the use of median". 
In statistics, the median is used for dividing a set into two equal length subsets, 
that one subset is always greater than the other.

> If we understand the use of median for dividing, we are very close to the answer.
First let's cut A into two parts at a random position i:<br>
> <pre>
>       left_A             |        right_A<br>
> A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
> <pre>

Since A has m elements, so there are m+1 kinds of cutting( i = 0 ~ m ). 
And we know: len(left_A) = i, len(right_A) = m - i . 

> Note: when i = 0 , left_A is empty, and when i = m , right_A is empty.
With the same way, cut B into two parts at a random position j:<br>
> <pre>
>       left_B             |        right_B
> B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]
></pre>

Put left_A and left_B into one set, and put right_A and right_B into another set. 
Let's name them left_part and right_part :
><pre>
>       left_part          |        right_part
> A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
> B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]
> </pre>

If we can ensure:
- len(left_part) == len(right_part)
- max(left_part) <= min(right_part)

then we divide all elements in {A, B} into two parts with equal length, 
and one part is always greater than the other. 
Then median = (max(left_part) + min(right_part))/2.

To ensure these two conditions, we just need to ensure:
- i + j == m - i + n - j (or: m - i + n - j + 1)<br>
    if n >= m, we just need to set: i = 0 ~ m, j = (m + n + 1)/2 - i
- B[j-1] <= A[i] and A[i-1] <= B[j]

1. For simplicity, I presume A[i-1],B[j-1],A[i],B[j] are always valid even if i=0/i=m/j=0/j=n . 
I will talk about how to deal with these edge values at last.

2. Why n >= m? Because I have to make sure j is non-nagative since 0 <= i <= m and j = (m + n + 1)/2 - i. 
If n < m , then j may be nagative, that will lead to wrong result.

So, all we need to do is:<br>
Searching i in [0, m], to find an object `i` that:<br>
    B[j-1] <= A[i] and A[i-1] <= B[j], ( where j = (m + n + 1)/2 - i )<br>
And we can do a binary search following steps described below:
1. Set imin = 0, imax = m, then start searching in [imin, imax]
2. Set i = (imin + imax)/2, j = (m + n + 1)/2 - i
3. Now we have len(left_part)==len(right_part). And there are only 3 situations
     that we may encounter:<br>
 a. B[j-1] <= A[i] and A[i-1] <= B[j]<br> 
    Means we have found the object `i`, so stop searching.<br>
 b. B[j-1] > A[i]<br>
        Means A[i] is too small. We must `ajust` i to get `B[j-1] <= A[i]`.<br>
        Can we `increase` i?<br>
            Yes. Because when i is increased, j will be decreased.<br>
            So B[j-1] is decreased and A[i] is increased, and `B[j-1] <= A[i]` may
            be satisfied.<br>
        Can we `decrease` i?<br>
            `No!` Because when i is decreased, j will be increased.<br>
            So B[j-1] is increased and A[i] is decreased, and B[j-1] <= A[i] will
            be never satisfied.<br>
        So we must `increase` i. That is, we must ajust the searching range to
        [i+1, imax]. So, set imin = i+1, and goto step 2.<br>
 c. A[i-1] > B[j]<br>
        Means A[i-1] is too big. And we must `decrease` i to get `A[i-1]<=B[j]`.
        That is, we must ajust the searching range to [imin, i-1].
        So, set imax = i-1, and goto step 2.

When the object i is found, the median is:<br>
max(A[i-1], B[j-1]) (when m + n is odd)<br>
or (max(A[i-1], B[j-1]) + min(A[i], B[j]))/2 (when m + n is even)<br>
Now let's consider the edges values i=0,i=m,j=0,j=n where A[i-1],B[j-1],A[i],B[j] may not exist. <br>
Actually this situation is easier than you think.

What we need to do is ensuring that max(left_part) <= min(right_part). <br>
So, if i and j are not edges values(means A[i-1],B[j-1],A[i],B[j] all exist), then we must check both B[j-1] <= A[i] and A[i-1] <= B[j]. <br>
But if some of A[i-1],B[j-1],A[i],B[j] don't exist, then we don't need to check one(or both) of these two conditions. <br>
For example, if i=0, then A[i-1] doesn't exist, then we don't need to check A[i-1] <= B[j]. <br>
So, what we need to do is:
Searching i in [0, m], to find an object `i` that:
><pre>
>    (j == 0 or i == m or B[j-1] <= A[i]) and
>    (i == 0 or j == n or A[i-1] <= B[j])
>    where j = (m + n + 1)/2 - i
></pre>

And in a searching loop, we will encounter only three situations:

1. (j == 0 or i == m or B[j-1] <= A[i]) and<br>
   (i == 0 or j = n or A[i-1] <= B[j])<br>
   Means i is perfect, we can stop searching.<br>

2. j > 0 and i < m and B[j - 1] > A[i]<br>
    Means i is too small, we must increase it.<br>

3. i > 0 and j < n and A[i - 1] > B[j]<br>
   Means i is too big, we must decrease it.<br>

pointed out that: i < m ==> j > 0 and i > 0 ==> j < n . Because:<br>
><pre>
> m <= n, i < m ==> j = (m+n+1)/2 - i > (m+n+1)/2 - m >= (2*m+1)/2 - m >= 0   
> m <= n, i > 0 ==> j = (m+n+1)/2 - i < (m+n+1)/2 <= (2*n+1)/2 <= n
></pre>
So in situation 2. and 3. , we don't need to check whether j > 0 and whether j < n.


## 5. Longest Palindromic Substring
Given a string s, find the longest palindromic substring in s.<br>
You may assume that the maximum length of s is 1000.
> Example:<br>
> Input: "babad"<br>
>Output: "bab"<br>
Note: "aba" is also a valid answer.

Tags: String




