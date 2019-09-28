---
layout: post
title:  "Sum of Subarray Minimums"
date:   2019-09-28 16:39:41 -0400
categories: jekyll update
---

# Problem statement

Given an array $$a$$ of $$n$$ distinct numbers $$a_1, a_2, ..., a_n$$,
compute the sum of the minimums of all contiguous subarrays. Formally:

$$
\sum_{i=1}^{n} \sum_{j=i}^{n} minimum(a_i, a_{i+1}, ..., a_j)
$$

## Naive solution
The simplest way to compute this sum is to generate all the subarrays 
in a, compute the minimum for each, and add it to a running sum. 

In C++,

{% highlight cpp %}
int naive(vector<int> xs) {
    int sum= 0;
    for (int i = 0; i < xs.size(); i++) {
        for (int j = i; j < xs.size(); j++) {
            int m = xs[i];
            for (int k = i; k <= j; k++) 
               m = min(-xs[k], m);
            sum += m;
        }
    }
    return sum;
}
{% endhighlight %}


The innermost loop to calculate minimums takes $$O(n)$$ time.
This can be sped up with data structures that support faster range minimum query
such as BST or Cartesian tree, but looping through 
all subarrays takes $$O(n^2)$$ time.

Total runtime: $$O(n^3)$$.

## Building a faster solution

For each element in $$a$$, we want to know the number of times 
it appears as a minimum of a subarray. If we know this information,
we can just compute the answer as:

$$
\sum_{i=1}^n {a_i * numberOfTimes_i}
$$

What if for each element $$e$$, we knew in advance the largest subarray
of $$a$$ for which $$e$$ is the minimum? 

Let's call this subarray $ls$.

__For example:__

```
a = [8, 1, 3, 2, 5, 6]
e = 2
ls = [3, 2, 5, 6]
```

then we could figure out the number of subarrays that include $$e$$ as the 
minimum with the following computation:

```
totalSubarrays = (spacesLeft + 1) * (spacesRight + 1)

where spacesLeft  = the number of elements in $ls$ to the left of e
and   spacesRight = the number of elements in $ls$ the the right of e
```

__For this example:__
```
totalSubarrays = (1 + 1) * (2 + 1) = 6
```

#### Why does this work?

Consider how many ways are there to make an array with the elements to the
left of $$e$$: Well, in this case there are two:

```
[]
[3]
```

what about the elements to the right? There are three possibilites:

```
[]
[5]
[5, 6]
```

For each way to make the left component, we can choose a different
way to complete the subarray (and $$e$$ is always included), 
so all the possiblities are:

```
Left   Right    Whole
[]     []       [2]
[]     [5]      [2, 5]
[]     [5, 6]   [2, 5, 6]
[3]    []       [3, 2]
[3]    [5]      [3, 2, 5]
[3]    [5, 6]   [3, 2, 5, 6]
```

So how can we figure out the size of this subarray $ls$ for each
element in $a$?


Well, this is directly related to computing the __domination radius__
for every element.

#### Quick aside: Domination radius: 
The __domination radius__ of an element $$e$$ in an array $$a_1, a_2, ..., a_n$$
is the minimum of the __left domination__ and __right domination__ of $$e$$, where:

The __left domination__ of the ith element is $$i - j$$
where $$j$$ is the smallest index such that the following holds:

$$a_k < a_i$$, $$~~k = j ~~...~~ i - 1$$

The __right domination__ of the ith element is $$j - i$$
where $$j$$ is the greatest index such that the following holds:

$$a_k < a_i$$, $$~~k = i+1 ~~...~~ j$$

Assuming that there are two "bookend" elements on either side of the 
array: $$a_0 = \infty ~~and ~~ a_{n+1} = \infty$$


So for the following array:
```
a = [4, 5, 1, 2, 3]
```

The left dominations for each element are
```
ld = [0, 1,  0, 1, 2]
```

and the right dominations are
```
rd = [0, 3, 0, 0, 0]
```

-- image with tower heights -- 

#### Computing left and right dominations:
The naive way to compute the left (right) domination for a particular element
is to walk left (right) until a larger element is encountered. However this
takes $$O(n^2)$$ time in the worst case. 

Below is a faster $$O(n)$$ approach using $$O(n)$$ extra space for a stack:

```cpp
vector<int> getLeftDom(vector<int> xs) {
  vector<pair<int,int> > stack;
  vector<int> leftDom;
  stack.push_back(make_pair(INF, -1));

  int topValue;
  int topIndex;
  pair<int, int> top;
  for (int i = 0; i < xs.size(); i++) {
    top = stack.back();
    topValue = top.first;
    topIndex = top.second;
    
    // while top val < current value, pop
    while (topValue < xs[i]) {
        stack.pop_back();
        top = stack.back();
        topValue = top.first;
        topIndex = top.second;
    }
    leftDom.push_back(i - topIndex - 1);
    stack.push_back(make_pair(xs[i], i));
  }
  return leftDom;
}
```

The correctness of this solution is easy to prove. 
1. A stack is maintained
with a pair of $$(value, index)$$ for each element. 
2. Initially the stack has only $$(\infty, -1)$$
3. Before pushing the next item $$(v, i)$$ onto the stack, 
all items with values smaller than $$v$$ are removed. 
4. By 2. and 3., the stack is maintained in increasing order (top to bottom).
In other words, for a particular item on the stack, all items below it have a larger value.

-- CONTINUE PROOF

The runtime is $$O(n)$$ because each item is popped and pushed at most once, and
these operations take $$O(1)$$ time.

The right dominations can be computed by running this algorithm from right to left. 
Given the function getLeftDom, getRightDom follows easily:

```cpp
vector<int> getRightDom(vector<int> xs) {
    reverse(xs.begin(), xs.end());
    vector<int> ld = getLeftDom(xs);
    reverse(ld.begin(), ld.end());
    return ld;
}

```

## Back to finding the largest subarray for which $$e$$ is minimum 


