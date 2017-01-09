# Caching DOM Queries
This started off with a function that would store the result of the DOM query using `document.querySelectorAll` in an object.
After a few tests and "what ifs", I came to learn that returning a function with a "baked-in" DOM query using `document.querySelectorAll` is quite performant.

At this point I'm assuming browsers are heavily optimized to re-use functions with the same signatures.

## Using a browser's native optimizations to enhance DOM queries performance with .querySelectorAll
To make the cache tests a little fair each test runs a single query prior to the test to "warm things up". The results?

A single query with a function return is faster in Chrome and Edge, Firefox seems fine on its own.

Repeated queries is... a repeat (hah) of the previous round, both Chrome and Edge benefit while Firefox still does things better invoking .querySelectorAll directly.

And the final test was a brute-force (10,000 iterations) query to see how browsers handle that much repetition. This is where browser function call optimization really shine. Chrome benefits by about ~400%, Firefox ~2200%, and Edge comes in at ~1200%.

Looks like browsers are pretty good at optimizing this pattern, and it seems favorable to use this pattern in most cases. Of course this absolutely needs more research, to the interwebs!

## Chrome 55
| Label                             | Result  |
|-----------------------------------|---------|
|1 query test: with caching         | 0.023ms |
|1 query test: without caching      | 0.050ms |
|10 query test: with caching        | 0.017ms |
|10 query test: without caching     | 0.032ms |
|10,000 query test: with caching    | 2.392ms |
|10,000 query test: without caching | 9.070ms |

## Firefox 50
| Label                             | Result  |
|-----------------------------------|---------|
|1 query test: with caching         | 0.77ms  |
|1 query test: without caching      | 0.55ms  |
|10 query test: with caching        | 0.78ms  |
|10 query test: without caching     | 0.59ms  |
|10,000 query test: with caching    | 1.39ms  |
|10,000 query test: without caching | 22.35ms |

## Edge 38
| Label                             | Result   |
|-----------------------------------|----------|
|1 query test: with caching         | 0.0456ms |
|1 query test: without caching      | 0.13ms   |
|10 query test: with caching        | 0.045ms  |
|10 query test: without caching     | 0.07ms   |
|10,000 query test: with caching    | 3.445ms  |
|10,000 query test: without caching | 36.515ms |

These tests were done with a console.time(), so some of the data will vary from test to test.
