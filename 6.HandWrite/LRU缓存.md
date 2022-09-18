```js
const lruCache = new LRUCache(2);
lruCache.set(1, 1) // {1 = 1}
lruCache.set(2, 2) // {1 = 1, 2 = 2} 
lruCache.get(1)    // 1 {2 = 2, 1 = 1}  
lruCache.set(3, 3) // {1 = 1, 3 = 3}
lruCache.get(2)    // null  
lruCache.set(4, 4) // {3 = 3, 4 = 4}

// 思路
// 用哈希表(逻辑结果，js中可以用Map 和 对象来体现)存储数据，这样get set的时间复杂度是O(1)
// 有序、可排序  -> 用Map来表示(对象的键值对是无序的)

// 可见Map真的厉害，即是有序的又是哈希表   Set也是有序的，但是不能支持排序

class LRUCache {

  constructor(length) {
    if (length < 1) throw new Error('无效长度！')
    this.length = length
    this.data = new Map();
  }
  set(key, value) {
    const data = this.data;
    if (data.has(key)) {
      data.delete(key)
    }
    data.set(key, value)   // 删了重建，处理顺序问题
    if (data.size > this.length) {
      // 删除最老的元素    .keys().next().value 这个方法用得很好，应该是迭代器的用法
      const delKey = data.keys().next().value
      data.delete(delKey);
    }
  }
  get(key) {
    const data = this.data;
    if (!data.has(key)) return null;

    // 删了重建
    const value = data.get(key)
    data.delete(key);
    data.set(key, value);
    // 有返回值
    return value
  }
}
```