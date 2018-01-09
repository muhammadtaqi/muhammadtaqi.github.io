---
layout: post
title: "Python Notes"
date: 2017-08-11T17:13:31+05:00
categories: python
<!-- published: false -->
---
# 1. Basics

## 1.1 Python Data-Types
![Python Data-Types]({{ site.raw_static_url }}/learning-python/python_data_types.png)

  > It turns out that using _`str(), list(), dict() and tuple()`_ for creating empty sequences isn't as fast as their shorthand counterparts _`('', [], {}, ())`_.

* __str__  --  Bad way `s = 'value'`, Good way `s=str('value')`
* __list__  --  Bad way `l=["Bad", "Way"]`, Good way `l=list("Good", "Way")`
* __dict__  --  Bad way `d={"Bad": "Way"}`, Good way `d=dict("Good": "Way")`
* __tuple__  --  Bad way `t=("Bad", "Way")'`, Good way `t=tuple("Bad", "Way")`
<br />
{% highlight python %}
#!/usr/bin/env python
import timeit

start = timeit.default_timer()
s = 'taqi'
print s
stop = timeit.default_timer()
time = stop-start
print "Time taken for the calculation was {} seconds".format(time) # 0.00690793991089 seconds

start1 = timeit.default_timer()
d = str('taqi')
print d
stop1 = timeit.default_timer()
time1 = stop1-start1
print "Time taken for the calculation was {} seconds".format(time1) # 0.00169682502747 seconds
{% endhighlight %}

## 1.2 Scope in python
 > Scope define where a variable is accessible and what is lifetime of that variable. The reign where a variable can be use that called scope for that variable.

Not all variables are accessible from all parts of our program, and not all variables exist for the same amount of time.

### LEGB rule [Local, Enclosing, Global, Built-in]
When a reference is made inside a function, the name is searched in the local namespace, then in the global namespace and finally in the built-in namespace.

* `L, Local` — Names assigned in any way within a function (def or lambda)), and not declared global in that function.
* `E, Enclosing-function locals` — Name in the local scope of any and all statically enclosing functions (def or lambda), from inner to outer.
* `G, Global (module)` — Names assigned at the top-level of a module file, or by executing a global statement in a def within the file.
* `B, Built-in (Python)` — Names preassigned in the built-in names module : open,range,SyntaxError,...
{% highlight python %}
#!/usr/bin/env python
def function():
    x = 'x is defined in function'
    print x
    def nested_function():
#        x = 'x is defined in nested function'
        print x
    nested_function()

x = 'x is globally defined'

print x

function()
{% endhighlight %}
<!-- ![scope resolution]({{ site.raw_static_url }}/learning-python/scope_resolution.png) -->
<br /><br />
# 2. OOP in Python

  {% highlight python %}
  #!/usr/bin/env python
  class Customer(object):

      ###################### constructor #######################################
      def __init__(self, name, balance=0.0):
          """ `self` is `instance` of the `class` that is being called on. """
          self.name = name
          self.balance = balance

      ###################### regular method ####################################
      def withdraw(self, amount):
          if amount > self.balance:
              raise RuntimeError('Amount greater than available balance.')
          self.balance -= amount
          return self.balance

      def deposit(self, amount):
          self.balance += amount
          return self.balance

      ###################### class method ######################################
      @classmethod
      def from_string(cls, emp_str):
          print('class method is called')
          first, last, pay = emp_str.split('-')
          return cls(first, last, pay)

      ###################### static method #####################################
      @staticmethod
      def make_car_sound():
          print('static method is called')

      ##########################################################################
      ###################### Getter, Setter, Deleter ###########################
      ##########################################################################
      @property
      def balance(self):
          """I'm the 'balance' property."""
          print("getter of balance called")
          return self.balance

      @balance.setter
      def balance(self, value):
          print("setter of balance called")
          self.balance = value

      @balance.deleter
      def balance(self):
          print("deleter of balance called")
          del self.balance

      ####################### magic methods ####################################
      def __str__(self):
          """ used to change behaviour how object is printed. """
          return "{} - {}".format(self.first_name, self.last_name)

      def __repr__(self):
          """ in absence of __str__, this will be used as default to print object """
          return self.name

  ##########################################################################
  # you can also use class methods as alternative to constructor
  new_emp = Employee.from_string('Taqi-Hassan-2256')
  {% endhighlight %}

####  2.1  `class`
  Simply a logical grouping of data and functions. Classes itself don't do anything, these are just blueprints for the date modeling and through these blueprints we create objects as many as we want.

  The class `Customer(object)` line does not create a new customer. we've merely outlined the blueprint to create a Customer object. To use the Customer blueprint to create me a new customers we call the class's __init__ method with the proper number of arguments `jeff = Customer('Jeff Knupp', 1000.0)`

  The jeff object, known as an instance, is the realized version of the Customer class. Before we called Customer(), no Customer object existed. We can, of course, create as many Customer objects as we'd like. There is still, however, only one Customer class, regardless of how many instances of the class we create.

####  2.2  `methods`
  `def` is used to define a function or method. Methods have access to all the data contained on the instance of the object; they can access and modify anything previously set on self.
  1. `Regular method` takes `self` as first argument.

  2. `@staticmethod` don't have access to `self` and should not receive the instance as the first parameter.

  3. `@classmethod` Instead of receiving the `instance`, takes class `cls` as first argument. Another great example is [datetime][datetime] module of python.

  4. `@abstractmethod`

  5. `@property getter, setter, and deleter methods`

  6. _Magic methods_

####  2.3  `__init__`
  * When we call `__init__`, we're in the process of creating an object, so how can there already be a self? Python allows us to extend the self pattern to when objects are constructed as well, even though it doesn't exactly fit.
    {% highlight python %}
    emp1 = Employee('Taqi') is same as calling emp1 = Employee(emp1, 'Taqi')
    # and
    self.name = name  is the same as emp1.name = name
    {% endhighlight %}

####  2.4  `Inheritance - Abstract classes`

* Use the `abc` module to create abstract classes. if we don't then we will be able to create instance of this `Base class` that violates the rule of `abstract classes`.
* Use the `abstractmethod` decorator to declare a method abstract. Declare a `class abstract` depending upon your Python version.

{% highlight python %}
# Python 3.4+
from abc import ABC, abstractmethod
class Base(ABC):
    @abstractmethod
    def foo(self):
        pass

# Python 3.0+
from abc import ABCMeta, abstractmethod
class Base(metaclass=ABCMeta):
    @abstractmethod
    def foo(self):
        pass

# Python 2
from abc import ABCMeta, abstractmethod
class Base:
    __metaclass__ = ABCMeta

    @abstractmethod
    def foo(self):
        pass

class Concrete(Base):
    def __init__(self):
        super.__init__(self)
# Car class inherit Vehicle class
    def foo(self):
        pass
{% endhighlight %}

# In Progress . .  .

[datetime]:      https://github.com/python/cpython/blob/3.6/Lib/datetime.py#L718
