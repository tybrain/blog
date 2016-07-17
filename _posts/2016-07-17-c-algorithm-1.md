---
title: "C++ algorithm"
layout: post
category: 
date: 2016-07-17 10:53:37 EDT
---

This is simply a compilation of the `<algorithm>` in the newer version of c++ (c++1y). 

### find

```c++
	// v is a container and val is of type T
	auto p = find( begin(v), end(v), val);   // iterator point to val
```

### min_element, max_element

Return the forward iterator of the "smallest/largest" value in range [first, last) (not necessarily sorted). Here the meaning of <span class="orange">"*smallest/largest*"</span> is relative. It depends on the definition of the comparison functor. 

```c++
	// v = {4,6,3,9,2,5}
	auto p = std::min_element(v.begin(), v.end(), std::less<>{}); // *p = 2
	p =  std::min_element(v.begin(), v.end(), std::greater<>{}); // *p = 9
```

### lower_bound, upper_bound

They are a bit different from min_element/max_element in that they require the container be partially sorted to be meaningful. In other words, the container needs to be separated by a point into two groups: less group, no less group. This is where the meaning of *bound* comes from. They are actually a bit tricky. Let us ignore the meaning of lower/upper. In stead, we just examine the comparison functor, com(x, val) for every x in the container. The result should be like:

```c++
    //v = {1, 4, 6, 3, 2, 7, 9, 11, 13}, val = 7, comp(x,val)= std::less<>{} 
 comp(x,val):   T, T, T, T, T, F, F, F, F
```

In this example, the lower bound is 7, upper bound is 9. The quick way to think of this: in terms of the comparison function, we map the container into a list of boolean:

- lower_bound is the first F counting from right in this list
- upper_bound is the first F (and not equal val) counting from right in this list

For a sorted list in ascending order, if val is in the container, lower_bound points to the first val and upper_bound points to the first item greater than val. [lower_bound, upper_bound) is essentially the range of repetition of val. These methods are sometimes very handy.

```c++
// v = { 3, 4, 4, 4, 4, 5, 6, 8, 9, 10}, com = std::less<>{} 
auto p = std::lower_bound(v.begin(), v.end(), 4 );  // 3, (p)4, 4, 4 ...
p = std::upper_bound(v.begin(), v.end(), 4 );  // 3, 4, 4, 4, 4, (p)5, 6, ...
```

### rotate

rotate simply interchange two consequtive trunks given by [trunk1_head, trunk2_head, trunk2_end).

```c++
// v = { 1, 2, 3, 4, 5, 6, 7, 8, 9}, *it1 = 1, *it=4, *it2=7
std:rotate(it1, it, it2);  //  { 4, 5, 6, 1, 2, 3, 7, 8, 9}
```


### partition

partition arranges the list such that the list is put into two groups by the test function, {T,T,T,..T, F,F,...F}, with n Ts followed by (N-n) Fs. It returns the iterator at the first F.

```c++
// v = { 1,4,5,3,9,8,2,6},  n = 4
auto p = partition( v.begin(), v.end(), [](auto a){ return a <= 4; });  // v = { 1,4,2,3,9,8,5,6} unstable 
p = stable_partition( v.begin(), v.end(), [](auto a){ return a <= 4; });  // v = { 1,4,3,2,5,9,8,6} stable 
std::cout << *p << "\n";  // 5
```

### nth_element

A related method to the partition method, which achieves the similar functionality. 

```c++
// v = { 1,4,5,3,9,8,2,6},  n = 4
std::nth_element( v.begin(), v.begin()+4, v.end() );  // v = { 3,2,1,4,5,6,8,9} unstable 
```

### random_shuffle

Shuffle the elements in the range given.

```c++
std::random_shuffle( v.begin(), v.end() );
```

### inplace_merge

Merge sort two consequtive sorted trunks of a list in place. 

```c++
// v = { 1,3,5,7,9, 2,4,6,8,10,12}
std::inplace_merge(v.begin(), v.begin()+5, v.end() ); // v={1,2,3,4,5,6,7,8,9,10,12}
```

### copy

copy(source_begin, source_end, destination_begin)

```c++
// v = {1,2,3,4,5,6,7,8,9}
std::copy( v.begin() + 2, v.end(), v.begin() );  // v={3,4,5,6,7,8,9,8,9}
// a different way to output result, copy to cout stream
std::copy( v.begin(), v.end(), ostream_iterator<int>("cout", " "));  

```

### accumulate 

```c++
// v = {1,2,3,4,5}
int init_value = 3
std::accumulate( v.begin(), v.end(), init_value ); //18
```

### heap related methods 

**push_heap, pop_heap, sort_heap**

push_heap(RanIt_begin, RanIt_cur) inserts *RanIt_cur in heap, assuming [RanIt_begin, RanIt_cur-1] is a heap;
pop_heap(RanIt_begin, RanIt_end) move *RanIt_begin to *(RanIt_end-1), then heapify

```c++
// v = { 2,4,6,8,1,3,5,7,9}
for( auto it = v.begin(); it != v.end(); ) {
	std::push_heap(v.begin(), ++it );  
} 
// v = {9,8,5,7,1,3,4,2,6}

for( auto it = v.end(); it != v.begin(); ) {
	std::pop_heap(v.begin(), it-- );
}
// v = {1,2,3,4,5,6,7,8,9}, same as what after sort_heap()
```


