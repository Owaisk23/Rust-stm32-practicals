# Busy waiting

The timer should now be properly initialized. All that's left is to implement the `delay` function
using the timer.

First thing we have to do is set the autoreload register (`ARR`) to make the timer go off in `ms`
milliseconds. Because the counter operates at 1 KHz, the autoreload value will be the same as `ms`.

``` rust
    // Set the timer to go off in `ms` ticks
    // 1 tick = 1 ms
    tim6.arr.write(|w| w.arr().bits(ms));
```

Next, we need to enable the counter. It will immediately start counting.

``` rust
    // CEN: Enable the counter
    tim6.cr1.modify(|_, w| w.cen().set_bit());
```

Now we need to wait until the counter reaches the value of the autoreload register, `ms`, then we'll
know that `ms` milliseconds have passed. That condition is known as an *update event* and its
indicated by the `UIF` bit of the status register (`SR`).

``` rust
    // Wait until the alarm goes off (until the update event occurs)
    while !tim6.sr.read().uif().bit_is_set() {}
```

This pattern of just waiting until some condition is met, in this case that `UIF` becomes `1`, is
known as *busy waiting* and you'll see it a few more times in this text `:-)`.

Finally, we must clear (set to `0`) this `UIF` bit. If we don't, next time we enter the `delay`
function we'll think the update event has already happened and skip over the busy waiting part.

``` rust
    // Clear the update event flag
    tim6.sr.modify(|_, w| w.uif().clear_bit());
```

Now, put this all together and check if it works as expected.
