---
layout: post
title: On Python's namedtuples
---

I started reading <a href="http://www.amazon.com/Fluent-Python-Luciano-Ramalho/dp/1491946008" target="_blank">Fluent Python</a> by Luciano Ramalho last night and even though I code professionally in Python, I've already learned some new things. The book itself so far has been great; very informative and also very easy to read due to its short and concise writing. I would highly recommend picking this up if you have any interest in getting a deeper understanding on what's happening when you fire up the python interpreter.

Within the first few pages, Ramalho introduces a concept called <code>namedtuple</code>. Found within the <code>collections</code> library, <code>namedtuple</code> allows you to create instances of classes that you can define in just one line.

```
from collections import namedtuple
Person = namedtuple('Person', ['name', 'gender', 'date_of_birth'])
```

I just created a class called 'Person' and gave it 3 attributes, but I could have named it anything or gave it any number of attributes. This may seem trivial and useless but consider the following equivalent python code:

```
class Person:
    def __init__(self, name, gender, date_of_birth):
        self.name = name
        self.gender = gender
        self.date_of_birth = date_of_birth
```

Minus the import statement, I've condensed 5 lines into 1, and also made it more readable at the same time.

The reason I found this so interesting was because of it's resemblance to <a href="http://www.scala-lang.org/old/node/107" target="_blank">Scala's Case Classes</a>. I first came across these while working on a project using <a href="http://akka.io/" target="_blank">Akka</a>, which is Scala's de facto distributed processing library. You can't even get past the <a href="http://www.typesafe.com/activator/template/hello-akka" target="_blank">hello world example</a> without using case classes. Instead of passing around your data in JSON or something else, you would pass around instances of your case class. The beauty of case classes is that they're easy to handle programmatically and are also immutable.

These `namedtuples` are also immutable, and provide an alternative to organizing your data with `dict`s in python. They provide a much easier way of keeping your data structures and classes clean and easy to maintain by encapsulating your data in an object in just a few lines. Modeling data with multiple classes becomes much easier and more intuitive when combining namedtuples with regular classes. Here's a simple `Book` class that uses a `Page` `namedtuple` to encapsulate the pages in a book.

```
Page = namedtuple('Page', ['number', 'contents'])
class Book:
    def __init__(self, pages):
        self.pages = pages

    def __len__(self):
        return self.pages[-1].number

    def get_page(number):
        return self.pages[number].contents
```

It also comes with some helpful debugging benefits baked right in:

```
>>> p = Person('Alex', 'Male', '1/1/1999')
 Person('Alex', 'Male', '1/1/1999')
```

The `__str__` method automatically is created and returns a string representation of the instance of the object. This means that you could just plug this into a log statement and get a meaningful message.

```
>>> Log.debug('Received information about person: %s' % p)
 'Received information about person: Person('Alex', 'Male', '1/1/1999')'
```

The benefit to this paradigm is that you can write clear and concise code without sacrificing organization. I haven't seen any Akka-like uses for these but I would be interested in hearing about them.
More reading <a href="https://docs.python.org/2/library/collections.html#collections.namedtuple">here</a>!
