#leetcode #linkedList #hashmap #problem

https://leetcode.com/problems/lru-cache/

**Requirements**

1. **Get** --> O(1)
2. Put --> O(1)
3. **Remove** -> if space is full remove least recently used. 

**Assumption**

1. **HashMap**
	problem with [[HashMap]] is the look out for least recently used item. 
	although we can retrieve and set items in O(1) but deleting least recently is hard. 

2. **Linked List**
   [[Linked List]] is great choice to keep track of least recently used item. so we will always put most used item as head and in that order we will have least recently used item at the back of list.
   But there is a problem with this as retrieving or adding an item will take O(n) .

#### Solution
we can **map our keys not to the values directly, but to the [[linked list]] nodes** that represent these values. Mapping keys to list nodes means that the **dictionary will hold node references** that don't depend on node positions in the list itself. So we would be able to **rearrange list items without a need to remap** them in the dictionary.

Solution Require [[Doubly Linked List]]

![[Pasted image 20230718112143.png]]


**Code**
```js
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.cache = new Map();
    this.head = { key: null, value: null, prev: null, next: null };
    this.tail = { key: null, value: null, prev: this.head, next: null };
    this.head.next = this.tail;
  }

  _moveToHead(node) {
    // Remove node from its current position
    node.prev.next = node.next;
    node.next.prev = node.prev;

    // Move node to the front of the list
    node.next = this.head.next;
    node.prev = this.head;
    this.head.next.prev = node;
    this.head.next = node;
  }

  get(key) {
    if (!this.cache.has(key)) return -1;

    const node = this.cache.get(key);
    this._moveToHead(node);

    return node.value;
  }

  put(key, value) {
    if (this.cache.has(key)) {
      const node = this.cache.get(key);
      node.value = value;
      this._moveToHead(node);
    } else {
      if (this.cache.size >= this.capacity) {
        // Remove the least recently used node (tail.prev)
        const nodeToRemove = this.tail.prev;
        nodeToRemove.prev.next = this.tail;
        this.tail.prev = nodeToRemove.prev;
        this.cache.delete(nodeToRemove.key);
      }

      // Add the new node to the front of the list
      const newNode = { key, value, prev: this.head, next: this.head.next };
      this.head.next.prev = newNode;
      this.head.next = newNode;
      this.cache.set(key, newNode);
    }
  }
}

// Example usage:
const cache = new LRUCache(3);
cache.put(1, 'One');
cache.put(2, 'Two');
cache.put(3, 'Three');
console.log(cache.get(2)); // Output: 'Two'
cache.put(4, 'Four');
console.log(cache.get(1)); // Output: -1 (not found, as it was the least recently used)

```