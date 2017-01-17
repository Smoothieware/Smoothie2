# Smoothie2

A Smoothie firmware port to the Bambino210E board ahead of making changes to support the SmoothieV2 hardware.

There are no guarantees that this code works, won't burn down your house or destroy your electronics or hardware, use entirely at your own risk.

There are a lot of bugs in the code and it is very much a work in progress, the reason it is published is to allow others to contribute before it is folded back into the Smoothie2 main repo. 

#IF YOU ARE NOT A CODER / WORKING ON THIS CODE BASE DO NOT TRY TO USE THIS UNTIL THE CODE HAS BEEN FULLY PORTED AND TESTED

Current status as at 2016-11-08:
* SlowTicker code finally ported, but there are timing issues that need to be solved, including interrupt priorities to get it working properly (for end-stops for instance)
* The current focus is getting all the timing and interrupts and interrupt priorities sorted, then continue testing of ported code.
* Merged changes 65fb13e and c3f254c to bring codebase current with Smoothie V2 main repo 
* Moved open issues to issue tracker
* This code base is equivalent to Smoothie V1 as at 2016-10-18 and Smoothie V2 as at 2016-11-08
* The code that relies on the priority setting of GPIO interrupts has not had this feature migrated (e.g. laser)
* Some major lower level things need implementation ( USB, Ethernet, SDIO ).  
* There some changes in `gcc4mbed/external/mbed/libraries/mbed/hal/sleep_api.h` which appear to be driven by Silicon Labs changes to support their EFM32 range of ARM processor's lower power modes.  This causes conflicts with `sleep` and I have had to slightly modify this file to get our code to compile and work properly.  I don't have time to go back and investigate this at this time.

There are a lot of bugs in the code and it is very much a work in progress, the reason it is published is to allow others to contriute before it is folded back into the Smoothie2 main repo.

Current status : 
* This code base is equivalent to Smoothie V1 as at 2016-10-18.
* The Extruder code is being worked on and as of 2016-10-22 and does not yet compile.
* Some major lower level things need implementation ( USB, Ethernet, SDIO ).  

# Update, 05/12/2016

Jim Morris ( lead Smoothie1 dev ) and Alan Carvalho de Assis ( NuttX dev ) are working on a port of Smoothie v1 to the LPC43XX by rebuilding Smoothie on top of the NuttX RTOS ( nuttx.org ) and completing the LPC43XX port of NuttX. That work is expected to take several months, but is now the "official" route to a v2 firmware.

This "mBed" version of the v2 firmware currently in this repository is expected to be left aside, even though it has advanced to the point of being capable of moving motors. We want to apologize to those who have put effort into this version of the port so far. Hopefully the many advantages of moving Smoothie to a RTOS will outweigh the wasting of this codebase, and you will be as exited as we are about the new direction.

If you want to help with the NuttX version of the v2 firmware, please note there will be a period of construction during which only Jim and Alan will work on it, but after that help will be extremely welcome.

# Compiling

First, go into gcc4mbed

    cd gcc4mbed/

Then, install the toolchain

    ./linux_install

Finally, setup the build environment

    ./BuildShell

Then go to the Smoothie2 source code

    cd ../src/

And finally compile the code

    make

# Board Specific Programming/Debugging Notes
* [Bambino210E Boards](https://github.com/Smoothieware/Smoothie2/blob/master/notes/Bambino210E-board.creole#bambino210e-board-programmingdebugging-notes)

# TODO : 

Current major TODOs : 
 * SD-Card, USB and Ethernet need to be implemented, awaiting base libraries from Micromint.com.
 * Move the step generation to the M0 co-processor instead of on the M4 main core OR into a FPGA.

TODO functionality to port from v1 in more detail : 
 * libs/Adc.cpp : Port low level ADC functionality, then re-enable it in TemperatureControl ( note : there is some weird pin configuration surrounding this that makes it more complicated than it seems, but I don't remember what it is. Logxen@gmail.com probably remembers what it is ).
 * libs/md5.cpp : Port and re-enable in SimpleShell
 * libs/Network : Port and enable once base libraries are available
 * [@adamgreen](https://github.com/adamgreen) - libs/SDFAT.cpp : Port for SPI SD card access, not SDIO, and re-enable. Then add SDIO support.
 * [@adamgreen](https://github.com/adamgreen) - libs/SPI.cpp : Port and make to use the mBed library instead of registers
 * libs/USBDevice : Port and enable once base libraries are available
 * libs/Watchdog : Port ( no mBed libraries available that I could find )
 * libs/Hook.h : Uses doubles instead of ints ( in v1 ) for some reason, should be fixed
 * modules/tools/filamentdetector : Get PWM to work
 * modules/tools/spindle : Get PWM to work
 * modules/tools/temperaturecontrol : Finish porting, get the ADC to actually work, I cannot test the AD8495 temperature inputs as I don't have one of these!
 * modules/tools/zprobe : Ported Needs testing (2016-11-08)
 * modules/tools/drillingcycles : Ported Needs testing (2016-11-08)
 * modules/tools/extruder : Ported Needs testing (2016-11-08)
 * modules/tools/filamentdetector : Port
 * modules/tools/filamentdetector : Get PWM to work
 * modules/tools/spindle : Get PWM to work
 * modules/tools/temperaturecontrol : Finish porting, get the ADC to actually work, port the AD8495 temperature input
 * modules/tools/zprobe : Ported Needs testing (2016-11-08)
 * modules/tools/simpleshell : Port once USB ported

 * The FPGA requires a SGPIO spi library
 * The FPGA requires a way to flash a .bin to it over JTAG from the M4 

Things that are broken during the port and should be added back as things progress ( some things might be missing here. I try to use the TOADDBACK label when commenting things so search for that too ) : 

* In Switch.cpp : Commented out all of the set_low_on_debug stuff; do we still need this?
* In SlowTicker.h and Hook.h : Using doubles instead of ints for intervals/frequencies and counting down. Done to accomodate mBed, but dirty costly hack
* In RingBuffer.h : Removed the irq stuff
* In Laser.cpp : PWM pin is broken, needs to be fixed

Things changed from Smoothie1 : 

* Pin now uses mBed instead of registers
* SlowTicker now uses mBed instead of registers
* StepTicker now uses mBed instead of register
* Various "waits" now use mBed's Timer

Planned refactors ( only to be done when the basic port is done ) : 

* Remove the checksum system for config options
* Try to read config directly from SD card without cache ( as smoothie used to do ) now that we have SDIO
* Queue refactor ( see wiki )
* Adding MTP and removing MSD for USB
* Explore using http://nuttx.org/ for a RTOS ( major rewrite )
