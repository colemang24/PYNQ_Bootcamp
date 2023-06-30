# Interacting with the PYNQ Board

Earlier you started to get familiar with the Jupyter Notebook interface, writing Markdown, and running Python code. Nothing so far has been specific to the PYNQ board. This all could have easily been run on your laptop instead. Now, we'll start looking at the PYNQ board specifically — including playing with the on-board switches and LED lights.

Before we start interacting with the physical world, let's try and convince ourselves that the code we write is indeed running on the PYNQ board.

## Contents

* [Getting PYNQ system information](#Getting-PYNQ-system-information)
* [LEDs, switches and buttons](#LEDs-switches-and-buttons)
* [Controlling an LED](#Controlling-an-LED)
* [Reading from switches and buttons](#Reading-from-switches-and-buttons)
* [Reacting to button presses](#Reacting-to-button-presses)
* [Summary](#Summary)


## Getting PYNQ system information

First of all, let's get the "hostname" of the device this code is running on. There is a system command (i.e. not a Python command) called `hostname` that does just this. Any command we type after a `!` will be executed as a system command. Try it below:


```python
!hostname
```

    pynq
    

The fact that the host name is "pynq" should start to convince you that this code is running on the board, and not your laptop!

Note that we can use the `!` symbol to run *any* Linux command we want. For example, we'll use this in some of the sessions tomorrow to install extra software.

Now that we've seen that the commands we write are indeed run on the PYNQ board, let's use the board to interact with something physical!

## LEDs, switches and buttons

The PYNQ board some on-board controls, including:

  * Four green LEDs (called `LD0` to `LD3`)
  
  * Two multi-color LEDs (`LD4` and `LD5`)
  
  * Two slide-switches (`SW0` and `SW1`) and...
  
  * Four push-buttons (`BTN0` to `BTN3`)
  
Have a look at the board and try to identify each of these elements.

Note that there are additional push-buttons and LEDs on the board, but these are used for specific functions (Power LED, a reset button, etc.) and are not user accessible.

These inputs and outputs can be controlled directly from Python. To demonstrate this, we first need get a Python representation of the board — let's call it `board`.


```python
from pynq.overlays.base import BaseOverlay

board = BaseOverlay("base.bit")
```



This `board` value has lots of different properties associated with it. The names for the buttons, switches, and LEDs are shown below:

![](./data/z2_peripherals.svg)

For example, if we wanted to do something with the rightmost LED, we would access it via `board.leds[0]`. Let's dive in to how to use each of these controls.

## Controlling an LED

Now that we have this `board` reference, we can start manipulating the LEDs. There are 3 main things we can do with each LED:

  1. Turn it on, using `.on()`
  2. Turn it off, using `.off()`
  3. "Toggle" it between off and on, using `.toggle()`

Let’s start by turning a single LED on and off.


```python
led0 = board.leds[0]
```


```python
led0.on()
```

Check the board and confirm the LED is on.


```python
led0.off()
```

Instead of having to manually toggle our LEDs, we'll now work our way up to flashing the LED on and off automatically. Let's use what we've learned about for loops to toggle LED0 on and off!

We can use the `sleep()` method from the `time` package to introduce a delay. This should let us control how quickly the LED flashes.

Let's try toggling the LED 20 times using our `for` loop. We'll wait for 0.1 seconds each time before toggling the LED.


```python
import time

for number in range(20):
    led0.toggle()
    time.sleep(.1)
```

 Note that we can only run one cell at a time, so be sure to wait until the loop finishes before continuing.

## Reading from switches

Next, we can start interacting with the LEDs using the switches. We can use `.read()` to get the value of a button or switch.

First, let's make sure that all of the LEDs off. Instead of writing 4 lines (one for each LED), let's use a `for` loop to turn off all the LEDs.


```python
for led_num in range(board.leds.length):
    board.leds[led_num].off()
```

Now, let's use the switches to control the two multi-color LEDs. Since we only want to use the multi-color LEDs in positions 4 and 5 of our LEDs list, we should ignore the four LEDs in positions 0, 1, 2, and 3. 

We want to turn on each LED when the corresponding switch is "on", but when the switch is "off", we want the LEDs to turn off as well. We can apply an if-else statement here, to do exactly that! 

Because these are multi-color LEDs, we also need to tell it what color to use. Try changing the `color` number to different values between 1 and 7.


```python
color = 1 # Try changing this!

for switch_num in range(board.switches.length):
    led_num = switch_num + 4
    
    if board.switches[switch_num].read():
        board.rgbleds[led_num].on(color)
    else:
        board.rgbleds[led_num].off()
```

## Summary

Let's just summarize what we've covered:

  * Used system commands to get information specific to the PYNQ board
  * Started to interact with the physical world from Python!
    + Turning LEDs on and off
    + Reading input from switches

### Bonus Challenges

Again, if you finish this session early, there are some optional challenges for you to work through. You now know enough to try some pretty neat things with the PYNQ board's inputs and outputs. For example, try:

  * Start by displaying a custom pattern on the 4 LEDs
  * Now, try making this pattern rotate (LD0 becomes LD1, LD1 becomes LD2, etc.) when a button is pressed
  * Let the direction of the rotation be set by one of the switches 
  * Try making an animation using the LEDs. Start simple, but get as complex as you'd like!  
----

[Back to the top](#Contents)

----
