readme.txt for ADSP-CM408F EZ-Board Power-On Self-Test (POST)

Last updated:	February 19, 2013

__________________________________________________________

CONTENTS

I.   FUNCTIONAL DESCRIPTION
II.  IMPLEMENTATION DESCRIPTION
III. OPERATION DESCRIPTION
IV.  POST NOTES
V.   USING FLASH FILES

__________________________________________________________

I.  FUNCTIONAL DESCRIPTION

This directory contains an example power-on self-test (POST) project 
which tests the many peripherals of the ADSP-CM408F Board.  
This application is also loaded into the SPI flash on the 
ADSP-CM408F EZ-Board.  Using the following instructions, you can 
test each peripheral and also create a loader file suitable 
for loading into the on-board flash.

__________________________________________________________

II.  IMPLEMENTATION DESCRIPTION

Executes a POST on the EZ-KIT Lite.  By selecting certain pushbutton 
sequences, you can choose which test or tests to run.

__________________________________________________________

III.  OPERATION DESCRIPTION

Note that POST is designed to work on a specific rev EZ-Board.
If you want to build for a different rev EZ-Board you need to
change a definition in the project settings.  To do that follow
these steps:

- From the IAR C/C++ perspective, click on the 
  "Project->options->C/C++ compiler->Preprocessors" menu item and 
  select "Properties".
- Select "C/C++ Build" and then "Settings" in the left hand window.
- In the "Tool Settings" Tab select the preprocessor under the C/C++
  Compiler and enter the define for your EZ-Board in the Preprocessor
  definitions window.  Valid options are:
    _CM408F_EZBOARD_REV_1_0_
- If a valid board rev is not defined POST will not build.
- Then click "OK" and rebuild the project.

* Jumper settings for EZ-Board:
	
	P4			INSTALLED
	P5			INSTALLED
	P6			INSTALLED
	P7			INSTALLED 
	JP2			1-2
	
__________________________________________________________

 IV.  POST TEST NOTES
 
  
This POST uses a pushbutton sequence to select individual tests.  
With only two pushbuttons available and a large number of tests, 
it requires that you enter a 5-button sequence to choose a test.
The only exception is the standard loop, which can also be entered 
with a single pushbutton pressed at reset. In all cases, PB1 = 0 
and PB2 = 1 (e.g. 0-1-0-0-0 = PB1-PB2-PB1-PB1-PB1)

This readme makes frequent references to the following blinking patterns.
The "standard blinking pattern" is defined as all LEDs blinking in sequence, one by one.
The "original blinking pattern" is defined as all LEDs blinking together.

Note that this POST allows you to run multiple tests without resetting 
the board.  After a test runs successfully, it will return to the 
standard blinking pattern.  At this time, you may choose to run 
another test.  If you want to run a single test continuously, 
enter the LOOP_FLAG sequence (1-0-1-0-1); when the standard blinking 
pattern returns, enter the test on which to loop.

Refer to the table in main.c for all the pushbutton sequences 
and their corresponding LED status.  All references to LED status 
will be in the order of LED4-LED3-LED2-LED1.  Also, since multiple tests 
will have the same LED pattern, a failure will result in the pattern 
being blinked a certain number of times with a larger delay between 
each series.  For example, TEST1 and TEST8 have the same LED 
pattern (0-0-1).  If TEST1 fails, it will blink the pattern one time 
since it is in the first group.  If TEST8 fails, it will blink the 
pattern twice since it is in the second group.  
Failing test patterns would look like this for the two tests:

TEST1:  0-0-1................0-0-1................0-0-1................
TEST8:  0-0-1...0-0-1........0-0-1...0-0-1........0-0-1...0-0-1........

Note that this POST sends helpful debug information over the UART connector.  
By placing a serial cable between the J3 connector and a COM port on your PC,
you can receive useful debug status information. To turn on this debug 
feature, "__DEBUG_UART__" must be defined in post_debug.h. In order to receive 
debug  information, you must run a terminal application on the PC with 
these settings:
*  9600 baud
*  8 data bits
*  no parity
*  1 stop bit
*  no flow control

The following sections contain notes for running each test:

Standard Loop Tests

NOTE: The UART test will not work if there is a cable connected to J3.
When this test starts, you will be prompted to short the Tx-Rx pins and 
press PB2 (DB9 connector pins 2-3). The Standard loop requires the CAN 
loopback cable be installed prior to starting.

1.  Power up the EZ-Board.
2.  After all LEDs starts to blink, press 0-0-0-0-0 to start the 
	standard loop test.  
3.  The first test is processor version: 0-0-1.  
    If this fails (it is only considered a warning), continue with 
    the rest of the tests, but alert Engineering of this warning.
4.  The next test is pushbuttons and LEDs (PBLED): 0-1-0.  
    Press and release PB1, PB2, and SW3(Wake) one at a time.  
    You should see the LEDs toggling with each press.
5.  The next test is the UART test. Short pins 2-3 of the J3 DB9 connector 
        (RS-232) and pins 2-3 and 7-8 of the J2 DB9 connector (RS-485) and 
        press PB2. If the test passes, it will go on to the next test. If 
        you were monitoring POST over the serial UART port, you can reconnect 
        at this time.
6.  At this point, the tests will continue without requiring user input.
    When the last test is reached, it will loop back to this point 
    so that tests will continue to run without any further user input.
7.  If a test fails, POST will stop at that point and blink the 
    LED pattern for the failing test.  Refer to the table in main.c 
    for LED patterns.
8.  When you have finished the standard loop test, you may power off 
    the EZ-Board.

Ethernet test
The Ethernet test requires connecting to an active network

1.  Attach the network to the Ethernet connector.
2.  After all LEDs starts to blink, press 0-0-1-1-0 to start the 
	Ethernet test.
3.  If THE test fails, POST will stop at that point and blink the 
    LED pattern for thIS test.
4.  If the test passes, the LEDs will return to the standard blinking pattern.


TWI Display Test

1.  Power up the EZ-Board, if POST is running the LEDs should be blinking the standard pattern.
2.  On the board, start the test by pressing pushbutton 0-1-0-0-0.
3.  The Display should change to "TWI Display Test" on one line and
    "Press PB2 to exit." Press PB2 to terminate the test.
4.  LEDs should return to the original blinking pattern to indicate success. 
	If the test fails, the status LEDs will blink the test pattern.



_________________________________________________________

 V.  USING FLASH FILES
 
POST ships already programmed into the internal serial flash.  
If you wish to make changes to POST or create your own application 
to program into flash, you may do so as well.  Flash drivers and
interface applications are provided for each flash on this EZ-Board 
allowing you to program each flash.

Note that POST has only been tested and verified to boot from
serial flash on this EZ-Board.  

1.  Open an ADSP-CM408F EZ-Board session in IAR.
2.  Import the "Power_On_Self_Test" project.
3.  From the "Project" menu, choose "Options...".
4.  Click the General Options Category 
5.  On the Target tab, select "Device" radio button.
6.  Browse to Analog Devices and select AnalogDevices ADSP-CM40z-384_2048
7.  In the Linker category, Config tab, ensure The Override default is 
    unchecked for the linker configuration file.	
8.  In the Debugger category, Setup tab, ensure The Override default is 
    unchecked for the device description file.	
9. On the Download tab, check the Use flash loader(s) box.
10. In the J-Link/J-Trace Debugger sub-category, Setup tab, ensure Reset 
    is set to Connect during reset.
11. In the J-Link/J-Trace Debugger sub-category, Connection tab, ensure 
    Interface is set to SWD.

Check the IAR Embedded Workshop Help for additional information. Located in 
Debugging reference | Flash loaders | Using flash loaders.


