# What's left for you to explore

We have barely scratched the surface! There's lots of stuff left for you to explore:

## Multitasking

All our programs executed a single task. How could we achieve multitasking in a system with no OS,
and thus no threads. There are two main approaches to multitasking: preemptive multitasking and
cooperative multitasking.

In preemptive multitasking a task that's currently being executed can, at any point in time, be
*preempted* (interrupted) by another task. On preemption, the first task will be suspended and the
processor will instead execute the second task. At some point the first task will be resumed.
Microcontrollers provide hardware support for preemption in the form of *interrupts*.

In cooperative multitasking a task that's being executed will run until it reaches a *suspension
point*. When the processor reaches that suspension point it will stop executing the current task and
instead go and execute a different task. At some point the first task will be resumed. The main
difference between these two approaches to multitasking is that in cooperative multitasking *yields*
execution control at *known* suspension points instead of being forcefully preempted at any point of
its execution.

## Direct Memory Access (DMA).

This peripheral is a kind of *asynchronous* `memcpy`. So far our programs have
been pumping data, byte by byte, into peripherals like UART and I2C. This DMA
peripheral can be used to perform bulk transfers of data. Either from RAM to
RAM, from a peripheral, like a UART, to RAM or from RAM to a peripheral. You can
schedule a DMA transfer, like read 256 bytes from USART1 into this buffer, leave
it running in the background and then poll some register to see if it has
completed so you can do other stuff while the transfer is ongoing.

## Sleeping

All our programs have been continuously polling peripherals to see if there's
anything that needs to be done. However, some times there's nothing to be done!
At those times, the microcontroller should "sleep".

When the processor sleeps, it stops executing instructions and this saves power.
It's almost always a good idea to save power so your microcontroller should be
sleeping as much as possible. But, how does it know when it has to wake up to
perform some action? "Interrupts" are one of the events that wake up the
microcontroller but there are others and the `wfi` and `wfe` are the
instructions that make the processor "sleep".

## Pulse Width Modulation (PWM)

In a nutshell, PWM is turning on something and then turning it off periodically
while keeping some proportion ("duty cycle") between the "on time" and the "off
time". When used on a LED with a sufficiently high frequency, this can be used
to dim the LED. A low duty cycle, say 10% on time and 90% off time, will make
the LED very dim wheres a high duty cycle, say 90% on time and 10% off time,
will make the LED much brighter (almost as if it were fully powered).

In general, PWM can be used to control how much *power* is given to some
electric device. With proper (power) electronics between a microcontroller and
an electrical motor, PWM can be used to control how much power is given to the
motor thus it can be used to control its torque and speed. Then you can add an
angular position sensor and you got yourself a closed loop controller that can
control the position of the motor at different loads.

## Digital input

We have used the microcontroller pins as digital outputs, to drive LEDs. But
these pins can also be configured as digital inputs. As digital inputs, these
pins can read the binary state of switches (on/off) or buttons (pressed/not
pressed).

(*spoilers* reading the binary state of switches / buttons is not as
straightforward as it sounds ;-)

## Sensor fusion

The STM32F3DISCOVERY contains three motion sensors: an accelerometer, a
gyroscope and a magnetometer. On their own these measure: (proper) acceleration,
angular speed and (the Earth's) magnetic field. But these magnitudes can be
"fused" into something more useful: a "robust" measurement of the orientation of
the board. Where robust means with less measurement error than a single sensor
would be capable of.

This idea of deriving more reliable data from different sources is known as
sensor fusion.

## Analog-to-Digital Converters (ADC)

There are a lots of digital sensors out there. You can use a protocol like I2C
and SPI to read them. But analog sensors also exist! These sensors just output a
voltage level that's proportional to the magnitude they are sensing.

The ADC peripheral can be use to convert that "analog" voltage level, say `1.25`
Volts,into a "digital" number, say in the `[0, 65535]` range, that the processor
can use in its calculations.

## Digital-to-Analog Converters (DAC)

As you might expect a DAC is exactly the opposite of ADC. You can write some
digital value into a register to produce a voltage in the `[0, 3.3V]` range
(assuming a `3.3V` power supply) on some "analog" pin. When this analog pin is
connected to some appropriate electronics and the register is written to at some
constant, fast rate (frequency) with the right values you can produce sounds or
even music!

## Real Time Clock (RTC)

This peripheral can be used to track time in "human format". Seconds, minutes,
hours, days, months and years. This peripheral handles the translation from
"ticks" to these human friendly units of time. It even handles leap years and
Daylight Save Time for you!

## Other communication protocols

SPI, I2S, SMBUS, CAN, IrDA, Ethernet, USB, Bluetooth, etc.

Different applications use different communication protocols. User facing
applications usually have an USB connector because USB is an ubiquitous
protocol in PCs and smartphones. Whereas inside cars you'll find plenty of CAN
"buses". Some digital sensors use SPI, others use I2C and others, SMBUS.

---

So where to next? There are several options:

- You could check out the examples in the [`f3`] board support crate. All those examples work for
  the STM32F3DISCOVERY board you have.

[`f3`]: https://docs.rs/f3

- You could try out [this motion sensors demo][madgwick]. Details about the implementation and
  source code are available in [this blog post][wd-1-2].

[madgwick]: https://mobile.twitter.com/japaricious/status/962770003325005824
[wd-1-2]: http://blog.japaric.io/wd-1-2-l3gd20-lsm303dlhc-madgwick/

- You could check out [Real Time for The Masses]. A very efficient preemptive multitasking framework
  that supports task prioritization and dead lock free execution.

[Real Time for The Masses]: https://docs.rs/cortex-m-rtfm

- You could try running Rust on a different development board. The easiest way to get started is to
  use the [`cortex-m-quickstart`] Cargo project template.

[`cortex-m-quickstart`]: https://docs.rs/cortex-m-quickstart/0.2.4/cortex_m_quickstart

- You could check out [this blog post][brave-new-io] which describes how Rust type system can
  prevent bugs in I/O configuration.

[brave-new-io]: http://blog.japaric.io/brave-new-io/

- You could check out my [blog] for miscellaneous topics about embedded development with Rust.

[blog]: http://blog.japaric.io

- You could check out the [`embedded-hal`] project which aims to build abstractions (traits) for all
  the embedded I/O functionality commonly found on microcontrollers.

[`embedded-hal`]: https://github.com/rust-embedded/embedded-hal

- You could join the [Weekly driver initiative] and help us write generic drivers on top of the
  `embedded-hal` traits and that work for all sorts of platforms (ARM Cortex-M, AVR, MSP430, RISCV,
  etc.)

[Weekly driver initiative]: https://github.com/rust-lang-nursery/embedded-wg/issues/39
