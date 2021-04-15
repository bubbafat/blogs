---
author: robert
comments: true
date: 2012-07-20 19:33:48+00:00
layout: post
link: https://roberthorvick.com/2012/07/20/project-euler-problem-33-explained/
slug: project-euler-problem-33-explained
title: Project Euler Problem 33 Explained
wordpress_id: 134
categories:
- Programming
---

I haven’t been blogging about each answer but I wanted to say something about problem 33 because the wording of the problem is very confusing.

 

Here’s what they wrote with some comments about what they mean.

 

**The fraction 49/98 is a curious fraction, as an inexperienced mathematician in attempting to simplify it may incorrectly believe that49/98 = 4/8, which is correct, is obtained by cancelling the 9s.**

 

The point here is that a curious fraction is one that has the following properties:

 

  
  1. The numerator and denominator have a common digit (in this case both contain a 9)
   
  2. If that common digit is removed the resulting fraction is identical
   
  3. That #2 is true is (in this case) coincidence and not a pattern that can be repeated (i.e., 48/74 != 8/7)
 

**We shall consider fractions like, 30/50 = 3/5, to be trivial examples.**

 

  
  1. This example is similar to 49/98
        
    1. 0 is the common number
     
    2. When 0 is removed the fraction remains the same
      
  2. They call this a trivial example of a curious fraction.  Basically they mean that using mod 10 makes this trivial
 

**There are exactly four non-trivial examples of this type of fraction, less than one in value, and containing two digits in the numerator and denominator.**

 

You need to find these four fractions.  They gave you one of them above (49/98).  Notice the restrictions they applied.

 

  
  1. The numerator and denominator will have two digits (10...99)
   
  2. The fraction will be less than one (numerator < denominator)
   
  3. They are non-trivial (i.e., they are not mod 10)
 

**If the product of these four fractions is given in its lowest common terms, find the value of the denominator.**

 

Ultimately you will end up with four fractions – when you have them, multiply them all together and the answer is the denominator in lowest common terms.  

 

Once I understood the problem the solution was trivial.  Basically it was

 

  
  1. For each potential fraction (10..99 / 10..99 )
        
    1. If the num or denom are mod 10, skip it
     
    2. if the num and denom share a common digit
            
      1. divide num and denom and store the result in ‘expected’
       
      2. remove the common digit from num and denom (generating num’ and denom’)
       
      3. divide num’ and denom’ and compare the result to ‘expected’
       
      4. if the results match then I have found one of the four answers
          
    3. next
      
  2. Multiply the four fractions
   
  3. Report the denominator in lowest common terms to the website
 

If you’ve done every problem to this point then you should have no trouble doing this – in fact I had to write almost no new code.  The euler.py module I’ve been adding every time I reuse something had pretty much everything I needed.
