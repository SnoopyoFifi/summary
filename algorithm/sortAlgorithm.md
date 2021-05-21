
# 排序算法

## 冒泡排序

```js
const bubbleSort = arr => {
  let swapped = false;
  const a = [...arr];
  for (let i = 1; i < a.length - 1; i++) {
    swapped = false;
    for (let j = 0; j < a.length - i; j++) {
      if (a[j + 1] < a[j]) {
        [a[j], a[j + 1]] = [a[j + 1], a[j]];
        swapped = true;
      }
    }
    if (!swapped) return a;
  }
  return a;
};
```

## 快速排序

```js
const quickSort = arr => {
  const a = [...arr];
  if (a.length < 2) return a;
  const pivotIndex = Math.floor(arr.length / 2);
  const pivot = a[pivotIndex];
  const [lo, hi] = a.reduce(
    (acc, val, i) => {
      if (val < pivot || (val === pivot && i != pivotIndex)) {
        acc[0].push(val);
      } else if (val > pivot) {
        acc[1].push(val);
      }
      return acc;
    },
    [[], []]
  );
  return [...quickSort(lo), pivot, ...quickSort(hi)];
};

// Examples

quickSort([1, 6, 1, 5, 3, 2, 1, 4]); // [1, 1, 1, 2, 3, 4, 5, 6]
```

## 插入排序

```js
const insertionSort = arr =>
  arr.reduce((acc, x) => {
    if (!acc.length) return [x];
    acc.some((y, j) => {
      if (x <= y) {
        acc.splice(j, 0, x);
        return true;
      }
      if (x > y && j === acc.length - 1) {
        acc.splice(j + 1, 0, x);
        return true;
      }
      return false;
    });
    return acc;
  }, []);

// Examples

insertionSort([6, 3, 4, 1]); // [1, 3, 4, 6]
```

## 希尔排序

```js
const shellSort = (array,gaps) => {
  for (var g = 0; g < gaps.length; g++) {
    var gap = gaps[g]
    for (var i = gap; i < array.length; i++) {
      var temp = array[i]
      for (var j = i; j >= gap && array[j - gap] > temp; j -= gap) {
        array[j] = array[j - gap]
      }
      array[j] = temp
    }
  }
  return array
}
```

## 选择排序

```js
const selectionSort = arr => {
  const a = [...arr];
  for (let i = 0; i < a.length; i++) {
    const min = a
      .slice(i + 1)
      .reduce((acc, val, j) => (val < a[acc] ? j + i + 1 : acc), i);
    if (min !== i) [a[i], a[min]] = [a[min], a[i]];
  }
  return a;
};

// Examples

selectionSort([5, 1, 4, 2, 3]); // [1, 2, 3, 4, 5]
```

## 堆排序

```js
const heapsort = arr => {
  const a = [...arr];
  let l = a.length;

  const heapify = (a, i) => {
    const left = 2 * i + 1;
    const right = 2 * i + 2;
    let max = i;
    if (left < l && a[left] > a[max]) max = left;
    if (right < l && a[right] > a[max]) max = right;
    if (max !== i) {
      [a[max], a[i]] = [a[i], a[max]];
      heapify(a, max);
    }
  };

  for (let i = Math.floor(l / 2); i >= 0; i -= 1) heapify(a, i);
  for (i = a.length - 1; i > 0; i--) {
    [a[0], a[i]] = [a[i], a[0]];
    l--;
    heapify(a, 0);
  }
  return a;
};

// Examples

heapsort([6, 3, 4, 1]); // [1, 3, 4, 6]
```

## 归并排序

```js
const mergeSort = arr => {
  if (arr.length < 2) return arr;
  const mid = Math.floor(arr.length / 2);
  const l = mergeSort(arr.slice(0, mid));
  const r = mergeSort(arr.slice(mid, arr.length));
  return Array.from({ length: l.length + r.length }, () => {
    if (!l.length) return r.shift();
    else if (!r.length) return l.shift();
    else return l[0] > r[0] ? r.shift() : l.shift();
  });
};

// Examples

mergeSort([5, 1, 4, 2, 3]); // [1, 2, 3, 4, 5]
```

## 计数排序

```js
let countingSort = (arr, min, max) => {
  let i = min,
      j = 0,
      len = arr.length,
      count = [];
  for (i; i <= max; i++) {
    count[i] = 0;
  }
  for (i = 0; i < len; i++) {
    count[arr[i]] += 1;
  }
  for (i = min; i <= max; i++) {
    while (count[i] > 0) {
      arr[j] = i;
      j++;
      count[i]--;
    }
  }
  return arr;
};
```

## 桶排序

```js
const bucketSort = (arr, size = 5) => {
  const min = Math.min(...arr);
  const max = Math.max(...arr);
  const buckets = Array.from(
    { length: Math.floor((max - min) / size) + 1 },
    () => []
  );
  arr.forEach(val => {
    buckets[Math.floor((val - min) / size)].push(val);
  });
  return buckets.reduce((acc, b) => [...acc, ...b.sort((a, b) => a - b)], []);
};

// Examples

bucketSort([6, 3, 4, 1]); // [1, 3, 4, 6]
```

## 基数排序

```js
getMax = array => { // O(n)
  let max = 0
  for (let num of array) {
    max = (max < num.toString().length) ? num.toString().length : max
  }
  return max
}

getPosition = (num,place) => Math.floor(num / Math.pow(10,place)) % 10 // O(1)

radixSort = array => { // O(nk)
  let max = getMax(array);
  for (let i = 0; i < max; i++) {
    let buckets = Array.from({length:10}, () => [])
    for (let j = 0; j < array.length; j++) {
      buckets[getPosition(array[j], i)].push(array[j])
    }
    array = [].concat(...buckets)
  }
  return array
}
```

## 稳定排序

```js
const stableSort = (arr, compare) =>
  arr
    .map((item, index) => ({ item, index }))
    .sort((a, b) => compare(a.item, b.item) || a.index - b.index)
    .map(({ item }) => item);

// Examples

const arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const stable = stableSort(arr, () => 0); // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

## 延伸阅读

- [十大经典排序算法（动图演示）](https://www.cnblogs.com/onepixel/p/7674659.html)
- [tylerewillis](https://tylerewillis.com/pages/sorting)
- [30secondsofcode](https://www.30secondsofcode.org/search?keyphrase=sort)
- [javascript-algorithms](https://medium.com/javascript-algorithms)
