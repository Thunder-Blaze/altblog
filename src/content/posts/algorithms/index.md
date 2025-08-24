---
title: Algorithms
description: Some to remember CP Algos & Properties
published: 2025-02-01
image: 'cover.jpg'
category: CP
tags: [Frontend, CP]
draft: false
---

## **Algorithms & Properties**

### Possible sum of K distinct integers between 1 and N

let the sum be S,  
we can form all numbers till $\dfrac{N(N+1)}{2}$  
but if we are to use fixed K digits, the Range will be limited to $(K)(N)+(K-1)$

### Sliding Window

- *Use deque or vector when you also need to access every element while sliding*
- **Sliding Window** for Maximum sum of K continous elements of vector *vec*

```C++
vector<int> vec(n);
// Take Input
queue<int> sm;
int sum=0;
for (int i=0; i<n; i++){
    if (sm.size()>k){
        sum-=sm.front();
        sm.pop();
    }
    sm.push(vec[i]);
    sum+=vec[i];
    ans=max(sum,ans);
}
```

> Photo by [Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski) on [Unsplash](https://unsplash.com/)
