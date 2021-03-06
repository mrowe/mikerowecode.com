---
layout: post
title: Filtering lists
date: "2009-09-24"
url: "/2009/09/filter-in-modern-languages.html"
---

Recently, my friend Gav wrote about [using STL to filter a vector of
values in C++][1] in which he explained a surprising gotcha. I'm sure
he knows what he's talking about, but it struck me how *ugly* this
(presumably idomatic) code was. So I figured I'd see what it would
look like in a few more "modern" languages:

### Ruby

{{< highlight ruby >}}
>> numbers = 1..9
=> 1..9
>> numbers.reject { |n| n.even? }
=> [1, 3, 5, 7, 9]
{{< /highlight >}}

Or, if you skip the separate assignment of the input data:

{{< highlight ruby >}}
>> (1..9).reject { |n| n.even? }
=> [1, 3, 5, 7, 9]
{{< /highlight >}}


### Python

{{< highlight py >}}
>>> numbers = range(1,10)
>>> [n for n in numbers if n % 2]
[1, 3, 5, 7, 9]
{{< /highlight >}}

or

{{< highlight py >}}
>>> [n for n in range(1, 10) if n % 2]
[1, 3, 5, 7, 9]
{{< /highlight >}}

### [Clojure][]

{{< highlight clj >}}
user=> (def numbers (range 1 10))
#'user/numbers
user=> (filter odd? numbers)
(1 3 5 7 9)
{{< /highlight >}}

or

{{< highlight clj >}}
user=> (filter odd? (range 1 10))
(1 3 5 7 9)
{{< /highlight >}}

Yeah, I get that this wasn't the point of the original post--sometimes
you're just stuck with C++. But if you do have the choice, other
languages can be far more expressive for this common kind of list
processing.

If you have examples in other languages (or improvements to my
efforts) [send them in][] and I'll post them here.

_Update_: From [Julian Doherty][]:

### Erlang

{{< highlight erlang >}}
1> Numbers = lists:seq(1,9).
[1,2,3,4,5,6,7,8,9]
2> [X || X <- Numbers, X rem 2 =/= 0].
[1,3,5,7,9]
{{< /highlight >}}

_Update_: From [Ben MacLeod][]:

### C&#35;

{{< highlight csharp >}}
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
{{< /highlight >}}

_Update_: From John Carney:

### PHP
#### 5.2

{{< highlight php >}}
function not_even($x) {
    return $x & 1 ;
}

$numbers = array(1, 2, 3, 4, 5, 6, 7, 8, 9) ;
$numbers = array_filter($numbers, "not_even") ;
{{< /highlight >}}

#### 5.3

{{< highlight php >}}
$numbers = array(1, 2, 3, 4, 5, 6, 7, 8, 9) ;
$numbers = array_filter($numbers, function($x) { return $x & 1 ; }) ;
{{< /highlight >}}


[1]: http://antonym.org/2009/09/stl-filtering.html
[Clojure]: http://clojure.org/
[send them in]: mailto:mowe@mojain.com
[Julian Doherty]: http://www.rawblock.com
[Ben MacLeod]: http://houtschuurtje.blogspot.com/
