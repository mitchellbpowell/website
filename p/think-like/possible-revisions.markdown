---
title: HTLCS:LP3 -- Possible Revisions
description: Some notes on the book
layout: post
---

*2019*

From time to time, I see little mistakes in *[How to Think Like a Computer Scientist: Learning with Python 3](http://openbookproject.net/thinkcs/python/english3e/index.html)*. It appears to be no longer actively maintained, so I'll just park these here in case someone one day wants to work on it.

# Forward

In the [Forward](http://openbookproject.net/thinkcs/python/english3e/foreword.html), the ending reads like so:

"Although Python is still a young and evolving language, I believe that it has a bright future in education. This book is an important step in that direction. David Beazley University of Chicago Author of the *Python Essential Reference*"

I imagine someone might want to do something about the formatting of that last bit about David Beazley.

# Preface

I think we should change "in the Python version it becomes" to "In the Python version it becomes".

We should change "minutia" to "minutiae".

# Preface-3

We should replace "don't" with "doesn't" in  "Python (in company with languages like Javascript, Ruby, Perl, PHP, etc.) don’t really emphasize".

Instead of "Odds-and-ends", I think we should go with "odds-and-ends".

# Contributor List

"thoughtfull" to "thoughtful"

"they have make" to "they have made"

# 1.1

"is called the Python Interpreter:" to 
"is called the Python Interpreter."

# 1.11

"a" to "A" in "a sequence of instructions that specifies to a computer".

# 2.6

Add comma after "floor division" in "The second, called **floor division** uses the token //."

# 2.14.2

"parenthesis" to "parentheses"

# 3.4 Flow of Execution of the for loop

"Execution" to "execution".

# 4.1

Add a comma after "loop" in "We've already seen the for loop which follows this pattern."

"The parameters specifies" to "The parameters specify".

Delete either "of" or "about" in "of which we'll talk more about."

# 4.5

Semicolon after "void function" in "Languages like Java, C#, C, and C++ use the term 'void function', other language like Pascal call it a **procedure**."

# 4.9.7

Add a comma between `10` and "which".

# 5.10

Replace comma with semicolon after "licence when our age is greater or equal to 17".

# 5.12

The "final program" in this section seems to be lacking an ```import turtle```.

# 6.6

"functons" to "functions"

# 6.9.15

"test" to "tests" in "its test still pass".

# 7.12

A function is used to generate individual lines of a multiplication table.

{% highlight ruby %}
def print_multiples(n):
    for i in range(1, 7):
        print(n * i, end="   ")
    print()
{% endhighlight %}

When called repeatedly for n = 1 ... 7, it produces, according to the textbook, the following table:

```
1      2      3      4      5      6
2      4      6      8      10     12
3      6      9      12     15     18
4      8      12     16     20     24
5      10     15     20     25     30
6      12     18     24     30     36
```

Unfortunately, when I do get it, I get this:

```
1   2   3   4   5   6   
2   4   6   8   10   12   
3   6   9   12   15   18   
4   8   12   16   20   24   
5   10   15   20   25   30   
6   12   18   24   30   36  
```
I can get a nicely formatted table if I alter the line ```print(n * i, end="   "``` to ```print(n * i, end="\t")```. It looks to me like we'd need a tab of some kind instead of the three spaces. But I'm new to this, so I could be missing something.

# 7.14

This section links out to http://netserv.ict.ru.ac.za/python3_viz/, which doesn't seem to exist any more.

# 7.26.13

Add a space after "article" in "Now read Wikipedia’s article(http://en.wikipedia.org/wiki/Eulerian_path) about Eulerian paths."

# 7.26.16

Add comma after "16".

# 10.2

"is)." to "is.)"

# 10.6.3

Add a comma after ```showturtle``` in the sentence, "In an earlier chapter we saw two turtle methods, ```hideturtle``` and ```showturtle``` that can hide or show a turtle."

# 11.13

The infobox at the end of this section is outdated now that the Python visualizer no longer exists at the linked address.

# 11.22.6

Add a comma after "v" in "Write a function ```scalar_mult(s, v)``` that takes a number, ```s```, and a list, ```v``` and returns the [scalar multiple](https://en.wikipedia.org/wiki/Scalar_multiplication) of ```v``` by ```s```.:" Also a good idea to do something about that weird punctuation at the end of the sentence.

---

_This page is released under the [GNU Free Documentation License](http://openbookproject.net/thinkcs/python/english3e/fdl-1.3.html), any version 1.3 or later produced by the Free Software Foundation. I am grateful to the authors of the original textbook for releasing it that way: Peter Wentworth, Jeffrey Elkner, Allen B. Downey, and Chris Meyers._
