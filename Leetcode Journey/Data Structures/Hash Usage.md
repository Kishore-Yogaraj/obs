Having talked about TreeSets and TreeMaps in the previous chapter, let's discuss how the map and the set interfaces can be implemented using hashing.

In this lesson, we will be focusing on the usage of a HashSet and a HashMap, but we will cover their implementation in the next lesson. While knowing the implementation is important, it is not as important as knowing how to use them.

Hash maps are probably one of the most useful data structures/concepts for coding interviews. When questions use the words **"unique", "count", or "frequency"**, it is almost certain a hashmap or hashset will be useful for solving the problem.


> [!ATTENTION] Recall that the difference between a set and a map is that in sets contain keys only, while maps contain key-value pairs.

![[Pasted image 20250309161435.png]]

### Tree Maps vs Hash Maps

The downside of hash maps is that they are not ordered, so it not possible to traverse the keys of a hashmap in any particular order. If you were to iterate through all the keys, you would first need to sort them, which will run in O(n log n)O(n log n) time, making it slower than an in order traversal of a tree map which is O(n)O(n).

Because hashmaps don't allow duplicates and have key-value pairs, we can use them to count frequency of keys. Going back to our phonebook example, we can count the number of times a given name appears in our phonebook by mapping the name to its frequency.

Given the array below, we can add all the elements into a hash map as keys. Because hash maps do not allow duplicates, we can use this to our advantage. If a name already exists in our hash map as the key and we encounter it again in our array, we can just increment its value by 1. If the name does not exist, we can add it to our hash map and set the frequency to 1.

`["alice", "brad", "collin", "brad", "dylan", "kim"]`

![[Pasted image 20250309161637.png]]

**Code Implementation:**
```python
names = ["alice", "brad", "collin", "brad", "dylan", "kim"]

countMap = {}
for name in names:
    # If countMap does not contain name
    if name not in countMap:
        countMap[name] = 1
    else:
        countMap[name] += 1
```