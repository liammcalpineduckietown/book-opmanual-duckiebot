```{seo}
:description: Duckietown SD card flashing
:keywords: Duckietown, Duckiebot, sd-card, sd card, flashing, reflashing, initialization
```

(reflash-microcontroller)=
# Debug - Re-flash Microcontroller

```{needget}
* A Duckiebot of [configuration](#duckiebot-configurations) `DB18` or above.
* A stable network connection to your Duckiebot.
---
* A flashed microcontroller (not SD card) on the HUT board, with the latest code version.
```

```{warning}
This procedure is needed only if your Duckiebot does not recognize the HUT (Dashboard > Robot > Components). Although often unnecessary, it is safe to perform on any HUT of version `2.0` and above.   
```

(reflash-microcontroller-when)=
## When and why should I run this procedure?

This procedure flashes the microcontroller on the Duckietown HUT. This microcontroller is responsible for translating the duty cycle commands from the onboard computer to actual `PWM` signals that control the motors and the LEDs (because they are "addressable" LEDs) of the Duckiebots.

A typical example of when is necessary to flash the microcontroller is when:

(a) commands are sent to the motors, e.g., through keyboard control,
(b) the motors signals on the dashboard/mission control show that signals are correctly being sent, 

but the Duckiebot does not move.  

This procedure will not be useful to fix problems such as one motor working and not the other, or LEDs showing unexpected colors when the motors work.

(reflash-microcontroller-how-dts)=
## How to flash the microcontroller - Method 1: with `dts`
On your computer

```
dts duckiebot hut_upgrade [ROBOT_NAME]
```

There are instructions on-screen to guide you through the process.

During the process, you will be asked twice to compare the command output against some expected outputs. For example,

```
avrdude: verifying ...
avrdude: 2832 bytes of flash verified

avrdude: safemode: Fuses OK (E:FF, H:DF, L:E2)

avrdude done.  Thank you.

================================================================================
=== (Above) command output =====================================================

================================================================================

=== (Below) expected output ====================================================
================================================================================

avrdude: verifying ...
avrdude: 2832 bytes of flash verified

avrdude: safemode: Fuses OK (E:FF, H:DF, L:E2)

avrdude done.  Thank you.

Did the command output match the expected output? (Y/N, default N):
```

Once confirmed, you could type 'Y' and press the "Enter" key to continue.


(reflash-microcontroller-how)=
## How to flash the microcontroller - Method 2: step-by-step via SSH

SSH into your Duckiebot by running:

    ssh duckie@![ROBOT_NAME].local


```{note}
All of the following instructions are run on Duckiebot throught he SSH terminal
```


Install the packages needed to compile the microcontroller firmware:

    sudo apt-get update
    sudo apt-get install bison autoconf flex gcc-avr binutils-avr gdb-avr avr-libc avrdude build-essential

Clone the firmware for the microcontroller using the following command:

    git clone https://github.com/duckietown/fw-device-hut.git

Navigate inside the repository you cloned :

    cd fw-device-hut

Warning: read the next passages carefully. Do not just copy and paste every line of code!

Copy the `avrdude.conf` file in the `/etc` folder of the robot. **If** you are running a Duckiebot with an NVIDIA Jetson Nano board run:

    sudo cp _avrdudeconfig_jetson_nano/avrdude.conf /etc/avrdude.conf
    
**else**, if you have a Raspberry Pi based Duckiebot, use:

    sudo cp _avrdudeconfig_raspberry_pi/avrdude.conf /etc/avrdude.conf
    
**Then**, test the `avrdude` and set the low-level configuration with:

    make fuses

A successful outcome looks like:

    avrdude: verifying …
    avrdude: 1 bytes of efuse verified

    avrdude: safemode: Fuses OK (E:FF, H:DF, L:E2)


    avrdude done.  Thank you.

**If** you see the message `make: warning: Clock skew detected. Your build may be incomplete.` or the process is not stopping, stop the process pressing <kbd>Ctrl</kbd>-<kbd>C</kbd> and run:

    find -exec touch \{\} \;

And then retry running the `make fuses` command.


To complete the procedure (in all cases, whether or not a warning was issued), remove all temporary files by running:

    make clean

Compile the firmware and upload it to the microcontroller:

    make

The resulting output should be:

    ```none
    .....

    sudo avrdude -p attiny861 -c linuxgpio -P  -q -U flash:w:main.hex

    avrdude: AVR device initialized and ready to accept instructions

    Reading | ################################################## | 100% 0.00s

    avrdude: Device signature = 0x1e930d (probably t861)
    avrdude: NOTE: "flash" memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
    avrdude: erasing chip
    avrdude: reading input file "main.hex"
    avrdude: input file main.hex auto detected as Intel Hex
    avrdude: writing flash (2220 bytes):

    Writing | ################################################## | 100% 0.75s

    avrdude: 2220 bytes of flash written
    avrdude: verifying flash memory against main.hex:
    avrdude: load data flash data from input file main.hex:
    avrdude: input file main.hex auto detected as Intel Hex
    avrdude: input file main.hex contains 2220 bytes
    avrdude: reading on-chip flash data:

    Reading | ################################################## | 100% 0.58s

    avrdude: verifying ...
    avrdude: 2220 bytes of flash verified

    avrdude: safemode: Fuses OK (E:FF, H:DF, L:E2)

    avrdude done.  Thank you.
    ```

Remove the cloned repository to free up space:

    cd .. && rm -rf fw-device-hut


and finally reboot the Duckiebot:

    sudo reboot

After reboot your Duckiebot should move normally and LEDs respond nominally. The Dashboard / components page will show a green status for the HUT, too.
