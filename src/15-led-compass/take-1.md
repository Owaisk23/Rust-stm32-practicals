# Take 1

What's the simplest way in which we can implement the LED compass? Even if it's not perfect.

For starters, we'd only care about the X and Y components of the magnetic field because when you
look at a compass you always hold it in horizontal position thus the compass is in the XY plane.

For example, what LED would you turn on in the following case. EMF stands for Earth's Magnetic Field
and green arrow has the direction of the EMF (it points north).

<p align="center">
<img title="Quadrant I" src="../assets/quadrant-i.png">
</p

The `Southeast` LED, right?

What *signs* do the X and Y components of the magnetic field have in that scenario? Both are
positive.

If we only looked at the signs of the X and Y components we could determine to which quadrant the
magnetic field belongs to.

<p align="center">
<img title="Quadrants" src="../assets/quadrants.png">
</p>

In the previous example, the magnetic field was in the first quadrant (x and y were positive) and it
made sense to turn on the `SouthEast` LED. Similarly, we could turn a different LED if the magnetic
field was in a different quadrant.

Let's try that logic. Here's the starter code:

``` rust
#![deny(unsafe_code)]
#![no_main]
#![no_std]

#[allow(unused_imports)]
use aux15::{entry, iprint, iprintln, prelude::*, Direction, I16x3};

#[entry]
fn main() -> ! {
    let (mut leds, mut lsm303dlhc, mut delay, _itm) = aux15::init();

    loop {
        let I16x3 { x, y, .. } = lsm303dlhc.mag().unwrap();

        // Look at the signs of the X and Y components to determine in which
        // quadrant the magnetic field is
        let dir = match (x > 0, y > 0) {
            // Quadrant ???
            (true, true) => Direction::Southeast,
            // Quadrant ???
            (false, true) => panic!("TODO"),
            // Quadrant ???
            (false, false) => panic!("TODO"),
            // Quadrant ???
            (true, false) => panic!("TODO"),
        };

        leds.iter_mut().for_each(|led| led.off());
        leds[dir].on();

        delay.delay_ms(1_000_u16);
    }
}
```

There's a `Direction` enum in the `led` module that has 8 variants named after the cardinal points:
`North`, `East`, `Southwest`, etc. Each of these variants represent one of the 8 LEDs in the
compass. The `Leds` value can be indexed using the `Direction` `enum`; the result of indexing is the
LED that points in that `Direction`.
