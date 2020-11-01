---
title: "Fun with Primes"
date: "2019-09-26"
layout: page
---

Back in middle school, I had a fascination with prime numbers, and worked on creating lists of them in Visual Basic. I quickly ran into a problem: processors only work so fast, and if you create a large enough list of primes, you wind up waiting, and waiting, and waiting on the computer to finish up.

Let's revisit this issue in Python 3.

First we can start with a definition of a prime number: x is prime if it is divisible only by itself and one. So we can check every integer in `range(2, x)` for divisibility into x, and if none divide in evenly, x is prime. So a function that would work is

{% highlight ruby %}
def is\_prime(x):
    """Takes an integer and returns whether it is prime (True or False)"""
    if x < 2:
        return False
    for a in range(2, x):
        if x % a == 0:
            return False
    return True
{% endhighlight %}

And here's a little code we can use to take it for a spin:

{% highlight ruby %}
import time

for i in range(1, 6):
    start\_time = time.perf\_counter()
    prime\_list = \[\]
    for b in range(2, 10\*\*i):
        if is\_prime(b):
            prime\_list.append(b)
    end\_time = time.perf\_counter()
    print("Primes up to {0:8,} listed in {1:.6f} seconds.".format(10\*\*i, 
                                                end\_time - start\_time))
{% endhighlight %}

This prints (your mileage may vary):

```
Primes up to       10 listed in 0.000012 seconds.
Primes up to      100 listed in 0.000146 seconds.
Primes up to    1,000 listed in 0.006790 seconds.
Primes up to   10,000 listed in 0.369426 seconds.
Primes up to  100,000 listed in 26.382979 seconds.
```

Notice how rapidly the execution time is growing. We should be able to slice some time off by not wasting our time checking for even factors. If, for example, 101 is not divisible by two, it is not going to be divisible by 4, or 6, or 8 either. So we can basically remove half of our "checks" by dealing with "is it divisible by two" at the outset and then only checking potential odd factors after that.

{% highlight ruby %}
def is\_prime(x):
    """Takes an integer and returns whether it is prime (True or False)"""

    # Since 2 is the smallest prime, no integer smaller than 2 can be prime.
    if x < 2:
        return False

    # 2 is prime
    elif x == 2:
        return True

    # If x is larger than 2, we need to make sure it's odd
    elif x % 2 == 0:
        return False

    # Check all possible odd factors.
    for a in range(3, x, 2):
        if x % a == 0:
            return False
    return True
{% endhighlight %}

Now we can check how it stands up to a test like last time:

```
Primes up to       10 listed in 0.000013 seconds.
Primes up to      100 listed in 0.000104 seconds.
Primes up to    1,000 listed in 0.007753 seconds.
Primes up to   10,000 listed in 0.221982 seconds.
Primes up to  100,000 listed in 13.154828 seconds.
```

That's progress. We've shaved about half of the time off. While we're at it, we may as well quit checking even numbers for primeness, and revise our top-level code like so:

{% highlight ruby %}
import time

for i in range(1, 6):
    start\_time = time.perf\_counter()
    prime\_list = \[2\]
    for b in range(3, 10\*\*i, 2):
        if is\_prime(b):
            prime\_list.append(b)
    end\_time = time.perf\_counter()
    print("Primes up to {0:8,} listed in {1:.6f} seconds.".format(10\*\*i, 
                                                end\_time - start\_time))
{% endhighlight %}

Now the code prints:

```
Primes up to       10 listed in 0.000007 seconds.
Primes up to      100 listed in 0.000055 seconds.
Primes up to    1,000 listed in 0.002420 seconds.
Primes up to   10,000 listed in 0.167709 seconds.
Primes up to  100,000 listed in 12.319272 seconds.
```

This only shaves a little bit of time of our function, because the very brief `is_prime` calls for even numbers were returning `False` pretty quickly.

However, while we've been dithering about with even numbers, we've missed a much more powerful optimization. Note that the factors of 144, for example, are 1, 2, 3, 4, 6, 8, 9, 12, 16, 18, 24, 36, 48, and 72. We can, if we like, re-arrange these factors into pairs which multiply to yield 144: (1, 144), (2, 72), (3, 48), (4, 36), (6, 24), (8, 18), (9, 16) and, the oddball, (12, 12). What you should notice is that, for each of these pairs, one member is less than or equal to the square root of 144, while the other is greater than or equal to its square root.

More generally, we can say that where _a_, _b_, and _c_ are integers such that _a(a+b) = c_, and _a_ and _c_ are positive, while _b_ is not negative; then _a_ must be less than or equal to the square root of _c_. And if, for some integer _c_, there is no such _a_ other than 1, _c_ must be prime. The proof of this is left as an exercise for the reader.

The upshot of all this is that when we're checking for factors to disqualify a number _c_ as prime, we need search no higher than the square root of _c_. We may rewrite our algorithm to reflect this:

{% highlight ruby %}
def is\_prime(x):
    """Takes an integer and returns whether it is prime."""

    # Since 2 is the smallest prime, no integer smaller than 2 can be prime.
    if x < 2:
        return False

    # 2 is prime
    elif x == 2:
        return True

    # If x is larger than 2, we need to make sure it's not odd
    elif x % 2 == 0:
        return False

    # Check whether x has any relevant odd factors
    for a in range(3, int(math.sqrt(x)) + 1, 2):
        if x % a == 0:
            return False
    return True
{% endhighlight %}

And behold, it prints something marvelous:

```
Primes up to       10 listed in 0.000017 seconds.
Primes up to      100 listed in 0.000072 seconds.
Primes up to    1,000 listed in 0.000790 seconds.
Primes up to   10,000 listed in 0.010202 seconds.
Primes up to  100,000 listed in 0.136442 seconds.
```

For the 100,000 level, we're now running about 100 times faster. We can even extend our code a little to get a couple more powers of ten checked.

```
Primes up to  1,000,000 listed in 2.079361 seconds.
Primes up to 10,000,000 listed in 50.499986 seconds.
```

Although that's quite a bit of improvement, we can do better. We can do much better. Skipping over some of the subtleties, let's imagine, we had a list of every number from 1 to 10,000,000. In the course of checking for primeness, we need to eliminate numbers divisible by 47 (among many others). One way we can do this is to divide each number by 47. Is 2001 divisible by 47? Is 2002 divisible by 47? Is 2003 divisible by 47? And so we go. It would be much faster to simply cross every number divisible by 47 off our list. Then we could just start at 94 and add 47 over and over, traversing through the list and crossing off all those multiples.

So if we go by division, we need to check every number (not quite, but that's the basic idea). That's division, about 10,000,000 times. Or, we could use addition about 200,000 times. Addition will be much faster, even after we adjust for some of the complexities that make this example not quite right.

So what we can do, for, say, 100,000,000, is to make a list in which all the elements read, at first, "True". Then we mark all odd elements as "False", all elements divisible by three as "False", and so on. This will save us a great deal of trouble.

We can write this up like so:

{% highlight ruby %}
\# so we can time our program
import time
# so we can take a square root later
import math

for i in range(2, 9):
    start\_time = time.perf\_counter()
    # set how high we want our prime list to go
    list\_length = 10\*\*i
    # create the prime list, as an empty list at first
    prime\_checker = \[\]

    # create the prime list with True for each element
    # on the principle that every number is 'innocent' till proven guilty
    for a in range (list\_length):
        prime\_checker.append(True)

    # 0 is not prime, so let's eliminate that
    prime\_checker\[0\] = False
    # 1 is not a prime, so let's eliminate that
    prime\_checker\[1\] = False

    # eliminate all odd numbers larger than two, 
    # because they can't be prime
    for a in range (4, list\_length, 2):
        prime\_checker\[a\] = False

    # now, for each prime number, eliminate all larger
    # multiples of that prime -- they're not prime
    for a in range (3, int(math.sqrt(list\_length)) + 1, 2):
        if prime\_checker\[a\] == True:
            for b in range (2 \* a, list\_length, a):
                prime\_checker\[b\] = False

    # now the program is done, so we check how long it took
    end\_time = time.perf\_counter()
    run\_time = end\_time - start\_time
    print("Primes up to {0:12,} listed in {1:.6f} seconds.".format(10\*\*i, 
                                                    end\_time - start\_time))
{% endhighlight %}

This prints:

```
Primes up to          100 listed in 0.000041 seconds.
Primes up to        1,000 listed in 0.000348 seconds.
Primes up to       10,000 listed in 0.005333 seconds.
Primes up to      100,000 listed in 0.050560 seconds.
Primes up to    1,000,000 listed in 0.296285 seconds.
Primes up to   10,000,000 listed in 3.073970 seconds.
Primes up to  100,000,000 listed in 34.915086 seconds.
```

Notice that this version finds primes up 10,000,000 in just 3 seconds instead of 51 like the previous version, while it can even knock out primes up to 100,000,000 in 35 seconds. If you compare it to the first version of our program, it handles the 100,000 task 500 times faster. And this last version grows linearly with the number of numbers we check, while the earliest version grew at something approaching n^2. If it did grow at n^2, we can imagine that, if it took about half a minute for 100,000, its time to 100,000,000 would be about a million times larger, or, to use a famous round number, roughly [525,600 minutes](https://www.youtube.com/watch?v=-r2xXtSsPV0).

_This file is released under the [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) license._
