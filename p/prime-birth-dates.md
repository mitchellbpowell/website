---
title: Prime birth dates
description: A silly math exercise
layout: post
---

_2020-10-24_

Prime birth dates

The excellent _How to Think Like a Computer Scientist: Learning with Python 3_,
has a fascinating little wrinkle in exercise 10 of its [seventh
chapter](http://openbookproject.net/thinkcs/python/english3e/iteration.html).
It suggests that the number 19911121 could represent someone’s birth date,
presumably someone born on November 21, 1991. 19911121 is a prime number so we
could say that this person has a _prime birth date_. It’s a little silly, but
then the text asks us how many students, out of a class of 100, might have a
prime birth date.

Well, let’s suppose that our students, maybe driver’s ed students or something,
were all born in 2003. Let’s use a scheme where a student born on January 1 is
assigned a birth date number of 20030101, and a student born on December 31 is
assigned a number of 20031231.

One thing that would be nice is a means to store all the possible numbers for
this class in a list. Here’s a function to make such a list:

{% highlight ruby %}
def make_date_numbers():
    our_list = []               # We'll add all possible values to this list
    month_days = {1: 31,        # Number of days in each month. Since we're
                  2: 28,        # only concerned with 2003, no need to 
                  3: 31,        # worry about leap years.
                  4: 30,
                  5: 31,
                  6: 30,
                  7: 31,
                  8: 31,
                  9: 30,
                  10: 31,
                  11: 30,
                  12: 31
    }
    for i in range(1, 13):
        for j in range (1, month_days[i] + 1):
            date_num = 20030000 + i*100 + j
            our_list.append(date_num)
    return our_list
{% endhighlight %}

Then we’ll need a function to tell whether a given number is prime.

{% highlight ruby %}
def is_prime(x):
    """Takes a positive integer and tells if it's prime."""
    if x == 1:
        return False
    if x == 2:
        return True
    if x % 2 == 0:
        return False
    for i in range(3, int(x**0.5 + 1), 2):
        if x % i == 0:
            return False
    
    return True
{% endhighlight %}

Now all we need to do is call our function to make a list of all the possible
date numbers, test each item in the list for primeness, and increment a counter
each time a prime is found. Then the computer can spit out an answer for us.

{% highlight ruby %}
date_list = make_date_numbers()
counter = 0
for date in date_list:
    if is_prime(date):
        counter += 1
print(counter, "of 365 days are prime. That's about", round(100*counter/365), 
                                                            " percent." )
{% endhighlight %}

This prints

```
19 of 365 days are prime. That's about 5  percent.
```

So, there you have it, folks. If you’re ever in a room with one hundred people
born in 2003, and you need to guess how many of them have prime number
birthdays according to this arbitrary scheme, “five” is a reasonable guess.


---

_This page is released under the [CC0
1.0](https://creativecommons.org/publicdomain/zero/1.0/) license._

