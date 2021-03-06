### Problem 2:

Source: https://projecteuler.net/problem=2

Statement:
> Each new term in the Fibonacci sequence is generated by adding the previous two terms. By starting with 1 and 2, the first 10 terms will be:
                                          1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...
By considering the terms in the Fibonacci sequence whose values do not exceed four million, find the sum of the even-valued terms.

The easy solution is to iterate over the fibonacci series and sum the even-valued terms. This would be O(n), where n is the number of terms you will have to traverse, in order to get those terms which are less than the limit.

##### Approach

When I started this problem, I could remember that there is a recurrence relation that satisfies the fibonacci series. That relation can easily be derived:

![equation](http://www.sciweavers.org/tex2img.php?eq=%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0A1%20%26%201%20%5C%5C%0A1%20%26%200%20%5C%5C%0A%5Cend%7Barray%7D%20%5Cright%29%20%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0AF_n%20%5C%5C%0AF_n_-_1%20%5C%5C%0A%5Cend%7Barray%7D%20%5Cright%29%20%3D%20%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0AF_n_%2B_1%20%5C%5C%0AF_n%20%5C%5C%20%0A%5Cend%7Barray%7D%20%5Cright%29%0A%0A%0A&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

So, what if I could derive a relation like this for the new series:
```
         2,    8,    34,    144,    610, ...  , F(n)
or      F(2), F(5), F(8),  F(11),  F(14), ... , F(n)
or      G(1), G(2), G(3),  G(4),   G(5), ..., G((n+1)/3)
or      G(1), G(2), G(3),  G(4),   G(5), ..., G(m)
where,  m = (n+1)/3
```

Let's derive this relation, which will give us a matrix like {1 1 ; 1 0} for the new series. Now what we want is to find G(m+1), given G(m) and G(m-1).
In, simple we know every 3 alternate terms. Why not find a formula in terms of these terms.


Or
```
We know:
G(m) or F(3m-1),
G(m-1) or F(3m-4)

We want to find:
G(m+1) or F(3m+2)

Let,
F(3m-2) = F(3m-4) + F(3m-3)
F(3m-2) = F(3m-4) + F(3m-1) - F(3m-2)       {:: Since, F(n) = F(n+2) - F(n+1)}
2F(3m-2) = F(3m-4) + F(3m-1)
F(3m-2) = ( F(3m-4) + F(3m-1) )/2

Also,
F(3m) = F(3m-2)  + F(3m-1)
      = ( F(3m-4) + F(3m-1) )/2 + F(3m-1)
      = ( F(3m-4) + 3F(3m-1) )/2

And,
F(3m+1) = F(3m-1) + F(3m)
        = F(3m-1) + ( F(3m-4) + 3F(3m-1) )/2
        = ( F(3m-4) + 5F(3m-1) )/2

Finally,
F(3m+2) = F(3m) + F(3m+1)
        = ( F(3m-4) + 3F(3m-1) )/2 + ( F(3m-4) + 5F(3m-1) )/2
        = F(3m-4) + 4F(3m-1)

So,

G(m+1) = 4*G(m) + G(m-1)
```

This can be represented as:

![equation](http://www.sciweavers.org/tex2img.php?eq=%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0A4%20%26%201%20%5C%5C%0A1%20%26%200%20%5C%5C%0A%5Cend%7Barray%7D%20%5Cright%29%20%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0AG_m%20%5C%5C%0AG_m_-_1%20%5C%5C%0A%5Cend%7Barray%7D%20%5Cright%29%20%3D%20%5Cleft%28%0A%5Cbegin%7Barray%7D%7Bccc%7D%0AG_m_%2B_1%20%5C%5C%0AG_m%20%5C%5C%20%0A%5Cend%7Barray%7D%20%5Cright%29%0A%0A%0A&bc=White&fc=Black&im=jpg&fs=12&ff=arev&edit=0)

##### Solution

There are two parts to the solution:

1. Find the G(n) and G(n+1), for which G(n) < limit and G(n+1) >= limit
2. Apply Formula (Proof ahead):
```
Sn = ( G(n) + G(n+1) - 2 ) / 4
```

Now, that we have the matrix `A = {4 1 ; 1 0}`, we can simply use it to calculate the desired number in the new _Even Fibonacci Series_. If we want to find `G(n)` simply have to multiply it **_n_** times to itself and then to the initial vector of {8,2}. This will give the _mth_ element in the series.

Uptil now the solution is O(n) as we have still to do n multiplications for finding nth element. We can take advantage of matrix property to exponentiate the power of **_A_**.
For example, if we want to find G(16) we just have to exponentiate the matrix A. The resultant matrices will be: A, A^2, A^4, A^8, A^16. In this way, we can find the required matrix in just 5 multiplications.

What about the elements which lie in between 8 and 16: we can simply do binary search between 8 and 16.

I have also saved the subsequent matrices being produced in a dictionary, as they can be used for powers which lie in between m and 2*m. For example, if you are binary searching between 8 and 16, you will have to calculate 12th power of the matrix A. You don't have to do it from start. Just multiply the matrices, A^8 and A^4 we computed earlier and saved in the dictionary.

So, the complexity of this solution is O(log n).

##### Proof for sum

```
Let,
G(n+2) = G(n) + 4G(n+1)
or
G(n) = G(n+2) - 4G(n+1)


Summing:
G(n) = G(n+2) - 4G(n+1)
G(n) = G(n+1) - 4G(n)
.
.
.
G(2) = G(4) - 4G(3)
G(1) = G(3) - 4*G(2)
-------------------------
Sn  = [ G(n+2) - 4G(2) ] + [ - 3G(n+1) - 3G(n) - ... - 3G(3) ]
    = G(n+2) - 3G(n+1) - G(2) - [ 3G(n+1) + 3G(n) + ... + 3G(3) + 3G(2) + 3G(1) ] + 3G(1)
    = [ G(n+2) - 4G(n+1) ] + G(n+1) - G(2) + 3G(1) - 3Sn
4Sn = G(n) + G(n+1) - 8 + 3(2)
Sn  = (G(n) + G(n+1) - 2) / 4
```

Here are the solutions for this problem:
- [Go](go/problem2.go)
- [Haskell](haskell/problem2.hs)