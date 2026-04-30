**Notice:** This repo is a clone of the one on [Dominic Radermacher's website](https://mockmoon-cybernetics.ch/computer/p-touch2430pc/), and he is he author. It is available here (https://github.com/clarkewd/ptouch-print/), with his permission, to help increase visibility of the project and to encourage collaboration and further development.


# ptouch

ptouch is a command line tool to print labels on Brother P-Touch
printers on Linux.

There is no need to install the printer via CUPS, the printer is accessed
directly via libusb.

The tool was written for and tested with the `PT-2430PC`, but it should also
work with the `PT-1230PC` (untested so far). Maybe others work too (please report USB VID and PID so I can include support for further models, too).

**Models in `libptouch.c` include:**

- PT-2420PC
- PT-1230PC
- PT-2430PC
- PT-2730
- PT-2730
- PT-P700 ( see note below about mode switch )
- PT-D450

**Further info can be found at:**
https://mockmoon-cybernetics.ch/computer/p-touch2430pc/

### Compile instructions:

    ./autogen.sh
    ./configure --prefix=/usr
    make

### Macos compilation:

    brew install libusb cmake gd cloc automake gettext

    export LDFLAGS="-L$(brew --prefix)/lib -L$(brew --prefix gettext)/lib"
    export CPPFLAGS="-I$(brew --prefix)/include -I$(brew --prefix gettext)/include"
    export LIBS="-lintl"

    ./autogen.sh
    ./configure --prefix=/usr/local
    make

### Autocut & Chain printing

Tested on PT-H500 and based on [Brother Software Developer's Manual for PT-H500/P700/E500][1]

[1]: https://download.brother.com/welcome/docp000771/cv_pth500p700e500_eng_raster_111.pdf

Due to how these printers are built cutting blade is ~21mm apart from print head.
So when you only print 1 label printer by default **feed & cut** label,
therefore next label will have ~21mm empty space before content.

PT-H500, P700 and E500 have additional modes that allow to better control this behaviour:

- Automatic cutting of "lead": printer starts printing,
  pauses ~21mm deep to **cut** empty part, print rest,
- Chain printing: by default after every job printer feeds & cut,
  this produces waste (especially on short labels).
  This mode is useful when you printing several labels, but dont want to manually
  separate each of them (after printing long strip separated by pads).

You can control these modes with options:

- `--autocut` - will enable automatic cut of "lead" empty space,
- `--chain` - will stop **feed & cut** default behaviour, so that next label
   autocut will separate consecutive labels just in right place

#### Example:

When you're printing many labels you can it this way:


    ptouch-print --text "1" --autocut --chain
    ...
    ptouch-print --text "98" --autocut --chain
    ptouch-print --text "99" --autocut

(remove `--chain` on last one so it is feed & cut as usual, but leave `--autocut` so previous is separated)

### PT-P700:

- As the PT-P700 does not have a toggle switch for the printer mode, you may need to switch the mode first
- If you plug in the PT-P700 and the green light above the PLite button is on, the printer won't be recognized by `ptouch`. You must hold down the PLite button for ~ 2 seconds and the light will turn off and the system will find the device again.
- This step allowed it to be detected as `vid 0x04F9 pid 0x2061` insetad of `vid 0x04F9 pid 0x2064`
- This allows it to be found, (eg: `./ptouch-print --info`) and also puts it into a mode that it can be interfaced with


### A Note from Dominic's website

"Dear visitor, currently I have absolutely no time for improvements on this project (my free time currently is about one or two hours PER MONTH)..."

### Collaboration

You may still, of course, contact Dominic directly if you'd like. But I think GitHub is easier for collaboration, and he has given me permission to host a clone of his repo here, passing code contributions back to him.

