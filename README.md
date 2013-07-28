CirQ
====

A simple Circular Buffer / Queue with great test coverage.

A circular queue is basically an array that grows to a certain
size as you `.push(thing)` and then begins overwriting the
old things if they haven't been `.shift()`d yet.

Because everything in JavaScript is an Object, including Arrays,
you might prematurely conclude (as did I) that the work that
an Array has to do to perform `.shift()`
(i.e. that it has to rename key every element in the Array Object)
might be too slow for your application.

If so, you're probably wrong (as was I)...
unless, of course, you're storing > 250,000 things in your in-memory queue
(which I happen to know you aren't)

Installation
===

```bash
git clone https://github.com/coolaj86/cirqjs.git
```

Usage
===

```javascript
var CirQ = require('cirq')
  , maxLength = 1
  , cirq = new CirQ(maxLength)
  , assert = require('assert')
  ;

// the default maxLength is `4294967295`, which is `Math.pow(2,32) - 1);`
// this is the maximum safe integer value
cirq.push('a');
cirq.push('b');

assert.deepEqual(['b'], cirq.toArray());

assert.strictEqual('b', cirq.pop());

assert.deepEqual([], cirq.toArray());
```

API
===

  * `new CirQ(maxLength)`
  * `push(item)`
  * `pop()`
  * `toArray()`

Why
===

Because I didn't run a performance test first.

Despite rumors, `[].shift()` is probably efficient enough (until about 500,000 elements).

:'(

    function bench(max, postMax) {
      console.log(max, postMax);

      var now = new Date().valueOf()
        , q = new CirQ(max)
        , i
        ;

      for (i = 0; i < max; i += 1) {
        q.push('xyz');
      }
      console.log(new Date().valueOf() - now);
      now = new Date().valueOf();

      for (i = 0; i < postMax; i += 1) {
        // q.shift(); // for Array
        q.push('abc');
      }
      console.log(new Date().valueOf() - now);

      console.log();
    }

Benchmark
===

The first number is **how many items the queue may hold**
before it starts overwriting itself.

The second number is how many items over the max to insertor,
in other words, **how many times we cause the queue to rekey itself**.

The first time is how long it took to fill the queue.

The second time is how long it took to rekey the queue n times
(the 2nd number mentioned above).

CirQ

    5000 1000
    1 ms
    0 ms

    50000 10000
    19 ms
    2  ms

    500000 100000
    146 ms
    26  ms


Array (shift and push)

    5000 1000
    0  ms
    0  ms

    50000 10000
    11 ms
    1  ms

    500000 100
    57  ms
    167 ms

    500000 1000
    67   ms
    1631 ms

    5000000 1
    407  ms
    11   ms
