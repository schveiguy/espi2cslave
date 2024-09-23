# Working i2c slave driver

This project is an internal driver for the ESP32-S2 chip, open-sourced with the hope that it helps others who are struggling to get their ESP32 devices to properly operate as an I2C slave device.

Please note the license. It's provided AS-IS and has no guarantees as to performance or fit for any particular purpose.

## Why not work with the existing driver?

The existing driver as of this writing does not properly handle I2C slave mechanisms either at a hardware or design level. Much of the problems are described in this [github issue](https://github.com/espressif/esp-idf/issues/13377#issuecomment-2316012154).

In particular:
* The way clock stretching is handled is fundamentally wrong -- the clock stretch is cleared before data is ready to transmit
* The way slave devices must behave is not captured by the driver. For example, a slave read must be initiated outside the interrupt routine, by task-level code. This is not how slaves work, they are event based, and a transaction is always initiated by the master.

Fixing such problems requires a complete rethinking of the driver API, which is why this driver was created.

## What are the symptoms the rewrite fixes?

* Transmitting initial garbage bytes, or whole garbage messages.
* Missing data from the master
* Buffering messages to send between transactions initiated by the master
* probably some more that I didn't encounter.

## This doesn't work for my device, how can I make it work?

This was a project of a closed source project, provided as open source code so you can learn from it and write your own proper driver. If you have changes that make it work for your device, and those don't alter the usability for us, I will pull those changes.

But I do not have the cycles to develop this as a general solution for all ESP devices.

Please look at the spec for your device, particularly in the registers for handling the clock stretching.

Admittedly the specs are pretty sparse, without good examples of what events trigger what interrupts. In fact, a lot of this was trial and error with all interrupts turned on!

## The documentation is bad

I know. It's an internal project. Be happy it has any documentation at all :)

### Acnknowledgement

Thanks to Unicom for letting me open source this code for others to use/learn from!
