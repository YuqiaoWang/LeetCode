# LeetCode 刷题笔记

本文档只为记录 LeetCode 上每一题和好的解决思路，以提高自己的算法能力

## 1. Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.
The return format had been changed to zero-based indices.
> Example:
>
>Given nums = [2, 7, 11, 15], target = 9,
>Because nums[0] + nums[1] = 2 + 7 =9,
>
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
