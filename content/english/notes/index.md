---
title: "Notes"
# meta title
meta_title: ""
# meta description
description: "This is meta description"
# save as draft
draft: false
---

## Dynamic Programming
Weighted interval scheduling : Maximize the total weight of the bookings that are selected.
Many problems have an inductive structure but there is the probabilty that problems will be repeated.

Memoization is checking id the value needed has already been computed before.

 ```
 def fib(n):
	if n in fiblist.keys():
		return fiblist[n]
	if n<=1:
		return n
	else:
		value = fib(n-1) + fib(n-2)
		fiblist[n] = value
		return value
```
Solve  problems in topological order of dependency, this way wehen we need to evaluate a problem,all the previous subproblems have been already calculated.
Memoization is top down approach while Dynamic programming is a bottom up approach.

### 1. Grid path
**Manhattan problem**
In a 10x5 grid moving from bottom left to top right , youll make 5 rights and 10 ups. 
Fix position of 5 moves means total paths are 15C5 or 15C10, both same values.
![961b1c9d4bdc5d5e47384d2ca2c46f65.png](:/4fbdcd38f72a448199eb32869a30543e)

If a certain intersection is blocked, subtract paths from origin to that point and then from that point to destination.
If 2,4 is blocked, 6C4 and 9C6 are eliminated. 
 ![0e9fd07ba02012af6986aa6ebf5580f7.png](:/d05727340a5044a29279ac6401b1f932)
 Computing number of possibilities
 ![3c61924dc8a1a269d12bb3b53203e92b.png](:/58a2258afa7843b3b510a0c2f63702eb)
Funnily enough if there is a barrier of holes, dynamic programming will be slower since memoization will not compute the region being unused.

### 2.Common subwords
Find the longest matching substring given two words.  
Common sense one gives O(m\*n<sup>2</sup>) i.e,by comparing every letter in i and j
Instead by using inductions:
LCW(i,j) = 1 + LCW(i+1,j+1)
![5998e206fcdf604622c7233ff77f1218.png](:/f2ad6baf7a974f9aab3d4c410a087017)
from bottom right corner, each value is sum of 1 (if matched) and diagonal right
```
def LCW(u,v):
	import numpy as np
	(m,n) = (len(u),len(v))
	lcw = np.zeroes(m+1,n+1)
	maxclw = 0
	
	for c in range(n-1,-1,-1):
		for r in range(m-1,-1,-1):
			if u[r] == v[c]:
				lcw[r,c] = 1+lcw[r+1][c+1]
			else:
				lcw[r,c]  = 0
			if lcw[r,c] > maxlcw:
				maxlcw = lcw[r,c]
		return maxlcw
```
This is order O(mn)

### 3.Common subsequence
Subsequence is where words can be dropped.
*diff* command is an example that finds longest common subsequence.
![ce573ed2521cb36814524a9bf5e20c99.png](:/f8e647cd87d745518fb56a20c8d667a3)
![c9a2f8607a332ac3900f01c5437ae80c.png](:/8dc76015ec05412897d48b5f58460b01)
It depends on LCS(i+1,j+1), max[L(i,j+1),L(i+1,j)]
If i=j, value is 1 + value of(i+1,j+1)
else value is max(LCS(i+1,j),LCS(i,j+1))
```
def LCS(u,v):
	import numpy as np
	(m,n) = (len(u),len(v))
	lcs = np.zeros((m+1,n+1))
	for c in range(n-1,-1,-1):
		for r in range(m-1,-1,-1):
			if u[r] == v[c]:
				lcs[r,c] = 1 + lcs[r+1,c+1]
			else:
				lcs[r,c] = max(lcs[r+1,c],
lcs[r,c+1])

	return(lcs[0,0])
```
### 4.Document Similarity
Compute the number of insertions,deletions and substitutions.
![4c4ca57e388194029773e56f4fa20eb6.png](:/720f1cbfee52467c92104240ee25924a)
Levenshtein's distance is minimum number of editing operations needed to transform one document to the other. 
We realize that deleting and inserting are symmetric, changing abc to xbc involves deleting a and x or deleting a inserting x or deleting x addign a.

To transform u to v:
If a<sub>i</sub> = b<sub>j</sub>, nothing is to be done
If a<sub>i</sub> not equal to b<sub>j</sub> we have three options:
1. substitute a<sub>i</sub> by b<sub>j</sub>
2. delete a<sub>i</sub> 
3. insert b<sub>j</sub> before a<sub>i</sub>
ED(i,j) = 1 + min { ED(i+1,j+1),ED(i+1,j),ED(i,j+1) }
Base cases:
ED(m,n) = 0
ED(i,n) i.e, if second string is empty, them we do m-i deletes on first string
ED(m,j) i.e, if second string is empty then do m-j deletes on the second string

**Ed(i,j) is value at ED(i+1,j+1) or 1+ min(ED(i+1,j+1),ED(i,j+1),ED(i+1,j))**
![8031a8802df6b572479767ce3da7f2b0.png](:/8f1dca00474143bbb99c831bb7a10991)
Moving right is insertion, i.e r and e
moving down is deleting
```
def ED(u,v):
	import numpy as np
	(m,n) = (len(u),len(v))
	ed = np.zeroes((m+1,n+1))
	
	for i in range(m-1,-1,-1):
		ed[i,n] = m-i
	for j in range(n-1,-1,-1):
		ed[m,j] = n-j
	
	for j in range(n-1,-1,-1):
		for i in range(m-1,-1,-1):
			if u[i] == v[j]:
				ed[i][j] = ed[i+1][j+1]
			else:
				ed[i][j] = 1 + min(ed[i+1][j+1],ed[i,j+1],ed[i+1,j])
	return (ed[0][0])
```
Another algo of time O(mn)
![9035362d79e2c9b93e5ec8f2c99c09ee.png](:/36d95330ff6048e5bd06d6996b313d4c)
### 5.Multiplying matrices
```
A[i][k]*B[k][j]
Overall cost is o(mnp)
where A = mxn and B is nxp
We aren't changing the process just finding an optimal order in which to compute them.
```
Cost C(0,n-1) = C(0,k-1) + C(k,n-1) + r<sub>0</sub>r<sub>k</sub>C<sub>n-1</sub>
![94853125dc9cee8da0c2422941460c83.png](:/acb89e60e33d449eac4f68222fc5ba00)
```
def C(dim):
	#dim is a dimension matrix where each value is [(r_i,c_i)..]
	import numpy as np
	n = dim.shape[0]
	C = np.zeros((n,n))
	
	#Primary diagonal is zero
	for i in range(n):
		C[i][i] = 0
		
	for diff in range(1,n):
		for i in range(0,n-diff):
			j = i+diff
			C[i][j] = C[i][i] + C[i+1][j] + dim[i][0]*dim[i+1][0]*dim[j][i]
		
	for k in range(i+1,j+1):
		C[i,j] = min(C[i,j],C[i,k-1]+C[k,j]+dim[i][0]*dim[k][0]*dim[j][i])
	return(C[0,n-1])
```
![40f8c9817fecca93bc167dd04415ea2b.png](:/fc4e42d6e17c42bba2758c6eb868081b)