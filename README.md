<!--
  ~ Copyright (c) 2020 Goldman Sachs and others.
  ~ All rights reserved. This program and the accompanying materials
  ~ are made available under the terms of the Eclipse Public License v1.0
  ~ and Eclipse Distribution License v. 1.0 which accompany this distribution.
  ~ The Eclipse Public License is available at http://www.eclipse.org/legal/epl-v10.html
  ~ and the Eclipse Distribution License is available at
  ~ http://www.eclipse.org/org/documents/edl-v10.php.
  -->
[![][travis img]][travis]
[![][maven img]][maven]
[![][release img]][release]
[![][license-epl img]][license-epl]
[![][license-edl img]][license-edl]

[![][actions unit-tests img]][actions unit-tests]
[![][actions acceptance-tests img]][actions acceptance-tests]
[![][actions performance-tests img]][actions performance-tests]
[![][actions checkstyle img]][actions checkstyle]
[![][actions findbugs img]][actions findbugs]
[![][actions javadoc img]][actions javadoc]

<a href="https://www.eclipse.org/collections/"><img src="https://github.com/eclipse/eclipse-collections/blob/master/artwork/eclipse-collections-logo.png" height="50%" width="50%"></a>

#### [English](https://www.eclipse.org/collections/) | [中文](https://www.eclipse.org/collections/cn/index.html) | [Deutsch](https://www.eclipse.org/collections/de/index.html) | [Español](https://www.eclipse.org/collections/es/index.html) | [Ελληνικά](https://www.eclipse.org/collections/el/index.html) | [Français](https://www.eclipse.org/collections/fr/index.html) | [日本語](https://www.eclipse.org/collections/ja/index.html) | [Português-Brasil](https://www.eclipse.org/collections/pt-br/index.html) | [Русский](https://www.eclipse.org/collections/ru/index.html)
Eclipse Collections is a comprehensive collections library for Java. The library enables productivity and performance by delivering an expressive and efficient set of APIs and types. The iteration protocol was inspired by the Smalltalk collection framework, and the collections are compatible with the Java Collection Framework types.


## Why Eclipse Collections?

* Productivity
    * Rich, functional, and fluent APIs with great symmetry 
    * `List`, `Set`, `Bag`, `Stack`, `Map`, `Multimap`, `BiMap`, `Interval` Types 
    * Readable, Mutable, and Immutable Types
    * Mutable and Immutable Collection Factories
    * Adapters and Utility classes for JCF Types
* Performance
    * Memory Efficient Containers 
    * Optimized Eager, Lazy and Parallel APIs
    * Primitive Collections for all primitive types 

## Learn Eclipse Collections

* [Eclipse Collections Katas](https://github.com/eclipse/eclipse-collections-kata), a fun way to help you learn idiomatic Eclipse Collections usage.
    * Start Here - [Pet Kata](http://eclipse.github.io/eclipse-collections-kata/pet-kata/#/) 
    * Continue Here - [Company Kata](http://eclipse.github.io/eclipse-collections-kata/company-kata/#/)
* [Eclipse Collections Reference Guide](https://github.com/eclipse/eclipse-collections/blob/master/docs/guide.md) and [Javadoc](https://www.eclipse.org/collections/javadoc/10.2.0/overview-summary.html)
* [Articles](https://github.com/eclipse/eclipse-collections/wiki/Articles) and [Blogs](https://medium.com/tag/eclipse-collections/latest)

## Acquiring Eclipse Collections

### Maven
```xml
<dependency>
  <groupId>org.eclipse.collections</groupId>
  <artifactId>eclipse-collections-api</artifactId>
  <version>10.2.0</version>
</dependency>

<dependency>
  <groupId>org.eclipse.collections</groupId>
  <artifactId>eclipse-collections</artifactId>
  <version>10.2.0</version>
</dependency>
```

### Gradle

```groovy
implementation 'org.eclipse.collections:eclipse-collections-api:10.2.0'
implementation 'org.eclipse.collections:eclipse-collections:10.2.0'
```

### OSGi Bundle
Eclipse software repository location: http://download.eclipse.org/collections/10.2.0/repository


## Some Quick Examples

Eclipse Collections puts iteration methods directly on the container types. Here's several code examples that demonstrate the simple and flexible style of programming with Eclipse Collections.

First, we will define a simple class named `Person` with a first and last name, getters and a constructor.

```java
public class Person
{
    private final String firstName, lastName;
    ...
    public boolean lastNameEquals(String name)
    {
        return name.equals(this.lastName);
    }
}
```

#### `Collect` (aka `map`, `transform`)
First we will create a `MutableList` with three instances of the `Person` class. 
```java
MutableList<Person> people = Lists.mutable.with(
        new Person("Sally", "Smith"),
        new Person("Ted", "Watson"),
        new Person("Mary", "Williams"));
```
Then we will `collect` their last names into a new `MutableList`, and finally output the names to a comma delimited String using `makeString`. 
```java
MutableList<String> lastNames = people.collect(person -> person.getLastName());
Assert.assertEquals("Smith, Watson, Williams", lastNames.makeString());
```
The lambda in the example above can also be replaced with a method reference.

```java
MutableList<String> lastNames = people.collect(Person::getLastName);
```
Eclipse Collections has support for both [Mutable](http://www.eclipse.org/collections/javadoc/10.2.0/org/eclipse/collections/api/collection/MutableCollection.html) and [Immutable](http://www.eclipse.org/collections/javadoc/10.2.0/org/eclipse/collections/api/collection/ImmutableCollection.html) collections, and the return types of methods are covariant.  Here we use the same [Lists](https://www.eclipse.org/collections/javadoc/10.2.0/org/eclipse/collections/impl/factory/Lists.html) factory to create an `ImmutableList`.
```java
ImmutableList<Person> people = Lists.immutable.with(
        new Person("Sally", "Smith"),
        new Person("Ted", "Watson"),
        new Person("Mary", "Williams"));
```
While the `collect` method on a `MutableList` returned a `MutableList`, the `collect` method on an `ImmutableList` will return an `ImmutableList`.  
```java
ImmutableList<String> lastNames = people.collect(Person::getLastName);
Assert.assertEquals("Smith, Watson, Williams", lastNames.makeString());
```

Eclipse Collections has a [lazy API](http://www.eclipse.org/collections/javadoc/10.2.0/org/eclipse/collections/api/LazyIterable.html) as well, which is available by calling the method `asLazy`.  The method `collect` will now return a `LazyIterable`.  The `LazyIterable` that is returned does not evaluate anything until the call to a terminal method is made.  In this case, the call to `makeString` will force the `LazyIterable` to collect the last names.

```java
LazyIterable<String> lastNames = people.asLazy().collect(Person::getLastName);
Assert.assertEquals("Smith, Watson, Williams", lastNames.makeString());
```
#### `Select` / `Reject` (aka `filter` / `!filter`)
We can find all of the people with the last name "Smith" using the method named `select`.
```java
MutableList<Person> people = Lists.mutable.with(
        new Person("Sally", "Smith"),
        new Person("Ted", "Watson"),
        new Person("Mary", "Williams"));

MutableList<Person> smiths = people.select(person -> person.lastNameEquals("Smith"));
Assert.assertEquals("Smith", smiths.collect(Person::getLastName).makeString());
```
If we want to use a method reference, we can use the method `selectWith`.
```java
MutableList<Person> smiths = people.selectWith(Person::lastNameEquals, "Smith");
Assert.assertEquals("Smith", smiths.collect(Person::getLastName).makeString());
```
We can find all the people who do not have a last name of "Smith" using the method named `reject`.
```java
MutableList<Person> notSmiths = people.reject(person -> person.lastNameEquals("Smith"));
Assert.assertEquals("Watson, Williams", notSmiths.collect(Person::getLastName).makeString());
```
If we want to use a method reference, we can use the method `rejectWith`.
```java
MutableList<Person> notSmiths = people.rejectWith(Person::lastNameEquals, "Smith");
Assert.assertEquals("Watson, Williams", notSmiths.collect(Person::getLastName).makeString());
```

#### `Any` / `All` / `None`
We can test whether any, all or none of the elements of a collection satisfy a given condition.
```java
// Any
Assert.assertTrue(people.anySatisfy(person -> person.lastNameEquals("Smith"));
Assert.assertTrue(people.anySatisfyWith(Person::lastNameEquals, "Smith"));

// All
Assert.assertFalse(people.allSatisfy(person -> person.lastNameEquals("Smith"));
Assert.assertFalse(people.allSatisfyWith(Person::lastNameEquals, "Smith"));

// None
Assert.assertFalse(people.noneSatisfy(person -> person.lastNameEquals("Smith"));
Assert.assertFalse(people.noneSatisfyWith(Person::lastNameEquals, "Smith"));
```


## How to Contribute

We welcome contributions! We accept contributions via pull requests here in GitHub. Please see [How To Contribute](CONTRIBUTING.md) to get started.


## Additional information

* Project Website: http://www.eclipse.org/collections
* Eclipse PMI: https://projects.eclipse.org/projects/technology.collections
* StackOverflow: http://stackoverflow.com/questions/tagged/eclipse-collections
* Mailing lists: https://dev.eclipse.org/mailman/listinfo/collections-dev
* Forum: https://www.eclipse.org/forums/index.php?t=thread&frm_id=329

[actions acceptance-tests]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22Acceptance+Tests%22
[actions acceptance-tests img]:https://github.com/eclipse/eclipse-collections/workflows/Acceptance%20Tests/badge.svg?branch=master

[actions unit-tests]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22Unit+tests%22
[actions unit-tests img]:https://github.com/eclipse/eclipse-collections/workflows/Unit%20tests/badge.svg?branch=master

[actions performance-tests]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22Performance+Tests%22
[actions performance-tests img]:https://github.com/eclipse/eclipse-collections/workflows/Performance%20Tests/badge.svg?branch=master

[actions checkstyle]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22Checkstyle%22
[actions checkstyle img]:https://github.com/eclipse/eclipse-collections/workflows/Checkstyle/badge.svg?branch=master

[actions findbugs]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22Findbugs%22
[actions findbugs img]:https://github.com/eclipse/eclipse-collections/workflows/Findbugs/badge.svg?branch=master

[actions javadoc]:https://github.com/eclipse/eclipse-collections/actions?query=workflow%3A%22JavaDoc%22
[actions javadoc img]:https://github.com/eclipse/eclipse-collections/workflows/JavaDoc/badge.svg?branch=master

[travis]:https://travis-ci.org/eclipse/eclipse-collections
[travis img]:https://travis-ci.org/eclipse/eclipse-collections.svg?branch=master

[maven]:http://search.maven.org/#search|gav|1|g:"org.eclipse.collections"%20AND%20a:"eclipse-collections"
[maven img]:https://maven-badges.herokuapp.com/maven-central/org.eclipse.collections/eclipse-collections/badge.svg

[release]:https://github.com/eclipse/eclipse-collections/releases
[release img]:https://img.shields.io/github/release/eclipse/eclipse-collections.svg

[license-epl]:LICENSE-EPL-1.0.txt
[license-epl img]:https://img.shields.io/badge/License-EPL-blue.svg

[license-edl]:LICENSE-EDL-1.0.txt
[license-edl img]:https://img.shields.io/badge/License-EDL-blue.svg

[sonarqube]:https://sonarqube.com/dashboard?id=org.eclipse.collections%3Aeclipse-collections-parent
[sonarqube img]:https://sonarqube.com/api/badges/gate?key=org.eclipse.collections:eclipse-collections-parent

