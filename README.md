Copyright 2012 Constantin Kulikov  

Author: Constantin Kulikov (Bad_ptr) <zxnotdead@gmail.com>  
Date: 2013/07/09 21:10:53  
License: GPL either version 2 or any later version  
Keywords: xprop, xlib  
X-URL: https://github.com/Bad-ptr/xsetprop  


# Intro

xsetprop -- set window properties. I write it couse the xprop can't set a list of atoms.  

For example:

    xprop -f _NET_WM_STATE 32a -set _NET_WM_STATE _NET_WM_STATE_ABOVE

works fine

    xprop -f _NET_WM_STATE 32a -set _NET_WM_STATE _NET_WM_STATE_ABOVE,_NET_WM_SKIP_TASKBAR

don't work

    xsetprop --remap --mode='replace' --id=WIN_ID --format='32a' --propname='_NET_WM_STATE' --value='_NET_WM_STATE _NET_WM_STATE_ABOVE,_NET_WM_SKIP_TASKBAR'

OK. :)

# Compilation

    make

# Usage

    xsetprop xsetprop [--id=[window_id]] (--format=<32a> --propname=<WM_ICON_NAME>|--atom <ATOMNAME>|--string <PROP_NAME>) --value=<value> [--mode=[replace|append|prepend]] [--remap]

`--id` or `-i` (optional): window id, which you can get from xwininfo. If omitted -- you will be prompted to select a window with mouse.  
`--root` : use the root window.  
`--format` or `-f` : format of a property. 32a for atoms, 8s for strings, etc(See man xprop).  
`--propname` or `-p` : name of a property you want to set.  
`--value` or `-v` : A new value or a value to append/prepend of property(see --mode option).  
`--mode` or `-m` (optional): 'replace' or no specify for discarding the old value, append/prepend to append/prepend to the old value.  
`--remap` (optional): if this flag is specified the window will be unmapped and then mapped again after changing the propertyes(It helps WMs to see changes).  

A shorter form:

    xsetprop [--id=[window_id]] (--atom <ATOMNAME>|--string <PROP_NAME>) [--value <VALUE>]

(you don't need to specify --format and --propname).

# Alternative

Or you can use xprop.patch to make xprop work as you want. :)


# Change in 2021

In line 306 change CARDINAL to WINDOW, that turn possible change parameters of WM_TRANSIENT_FOR and attach one window in another window

Example:
xsetprop -id=0x00400006 --atom WM_TRANSIENT_FOR --value 0x05600011 -f 32x
To attach 0x00400006 in 0x05600011

To see all windows with id use:
wmctrl -p -l

To remove icon of one window in taskbar use:
wmctrl -i -r 0x00400006 -b add,skip_pager,skip_taskbar

And to turn little more cool, change to modal:
wmctrl -i -r 0x00400006 -b toggle,modal

Or using shell in easy way, just need change W1 and W2 value:

#!/bin/bash
W1="0x00400006"
W2="0x05600011"

xsetprop -id=$W1 --atom WM_TRANSIENT_FOR --value $W2 -f 32x
wmctrl -i -r $W1 -b add,skip_pager,skip_taskbar
wmctrl -i -r $W1 -b toggle,modal
