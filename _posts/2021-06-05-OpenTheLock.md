---
title: Open the Lock (LeetCode Problem 752)
tags: Code LeetCode LeetCode-Medium
---

## Problem Statement:
You have a lock in front of you with 4 circular wheels. Each wheel has 10 slots: '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'. The wheels can rotate freely and wrap around: for example we can turn '9' to be '0', or '0' to be '9'. Each move consists of turning one wheel one slot.

The lock initially starts at '0000', a string representing the state of the 4 wheels.

You are given a list of deadends dead ends, meaning if the lock displays any of these codes, the wheels of the lock will stop turning and you will be unable to open it.

Given a target representing the value of the wheels that will unlock the lock, return the minimum total number of turns required to open the lock, or -1 if it is impossible.

### Example 1
```
Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6
Explanation:
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".
```
### Example 2
```
Input: deadends = ["8888"], target = "0009"
Output: 1
Explanation:
We can turn the last wheel in reverse to move from "0000" -> "0009".
```
### Example 3
```
Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1
Explanation:
We can't reach the target without getting stuck.
```
### Example 4
```
Input: deadends = ["0000"], target = "8888"
Output: -1
```
 

### Constraints
* 1 <= deadends.length <= 500
* deadends[i].length == 4
* target.length == 4
* target will not be in the list deadends.
* target and deadends[i] consist of digits only.

## Problem Solving Approach
Before we start solving the problem, we need to come up with the base cases for this problem. The first one is if the dead-end set has the initial state of the lock ```"0000"```, in which case the result would be ```-1```, and the other case is when the target itself is ```"0000"```, in which case the result would be ```0```.

Now We are going to employ a BFS of all possible solutions from the init state ```"0000"``` by a rotation of the slot to the clockwise and anti clockwise side for all the 4 wheels.

For BFS we need to use a queue to keep track of all the nodes of the given level and process further down by going to the next wheel. The following are the operations that we would have to do when we reach a new combination of wheels:

* If we reach a new combination of wheels that is not a part of the deadend set we note down the number of steps taken to reach that point to a map and push the combination to the queue.
* If the combination of wheels is in the deadend set, we just continue to the next possible combination.
* If the new combination is equal to the target combination we return the number of steps taken to reach that point.

These operations are done till all the possible combinations are processed from the queue.

## Implementation
The following is my implementation of the above approach in Java:
```java
public class OpenTheLock {
  public int openLock(String[] deadends, String target) {
    Set<String> deadendsSet = new HashSet<String>();
    
    // Convert the array to a set for easy lookups
    for(String deadend: deadends) {
      deadendsSet.add(deadend);
    }
    
    // Base Cases for this problem
    if(deadendsSet.contains("0000")) {
      return -1;
    }
    else if(target.equals("0000")) {
      return 0;
    }

    // Queue to keep track of the combinations of the wheels to be processed
    Queue<String> queue = new LinkedList<String>();
    queue.add("0000");

    // Hash Map to store the mapping between the combination of wheels to 
    // the steps taken to reach that combination from "0000"
    Map<String, Integer> stepMap = new HashMap<String, Integer>();
    stepMap.put("0000", 0);
    
    // Execute till queue is completely processed or till the combination 
    // is equal to the target
    while(!queue.isEmpty()) {
      String item = queue.poll();
      
      // Get the steps to reach the current processed item
      int steps = stepMap.get(item);
      
      for(int i = 0; i < 4; i++) {
        for(int j = -1; j <= 1; j += 2) {
          int diff = (Character.getNumericValue(item.charAt(i)) + j + 10) % 10;
          String nextItem = item.substring(0, i) + diff + item.substring(i + 1);
          
          // Return steps when target is reached
          if(nextItem.equals(target)) {
            return steps + 1;
          }
          // If not target and not deadend then add to queue and map 
          // the steps taken to reach it
          else if(!deadendsSet.contains(nextItem) && !stepMap.containsKey(nextItem)) {
            stepMap.put(nextItem, steps + 1);
            queue.add(nextItem);
          }
        }
      }
    }
    return -1;
  }
}
```

---
