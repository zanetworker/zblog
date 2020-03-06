---
title:  "Why Learn a Functional Programming Language"
date:   2012-08-11 15:04:23
categories: [technical]
tags: [Coding, Functional, Haskell]
---


Okay, I have to admit I wasn’t a big fan of functional programming languages, but looking at the benefits that it introduces, it might not be a very bad idea to put it under the microscope for a while.

So, in this blog post I am going to list some of the benefits that are introduced by functional programming language.

- **“Polymorphic Type Systems”** : Functional programing languages usually lets you use functions for many types (belonging to the same class), thus making use of the concepts of generecity   and tragically reducing the software maintenance costs.  For example, (Len:: [Int] -> Int) is a function to get the length of a integer list, using functional programming languages like Haskell, one can write something like this (Len :: (Num a) =>  [a] -> Int)  which allows you to use any numeric type class like (double, Int,… etc).

- **“No Side Effects” :** In functional programming languages, functions have no side effects, this follows from the concept of “referential transparency” which means that evaluating a function multiple times with  same argument will eventually lead to the same output (doesn’t seem like much, or does it 😀 ?). A side effect can be a result of a function changing a variable outside it’s local scope thus causing another function to incorrectly use the wrong value of the intended variable, or it could be a result of writing or reading something as an output in the Console. Side effects are not needly a bad thing, however when care is not taken, they might introduce problems, so pure functional languages gets rid of these effects.

- **“Automatic Storage Management”:** The programmer doesn’t actually have to bother about allocating memory and freeing it, explicit pointer manipulations are done by the language internals.
“Equal Treatment of Functions As data Objects” : Functions are treated as data objects, this means that individual functions can be arguments to another functions. Those functions are called first class data objects.

- **“Lazy Evaluation”:** I chose to put this as last, because it is my favorite. Basically, what lazy evaluation does is it only evaluates parts of the argument which are necessary for the result calculation hence, it is referred to as “Lazy”. This can be demonstrated in Haskell by the following simple function that takes two arguments (three x 1 = 3 ). What this simply shows is that Haskell won’t bother to check what is inside “x” as long as the second argument is “1” the function will return 3.


I hope this helps
