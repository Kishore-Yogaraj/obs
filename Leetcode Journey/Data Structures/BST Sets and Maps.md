
Sets and Maps, similar to stacks and queues, are interfaces that can be implemented using trees. Implementing them with trees allows for a O(log n)O(log n) time for insertion, deletion, and search operations.

### Sets
Imagine we have a phone book with three names - `Alice, Brad, Collin`. We could store these using dynamic arrays but a set ensures that we have unique values in our data structure, and implementing it using a tree ensures that our keys are sorted alphabetically. This is known as a **TreeSet**.

### Maps
A map on the other hand operates on key-value pair. If implemented with a tree, it is commonly referred to as a **TreeMap**.

Going back to our phone book example, not only can we store names, but also map them to their phone numbers. Again, the data structure is sorted by the key. Because the key maps to a value, we can find all of the information associated with a key.

In this example, the key may be the last name of the person. The value here doesn't have to be a phone number, it can also be an object or another data type. The only requirement is that we can compare keys to each other, since the trees must follow some sort of order.

The operations performed on a TreeMap run in O(log n) time. The mapping for the phonebook would look like the following.

```python
{'Alice' : 123, 'Brad' : 345, 'Collin' : 678}
```

