
### Singly Linked List
A linked list is another data structure like an array in the sense that it stores elements in an ordered sequence

Linked lists are made up of object's called `ListNode` . The object contains two attributes which are `value` and `next`

**Value** stores the value of the node which could be a character or an integer and **Next** stores the reference to the next node in a linked list


![[Pasted image 20250125133412.png]]

#### Creating A Linked List from Scratch
```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
```

#### Making the node point to another node
```python
ListNode1.next = ListNode2
```

#### Continuing the pointers of the linked list all the way to the last node
```python
ListNode2.next = ListNode3
ListNode3.next = null
```

#### Traversing through a linked list
```python
curr = ListNode1
while curr != null:
	curr = curr.next
```

**Linked lists can also be circular where the last node points back to the first node instead of null**

#### Appending to a Linked List
The end of a linked list is assumed to have a tail pointer to signify that it is the end of the linked list. The start node has a head pointer to signify it is the start of the linked list. Therefore, to append to the end of a linked list you have to point the tail to the new node and assign tail to the new node as shown below.

```python
tail.next = ListNode4
tail = ListNode4
```

#### Deleting from a linked list
To delete from a linked list we simply point the node that is pointing to the node we want to delete to the next node in the linked list instead.

So this would be the node that the deleted node was pointing to

```python
head.next = head.next.next
```

### Doubly Linked Lists
Doubly linked lists are when the node has three attributes now which are `value` , `next` and `prev`

With a doubly linked list, every node now points to the node ahead of it and the node directly behind it

This means we'll need a couple extra steps for insertion and deletion because we also have to adjust the `prev` attribute

#### Insertion at the end
```python
tail.next = ListNode4
ListNode4.prev = tail
tail = tail.next
```

#### Deletion from end
```python
ListNode2 = tail.prev
ListNode2.next = null
tail = ListNode2
```


#### Time Complexity
![[Pasted image 20250125134657.png]]

##### Advantages
- Inserting can be performed in O(1) time even when inserting in the middle of the linked list as long as we have a reference to the desired position(index)
##### Disadvantages
- Do not support random access memory which means in order to find a specific node we have to traverse through the list in O(n) time
##### When to use
- Use when constant insertions and deletions are needed