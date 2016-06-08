+++
date = "2016-06-08T01:20:19-03:00"
draft = false
title = "How floats can screw your code"

+++

At [PDVend](https://www.pdvend.com.br/) I handle monetary operations everytime and everywhere at our codebase, from our Ruby on Rails API to our client apps. Since money isn't just integer at all, we have a lot of trouble with this. I'll show you some implications of using floats (or doubles) in your code, some situations that you actually can use it and alternatives to.

#### Floats are evil
Let's start trying out Ruby:

```ruby
a = 0.1
b = 0.2
puts "a + b = #{a + b}"
```

You probably expect this to output something like `a + b = 0.3`. Try to run and you'll see that it's `a + b = 0.30000000000000004`.

What just happened here? To answer this, we must remember that floats are binary representations of decimal numbers. Not always binary can represent them or it's operations with exact accuracy. Memory is limited, you cannot handle them

But what about real world examples?

In 1991, an missile was launched in Saudi Arabia against an american detachment. Another missile called Patriot was going to intercept it. Since Patriot missile battery was on for about 100 hours, it let the timestamps, which where stored as floats, to slowly introduce some diferences from the real timestamp to the computed timestamp.

If you remember your physics classes, you can imagine that in order to intercept, Patriot would have to calculate the other missile speed and distance and adjust it direction to the other missile. Speed is measured in distance by time and, since  interceptor missile has some mismatches in time, I think it's not going to work.

Back to our case, Patriot had about a 1/3 second of difference in it's timestamp. It means aproximately 600 meters due to it's speed. Float killed 23 soldiers that day.

You can read about the case in the U.S. Government Accountability Office [Report](http://www.gao.gov/products/IMTEC-92-26).

#### Not *that* evil... When to use? How to use?
Float inaccuracy can be an serious problem in some cases. Is it in all cases?

Most of programmers only discover about float problems in the day which them have some trouble with it. It happens because, for most cases, some little imprecisions are tolerated. In some cases floats are transformed to integers.

This datatype can be used when you can handle small imprecisions, like when there's no problem if your character's HP bar is 0.000000282px to the right or 0.000000013231px emptier than it should be.

When using this datatypes, keep attention to possible errors looking to this points:

- Sums or subtractions ("Unsafe" operations, due to floating point math)
- Operations in iterations
- Code stability (wheter it tent do reduce or magnify errors if floats)

#### Alternatives to Float
Most languages have workarounds to floats. Here goes some of them:

- C#: [decimal](https://msdn.microsoft.com/en-us/library/364x0z75.aspx?f=255&MSPPError=-2147217396)
- Java: [java.math.BigDecimal](https://docs.oracle.com/javase/7/docs/api/java/math/BigDecimal.html)
- Javascript: [bignumber.js](https://github.com/MikeMcl/bignumber.js)
- PHP: [BC Module](http://php.net/manual/en/ref.bc.php)
- Ruby: [BigDecimal](http://ruby-doc.org/stdlib-2.1.2/libdoc/bigdecimal/rdoc/BigDecimal.html)

Another strategy is to simplify your usage of floats. At PDVend, one of the solutions that emerged when we had to resolve float problems with money was to treat all of it as cents, the smallest money unit available.

Examine what makes senses to your business.

#### Conclusion
Float is evil, floats can kill, but you can give them a try. You should only keep an eye on your needs and restrictions. Think about what would happen if you have an 0.1 error or an 0.0000001 error; does it matter?

Even when you don't use floats, note that your code can implicit "create" then. PHP, for example, treat some strings as floats.

You should observe your language nuances, your code stability and your needs for precision when working with floats.
