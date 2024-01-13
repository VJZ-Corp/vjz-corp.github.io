# Understanding the Principia Mathematica Proof of 1+1=2 [PART 1]
## Introduction
A famous image that has been circling online is the following image:

![Principia Mathematica - Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/Principia_Mathematica_54-43.png/500px-Principia_Mathematica_54-43.png)

(Source: Wikipedia)

You may or may not have seen it, but to the average person, this seems absurd! Why would we need such complex notation for such a simple, universal, fact? 

Principia Mathematica is a book authored by Alfred North Whitehead and Bertrand Russell. The ultimate goal for the work was to create a book that had a proof for every statement in mathematics, except for a few, commonly, accepted statements, known as axioms.

The above proposition is only creating the logic behind 1+1=2, not the statement itself. 1+1=2 is actually proved in:

![The Universe of Discourse : 1+1=2](https://pic.blog.plover.com/math/PM/1+1=2-cardinals.png)

(Source: pic.blog.plover.com)
## Interpreting the Propositions
To understand 54.43, we need to understand sets. A set is just a box with things inside. In real life, you could have a box with toys, or a box with a couch, etc. It is important to note that a set cannot have duplicates, so you can't have two of the exact same thing in a box. In mathematics, you can have all sorts of mathematical objects in a set. The cardinality of a set is the amount of things you have in a set. If you have a set of 4 toy cars, then the cardinality of the set is 4. You can also combine two sets. There are two different ways to do this, either by taking what the sets have in common, or by shoving everything together. Either way, this creates a new set. The first way described is called the intersection. Think of a Venn Diagram. The intersection in the middle is one operator, while everything within a circle is another operator, which is called the union.

![Union/Intersection of Java sets](https://raw.githubusercontent.com/mahozad/mahozad/master/stackoverflow/java-union-intersection.svg)

(Source: raw.githubusercontent.com/mahozad/)

Sometimes, there is nothing in common between two boxes, in which case you get a box with nothing in it.

Theorem 54.43 starts with the end proposition that we are trying to prove. 

![The Universe of Discourse : A modern translation of the 1+1=2 lemma](https://pic.blog.plover.com/math/PM-translation/1+1=2-translation.png)

(Source: pic.blog.plover.com)

This image has the proposition in modern notation. Still not very easy to read, right? The final proposition states that 

"If you have two sets, A, B, both with cardinality 1, then those sets are disjoint if and only if the cardinality of their union is 2."

Let's break this statement down.

"If you have two sets, A, B,"

Here, we are simply declaring that we have two sets, named A and B.

"both with cardinality 1,"

This states that A and B both have one thing inside of their boxes. We don't know what, we just know that they have something.

"then those sets are disjoint"

Two sets are disjoint if and only if they have nothing in common, in other words, if their intersection is nothing.

"if and only if"

This is different from a traditional "if, then". In "if p then q", q can have a variety of reasons. Therefore, q does not imply p. However, in "p if and only if q", the only reason for q is p, therefore they imply each other (in other words, if one is true then the other is true).

"the cardinality of their union is 2"

This states that if we combine A and B, we will get another set that has two things within it.

In other words, if I have a set with one thing and add it to another set with another thing, I get a set with two things. (1+1=2)

[To Be Continued]
