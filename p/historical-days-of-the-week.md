---
title: "Historical days of the week"
date: "2019-09-23"
layout: page
---

Today is September 23, 2019, and it is a Monday. September 3, 2001, was a Tuesday. And January 1, 1 CE was a Monday. At least, it was a Monday assuming we're talking about the Gregorian calendar, which we'll assume for the rest of this post. Can we write a function that takes a date and gives the day of the week automatically for any CE date?

One approach is to create a single function that takes the month, day, and year of a given date, makes all the necessary manipulations, and spits out an answer. But that would be a pain to write and a pain to debug, especially for a newbie like myself. And I would be haunted by a nagging suspicion that, somewhere in the code, there's some screw-up I haven't discovered yet, even after poking around quite a bit.

Instead, I'll write it piece by piece, as a series of functions that each pass off complications to lower functions until everything is solved in a series of fairly simple operations. Let's start at the top. To manipulate days of the week in our code, it would help to assign a number to each day of the week. We can use a scheme already suggested in _[How to Think Like a Computer Scientist: Learning with Python 3](index.html)_. We'll say that Sunday's number is 0, Monday is 1, Tuesday is 2 ... and Saturday is 6. So if a given number of days has passed since January 1, 1, we can take that number of days, add 1 to represent that the initial day is Monday, and then take a modulo 7 to find our final weekday. Then we can convert that into a string, like "Tuesday" or "Thursday", and output it.

So we can write the following:

{% highlight ruby %}
def weekday(month, day, year):
    """Gives the day of the week for any date CE, according to the
    Gregorian calendar. Takes month as a string, e.g., "January". Takes
    day and year as integers. Returns a string, such as "Tuesday"."""
    
    # For purposes of calculation, the scheme below 
    # relates each weekday to a number.
    weekdays = {0: "Sunday",
                1: "Monday",
                2: "Tuesday",
                3: "Wednesday",
                4: "Thursday",
                5: "Friday",
                6: "Saturday"
    }

    # Calculates the day of the week for any date, using 
    # the fact that January 1, 1 CE was a Monday, and then
    # adding to that the number of days since that date.
    weeknum = (days_since_jan_1_1(month, day, year) + 1) % 7

    # Uses weekdays dictionary to return the day of the week
    # as a string.
    return weekdays[weeknum]
{% endhighlight %}

Once we ignore some fancy formatting and comments designed for the use of later editors of the same code, there's basically just two lines of code here. But we've passed off most of the hard work of the function to `days_since_jan_1_1(month, day, year)`.

So now we need a function that can take a given date and tell us how many days have passed since January 1, 1. For a given date, like September 12, 2012, we can break this task down into two smaller tasks. First, we find the number of days from January 1, 1 to January 1, 2012. Second, we find the number of days from January 1, 2012 to September 12, 2012. In our function, we can pass each of those two tasks off to other functions. Still, we've made progress by breaking our problem into two easier problems.

{% highlight ruby %}
def days_since_jan_1_1(month, day, year):
    """Takes as a string a month, and as integers day and year, 
    and returns the number of days from January 1, 1 CE, to 
    the date it takes."""
    return days_to_year(year) + days_to_date_in_year(month, day, year)
{% endhighlight %}

So now our to-do list consists of `days_to_year(year)` and `days_to_date_in_year(month, day, year)`. It's a short to-do list, but if we forget something on it, we'll later get a nice error thrown up by the missing function when we try to run our code later. Let's take `days_to_year(year)` first.

To discover the number of days from January 1, 1 to January 1, `year`, we need two things. We first need the number of years that have passed, which is `year - 1`. At 365 days per year, the number of days that have passed is `(year - 1) * 365`. But this ignores the number of leap-years that have passed. We can pass that problem off to another function.

{% highlight ruby %}
def days_to_year(year):
    """Returns the number of days from January 1, 1 CE, to 
    January 1 of a given year CE."""
    return (year -1) * 365 + leaps_to(year)
{% endhighlight %}

So now we can turn to `days_to_date_in_year`. This gives the number of days from January 1 of a given year to some date. For example, it might calculate the days from January 1 to September 3 in 1972. To do this, we need three things. First, the number of days from January 1 to September 1 in a typical (non-leap) year. Then, the number of days from September 1 to September 3 (3-1 = 2). Finally, if the date is March 1 or later, and it's a leap year, we need to add a day to account for February 29th. We can code that up like this:

{% highlight ruby %}
def days_to_date_in_year(month, day, year):
    """For a given date, returns the number of days from January 1
    of the same year to the date."""
    sum = days_to_typical_month(month) + day - 1 
    if sum >= days_to_typical_month("March") and is_leap(year):
        sum += 1
    return sum
{% endhighlight %}

Next, we can write a very simple function which is given a month, like say September, and gives the number of days from January 1 to September (or whatever) 1 in a non-leap year. All we need to do is hand-calculate twelve simple numbers, drop them into a dictionary, and here we are:

{% highlight ruby %}
def days_to_typical_month(month):
    """For a given month, gives the number of days from January
    1 to the first day of the given month, in a typical (non-leap)
    year."""
    day_look_up = {"January": 0,
                   "February": 31,
                   "March": 59,
                   "April": 90,
                   "May": 120,
                   "June": 151,
                   "July": 181,
                   "August": 212,
                   "September": 243,
                   "October": 273,
                   "November": 304,
                   "December": 334
    }
    return day_look_up[month]
{% endhighlight %}

Now all we have left are two little functions dealing with leap-days. The more complicated one is `leaps-to(year)`. This will return the number of leap-years between January 1, 1, and January 1, `year`.

First, let's review the rules for leap-years in the Gregorian calendar. Generally, if a year is divisible by four, it is a leap-year. But if a year is divisible by 100, in which case it is _not_ a leap-year. The final exception is that if a year is divisible by 400, it _is_ a leap-year.

So if we want, for example, the number of leap-years from January 1, 1, to January 1, 2000, we need to work out three things: the number of intervening years divisible by 4, which we'll call `divs4`; the number of years divisible by 100, which we'll calls `divs100`; and the number of years divisible by 400, or `divs400`. Then the total number of intervening leap-years will be `divs4 - divs100 + divs400`. When we calculate one of our "divs" variables, we need to make sure we don't include `year`: in this case, for example, the fact that 2000 is a leap-year is irrelevant, since we're only concerned with days up to January 1, 2001 -- before the leap-day. So the code comes out like this:

{% highlight ruby %}
def leaps_to(year):
    """Returns the number of leap days between January 1, 1 CE
    and January 1 of a given year."""
    divs4 = (year -1) // 4
    divs100 = (year - 1) // 100
    divs400 = (year -1) // 400
    return divs4 - divs100 + divs400
{% endhighlight %}

Now we have just one more function to write: `is_leap`, which will tell us whether a year is a leap year. It's a simple implementation of the Gregorian rules for leap-years:

{% highlight ruby %}
def is_leap(year):
    """Returns True if a given year is a leap year; False otherwise."""
    if year % 400 == 0:
        return True
    elif year % 100 == 0:
        return False
    elif year % 4 == 0:
        return True
    else:
        return False
{% endhighlight %}

Then we just put it all together, and at the end we'll add a little loop that lets the user try out dates.

{% highlight ruby %}
def weekday(month, day, year):
    """Gives the day of the week for any date CE, according to the
    Gregorian calendar. Takes month as a string, e.g., "January". Takes
    day and year as integers. Returns a string, such as "Tuesday"."""
    
    # For purposes of calculation, the scheme below 
    # relates each weekday to a number.
    weekdays = {0: "Sunday",
                1: "Monday",
                2: "Tuesday",
                3: "Wednesday",
                4: "Thursday",
                5: "Friday",
                6: "Saturday"
    }

    # Calculates the day of the week for any date, using 
    # the fact that January 1, 1 CE was a Monday, and then
    # adding to that the number of days since that date.
    weeknum = (days_since_jan_1_1(month, day, year) + 1) % 7

    # Uses weekdays dictionary to return the day of the week
    # as a string.
    return weekdays[weeknum]

def days_since_jan_1_1(month, day, year):
    """Takes as a string a month, and as integers day and year, 
    and returns the number of days from January 1, 1 CE, to 
    the date it takes."""
    return days_to_year(year) + days_to_date_in_year(month, day, year)

def days_to_year(year):
    """Returns the number of days from January 1, 1 CE, to 
    January 1 of a given year CE."""
    return (year -1) * 365 + leaps_to(year)

def days_to_date_in_year(month, day, year):
    """For a given date, returns the number of days from January 1
    of the same year to the date."""
    sum = days_to_typical_month(month) + day - 1 
    if sum >= days_to_typical_month("March") and is_leap(year):
        sum += 1
    return sum

def days_to_typical_month(month):
    """For a given month, gives the number of days from January
    1 to the first day of the given month, in a typical (non-leap)
    year."""
    day_look_up = {"January": 0,
                   "February": 31,
                   "March": 59,
                   "April": 90,
                   "May": 120,
                   "June": 151,
                   "July": 181,
                   "August": 212,
                   "September": 243,
                   "October": 273,
                   "November": 304,
                   "December": 334
    }
    return day_look_up[month]

def leaps_to(year):
    """Returns the number of leap days between January 1, 1 CE
    and January 1 of a given year."""
    divs4 = (year -1) // 4
    divs100 = (year - 1) // 100
    divs400 = (year -1) // 400
    return divs4 - divs100 + divs400

def is_leap(year):
    """Returns True if a given year is a leap year; False otherwise."""
    if year % 400 == 0:
        return True
    elif year % 100 == 0:
        return False
    elif year % 4 == 0:
        return True
    else:
        return False

while True:
    print("If you want to quit the program, just \n"
          "enter 'q' into the prompt that asks for \n"
          "a month.")
    month = input("Give me a month. \n")
    if month == "q":
        break
    day = int(input("Give me a day. \n"))
    year = int(input("Give me a year. \n"))
    print(weekday(month, day, year))
{% endhighlight %}

---

_This page is released under the [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) license._

