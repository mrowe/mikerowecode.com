---
layout: post
title: Filtering lists 
---

Recently, my friend Gav wrote about [using STL to filter a vector of
values in C++][1] in which he explained a surprising gotcha. I'm sure
he knows what he's talking about, but it struck me how *ugly* this
(presumably idomatic) code was. So I figured I'd see what it would
look like in a few more "modern" languages:

### Ruby

    >> numbers = 1..9
    => 1..9
    >> numbers.reject { |n| n.even? }
    => [1, 3, 5, 7, 9]

Or, if you skip the separate assignment of the input data:

    >> (1..9).reject { |n| n.even? }
    => [1, 3, 5, 7, 9]


### Python

    >>> numbers = range(1,10)
    >>> [n for n in numbers if n % 2]
    [1, 3, 5, 7, 9]

or

    >>> [n for n in range(1, 10) if n % 2]
    [1, 3, 5, 7, 9]

### [Clojure][]

    user=> (def numbers (range 1 10))
    #'user/numbers
    user=> (filter odd? numbers)
    (1 3 5 7 9)

or

    user=> (filter odd? (range 1 10))
    (1 3 5 7 9)

Yeah, I get that this wasn't the point of the original post--sometimes
you're just stuck with C++. But if you do have the choice, other
languages can be far more expressive for this common kind of list
processing.

If you have examples in other languages (or improvements to my
efforts) [send them in][] and I'll post them here.

_Update_: From [Julian Doherty][]:

### Erlang

    1> Numbers = lists:seq(1,9).
    [1,2,3,4,5,6,7,8,9]
    2> [X || X <- Numbers, X rem 2 =/= 0].
    [1,3,5,7,9]

_Update_: From [Ben MacLeod][]:

### C&#35;

    using System;
    using System.Linq;

    // ...

        var numbers = Enumerable.Range(1, 10).Where(n => n % 2 != 0);
        // or, equivalently:
        //var numbers = (from n in Enumerable.Range(1, 10) where n % 2 != 0 select n);
        foreach(var number in numbers) {
            Console.WriteLine(number);
        }

    // ...

_Update_: From John Carney:

### PHP
#### 5.2

    function not_even($x) {
        return $x & 1 ;
    }

    $numbers = array(1, 2, 3, 4, 5, 6, 7, 8, 9) ;
    $numbers = array_filter($numbers, "not_even") ;

#### 5.3

    $numbers = array(1, 2, 3, 4, 5, 6, 7, 8, 9) ;
    $numbers = array_filter($numbers, function($x) { return $x & 1 ; }) ;


[1]: http://antonym.org/2009/09/stl-filtering.html
[Clojure]: http://clojure.org/
[send them in]: mailto:mowe@mojain.com
[Julian Doherty]: http://www.rawblock.com
[Ben MacLeod]: http://houtschuurtje.blogspot.com/
