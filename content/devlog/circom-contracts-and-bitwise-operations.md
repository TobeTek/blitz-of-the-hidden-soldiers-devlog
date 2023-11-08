+++
categories = ["BoTHS", "DevLog", "Tobe", "circom", "bitwise-magic"]
date = "2023-11-08"
description = "Circom and the need for Bitwise Operations"
featured = "pic02.jpg"
featuredalt = ""
featuredpath = "date"
linktitle = ""
title = "Circom and the need for Bitwise Operations"
slug = "Circom and the need for Bitwise Operations"
type = "devlog"
+++

Few things make me feel as nerdy as bitwise operations. And I think that's a good thing.

But for some backstory. [Circom](https://docs.circom.io/) is used for implementing ZKPs, and that's what we're using on the BoTHS project. However, Circom comes with some peculiar quirks. It's not a fully-fledged programming language, so some things we take for granted don't really exist.
We've only got two datatypes, three if you're being generous:
 - A. Number: Generally always an integer, [because the EVM doesn't support floating numbers](https://ethereum.stackexchange.com/questions/87234/why-was-support-for-floating-point-numbers-not-natively-added-to-solidity-or-et).
 - B. An array of numbers.
 - C. Finally signals. Which are basically numbers that have constraints. 

 With this humble arsenal, you'd be surprised at [the amazing things we can build](https://github.com/privacy-scaling-explorations/incrementalquintree). 

 Why bother about typing variables, when they're all going to be numbers. Functions like `max`, `min` or even `sum` aren't natively implemented. So you'll generally look at the official library of standard components to see if they have what you're looking for. In my case, that was the `max` function. We need to check that a chess piece can not somehow wander of the board, by having a really large position coordinate. It would be really easy to 'clamp' this value in other programming languages, but here, we have to implement *almost* everything ourselves.

Enter bitwise magic! Let's just cut to the chase. You can find below the circuit I finally came up with to determine the maximum of two numbers using bitwise operations
```js
template Max() {
    signal input a;
    signal input b;
    signal output out;

    var BITSIZE = 64;
 
    // Use some bitwise magic to extract the sign bit
    var z = (a - b);
    var i = ( z >> BITSIZE-1) & 1;
 
    // Find the maximum number
    out <== a - (i * z);
}
```

The real magic happens with the
```js
( z >> BITSIZE-1)
```

which we use to extract the sign bit of the difference between the two numbers.

Now our chess pieces won't fall off the board :)
