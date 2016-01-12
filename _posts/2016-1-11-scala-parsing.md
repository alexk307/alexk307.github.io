---
layout: post
title: Parsing JSON with Scala
---

Lately I've been looking for a better way to parse JSON in Scala. Working with JSON in any staticly typed language is difficult and a pain to code/maintain. There's got to be a better way...

In comes [Argonaut](http://argonaut.io/), a Scala JSON parsing library that enables you to deserialize directly into case classes. Just define your schema, represent it in case classes, and boom you're done. No casting objects, no traversing nested fields, nothing. It's a thing of beauty.

Enough talk, let's get started. Grab argonaut using SBT:

```
"io.argonaut" %% "argonaut" % "6.0.4"
```

Let's define a simple schema that might represent a person

```
{
    'first_name': <String>,
    'last_name': <String>
}
```

To parse this into a case class, all we need is a few lines of Scala

```
import argonaut._
import Argonaut._

// Represent our data as a case class
case class Person(first_name: String, last_name: String)

// Define our translation or codec between the JSON and our case class
implicit def PersonCodecJson: CodecJson[Person] = casecodec2(Person.apply, Person.unapply)("first_name", "last_name")

// Parse our message into a Person object
Parse.decodeOption[Person](message)
```

That was too easy, let's make this more difficult and add a nested JSON structure within our Person schema.

```
{
    'first_name': <String>,
    'last_name': <String>,
    'place_of_birth': {
        'name': <String>,
        'id': <Int>
    }
}
```

all we have to do is make sure we write our new case class, define the codec, and add it to our existing `Person` case class

```
import argonaut._
import Argonaut._

abstract class Serializable
case class Person(first_name: String, last_name: String, place_of_birth: Location) extends Serializable
case class Location(name: String, id: Int) extends Serializable

implicit def LocationCodecJson: CodecJson[Location] = casecodec2(Location.apply, Location.unapply)("name", "id")
implicit def PersonCodecJson: CodecJson[Person] = casecodec3(Person.apply, Person.unapply)("first_name", "last_name", "location")

Parse.decodeOption[Person](message)
```

Two things to note, first the change in numbers following the `casecodec` method. The pattern is `casecodec<n>` where n is the number of arguments to apply to the case class. Just be sure to increment this number when adding a new parameter to your case class. Second, we made both of our classes implement an abstract class we defined as `Serializable` which is necessary when you want to define nested case classes like this.

Let's add a new parameter to our `Person` object called `middle_name`. Some people don't have middle names or choose not to use them

```
abstract class Serializable
case class Person(first_name: String, middle_name: Option[String], last_name: String, place_of_birth: Location) extends Serializable

implicit def PersonCodecJson: CodecJson[Person] = casecodec4(Person.apply, Person.unapply)("first_name", "middle_name", "last_name", "location")
```

The way to represent an optional parameter is to use an `Option` (duh). Just wrap your parameter in an `Option` in your case class and Argonaut will take care of the rest for you.

That's it. Define as many case classes as your heart desires, and nest them as deep as you'd like. Focus your time on processing your content rather than parsing it, enjoy!