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


