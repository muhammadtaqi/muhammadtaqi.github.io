---
layout: post
title: "Programming Paradigms"
date: 2017-08-15T11:44:15+05:00
categories: programming
keywords: python, programming paradigms
comments: true
---
Programming paradigms are simply the approach/modeling of your algorithms and data structures.

* Main programming paradigms
  * _**Functional:**_ Programming with function calls that avoid any global state.
  * _**Imperative:**_ Programming with an explicit sequence of commands that update state.
  * _**Object-oriented:**_ Programming by defining objects that send messages to each other. Objects have their own internal (encapsulated) state and public interfaces. Object orientation can be
  * _**Procedural:**_ Imperative programming with procedure calls.

* Other programming paradigms

  * _**Declarative:**_ Programming by specifying the result you want, not how to get it.
  * _**Structured:**_ Programming with clean, goto-free, nested control structures.
  * _**Class-based:**_ Objects get state and behavior based on membership in a class.
  * _**Prototype-based:**_ Objects get behavior from a prototype object.
  * _**Event-Driven:**_ Programming with emitters and listeners of asynchronous actions.
  * _**Flow-Driven:**_ Programming processes communicating with each other over predefined channels.
  * _**Logic (Rule-based):**_ Programming by specifying a set of facts and rules. An engine infers the answers to questions.
  * _**Constraint:**_ Programming by specifying a set of constraints. An engine finds the values that meet the constraints.
  * _**Aspect-Oriented:**_ Programming cross-cutting concerns applied transparently.
  * _**Reflective:**_ Programming by manipulating the program elements themselves.
  * _**Array:**_ Programming with powerful array operators that usually make loops unnecessary.

Paradigms are not meant to be mutually exclusive; a single program can feature multiple paradigms!.
We will use `python` to explore and practice because
> _**Python**_ is multi-paradigm language: it supports _**imperative, OO and functional**_ programming paradigms.

## Problem: Calculate sum of 10 numbers.

Writing in Progress ...


<!-- ## 1. _imperative approach **(HOW)**_
You know, _**imperative programming**_ is like how you do something, and _**declarative programming**_ is more like what you do, or something.

{% highlight python %}
numbers = [1, 2, 3, 4]
total = 0
for number in numbers:
  total += number
print(total)
{% endhighlight %}
In the code above we are telling the computer how to do the sum. We create the total variable then iterate over the list. At each lap we increment the total variable with one of the values in the list. And then we print the total.

## 2. _functional approach_
The functional coding style treats everything like a math equation. Algorithms tend also to be defined in terms of `recursion` and `composition` rather than loops and iteration.
{% highlight python %}
import functools
MyList = [1, 2, 3, 4, 5]
def AddIt(X, Y):
    return (X + Y)
Sum = functools.reduce(AddIt, MyList)
print(Sum)
{% endhighlight %}


### 2.1 Mutable vs. immutable
lets use the example of calculating the total sum of values in a list. This time using an **imperative style function**:
{% highlight python %}
def sum_lst(lst):
  total = 0
  for number in lst:
      total += number
  return total
{% endhighlight %}
As we can see, our function has only one variable called total that is updated on every iteration. This is clearly a case of a mutable variable. Now lets try a **functional approach**:
{% highlight python %}
def sum_lst(lst):
  if not lst:
      return 0
  else:
      return lst[0] + sum_lst(lst[1:]) # values are returned but no variable is changed
{% endhighlight %}
This time we are not updating any variables and are using recursion, which is the functional programming way of doing loops.

### 2.2 First-class functions, Higher order functions and Lambda
* A programming language is said to have **first-class functions** when it supports passing functions as parameters, returning them or assigning them to variables.
* And **higher order functions** are functions that receive one or more function as arguments and return a function.

Python supports **first-class functions**:
{% highlight python %}
def call_my_name(name):
  print(name)

call_my_name_again = call_my_name
call_my_name('Anderson') # prints Anderson
call_my_name_again('Anderson') # also prints Anderson
{% endhighlight %}
And also **higher order functions**:
{% highlight python %}
def convert_to(to_what, number):
 return to_what(number)

convert_to(float, 20) # convert 20 to float
convert_to(str, 10) # convert 10 to str
{% endhighlight %}
Lets see another example: `map` is very popular higher order function that you've probably heard of before. It traverses an iterable and applies a function to it:
{% highlight python %}
def double(number):
  return number * 2

print(list(map(double, [1, 2, 3, 4])))
{% endhighlight %}
Here is an imperative version of what is happening:
{% highlight python %}
numbers = [1, 2, 3, 4]
new_numbers = []
for number in numbers:
  new_numbers.append(number * 2)
print(new_numbers)
{% endhighlight %}
_**lambdas:**_ Lambdas are anonymous functions that are usually passed as parameters to other functions. Here is a Python example:
{% highlight python %}
add_one = lambda number: number + 1
print(add_one(10)) # prints 11
{% endhighlight %}
Or using map:
{% highlight python %}
print(list(map(lambda number: number + 1, [1, 2, 3, 4])))
{% endhighlight %}
Lambdas in Python can have only one line of code and automatically return the computed value, i.e, they are an expression, and so they don’t need the return statement.

### 2.3 List Compressions
Most times we use loops to iterate over lists and execute actions in its elements. List comprehensions are a more declarative way of doing that. Lets see an example. Here is a regular for iteration:
{% highlight python %}
dogs = ["Todd", "Tom", "Bob"]
big_dogs = []
for dog in dogs:
  big_dogs.append("Big {}".format(dog))
print(big_dogs)
{% endhighlight %}
Now lets do the same but now using list comprehensions:
{% highlight python %}
dogs = ["Todd", "Tom", "Bob"]
big_dogs = ["Big {}".format(dog) for dog in dogs]
print(big_dogs)
{% endhighlight %}

Sum-up

We introduced some functional programming concepts applying them using Python. Hopefully you can now identify functional constructions when you see one and also be able to use them when possible. Python has tons of other functional tools besides the ones showed in this post, start by looking for functools module.

## 3. _object-oriented approach_

The object-oriented coding style is all about increasing the ability of applications to reuse code and making code easier to understand. Using object-orientation features like `encapsulation` that object-orientation provides allows developers to treat code as a black box and `inheritance` makes it easier to expand the functionality of existing code.
In this case, CreateSum is an instance of ChangeList. The inner workings of ChangeList don’t matter to the person using it. All that really matters is that you can create an instance using a list and then call the DoAdd() method to output the sum of the list elements. Because the inner workings are hidden, the overall application can be easier to understand.


{% highlight python %}
class ChangeList:
    def __init__(self, MyList):
        if type(MyList) is list:
            self.MyList = MyList
        else:
            self.MyList =[]
    def DoAdd(self):
        self.Sum = sum(self.MyList)
CreateSum = ChangeList([1, 2, 3, 4, 5])
CreateSum.DoAdd()
print(CreateSum.Sum)
{% endhighlight %}

## 4. _procedural approach_ -->
