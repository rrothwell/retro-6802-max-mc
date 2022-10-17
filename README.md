# retro-6802-max-mc
Construction notes for a minimalist MC6802-based SBC.

## Introduction

The PCB for constructing this microcomputer is available for purchase at [Old Micros](https://sites.google.com/site/gogleoops/6802-max-board).
This is designed by Michael Cvetanovski.

![IMG_5485](https://user-images.githubusercontent.com/1712402/196101033-4df38f91-0e36-4e1a-b37c-d459b5a77973.jpg)


Notable aspects of this design include:
1. MC6802 microprocessor. No MC6800 family clock chip required.
1. MC6821 PIA parallel input/output IC with connections exposed on an edge connnector.
1. MC6850 ACIA RS232 serial input/output porrt for communication with the microcomputer from a PC serial terminal.
1. A battery-backed RAM module doing double duty as RAM and EPROM.
1. Board address decode logic implemented in a GAL22V10 IC.
1. The DS1245 battery-backed RAM module is divided into two and the half to be used is selected via a link that ties A16 high or low.

## Notes

The following should be read in conjunction with the information on the Old Micros site.

The following substitutions were made considering what components I had on hand:
| Original                         | Substitution |
| -------------------------------- | ------------ |
| 4 MHz oscillator - long can      | 4 MHz oscillator - short can |
| 1.8432 MHz oscillator - long can | 1.8432 MHz MHz oscillator - short can |
| MC6821 PIA                       | HD63B21 PIA |
| MC6850 ACIA                      | HD63B21 ACIA |
| DS1245 nonvolatime SRAM          | DS1249 nonvolatime SRAM |

The GAL and the SRAM were programmed with the ubiquitous TL866 II plus universal programmer.
The associated software is called Xgpro. This is a MS Windows executable.

### DS1245 nonvolatime SRAM 

The objective is to provide a monitor progam that is a modified version of SWTBUG.

The file XswtbugIMAGE.HEXfile was downloaded from "Old Micros" and file contents copied into the SRAM chip at address $E000.
The programmer was set to blank unused locations to zero.

However there are some surprises.

The DS1249A uses a + designator on the package for pin 1.
However the TL866 II plus programmer and the Xgpro software only recognise the device in the reversed direction.
The programmer provides a useful error message that the chip may be reversed in its socket.

This is also the case when the DS1249A is inserted into the 6802 Max SRAM socket. 
If its inserted in the wrong direction there is a short to ground, though no damage occurs.
The power LED is extinguished when this happens.
It has to be inserted so that the + marker points to the bottom right.

A mistake in ordering the battery backed RAM meant that a DS1249A was orderd instead of a DS1245A.
This was remedied by tying pin 30 of the IC to ground. In effect half of the chip is wasted as it is not usable.

![IMG_5486](https://user-images.githubusercontent.com/1712402/196101335-44737505-e319-4501-b5ed-7e1dd87766d7.jpg)


### GAL22V10

WinCUPL was installed at c:\Wincupl.

The PLD file is downloaded from "Old Micros" and duplicated into folder c:\Wincupl\RR_sources.
In File Explorer, View tab, make sure file extensions can be viewed in case Notepad is used to create the file with a .txt extension.

![TL866 II plus PLD programming](https://user-images.githubusercontent.com/1712402/196102502-8274a380-9ba8-4154-a849-7d3f1368a8ff.png)

The GAL appears to be of Lattice manufacture and its supported by the TL866 II plus programmer.

A device dependant compile produces a 6802.jed file in the same folder at c:\Wincupl\RR_sources\6802.jed.

![WinCUPL PLD compilation](https://user-images.githubusercontent.com/1712402/196102914-8d7dd8b9-44ad-485d-b09b-6573d1068a86.png)

The 6802_MAX_GAL22V10D.pdf produced cannot be opened.

### USB-Serial dongle

The serial to USB dongle maps the connections as follows:

| Board  | Dongle |
| ------ | ------ |
| RxData | Tx |
| TxData | Rx |
| RTS    | CTS |
| 0V     | Gnd |

The dongle is attached to the board and to a Mac using a Mini-USB to serial cable in this case.

In the MacOS terminal application issue the command to show the available USB serial devices.

`
ls -al /dev/tty*
`

shows an number of entries including /dev/tty.usbserial-A50285BI.
This will vary from machine to machine.
The entry for usbserial will only appear when the dongle is attached to the Mac.
If it does not, assume the dongle is based on an FTDI chip, like FT232RB, then install the FTDI VCP (virtual comm port) driver.

Then in the terminal issue the command 

`
screen /dev/tty.usbserial-A50285BI 115200
`

This should produce a $ prompt. 
Various SWTBUG monitor commands can then be exercised.

Cntrl-A provides the escape from the screen context.



