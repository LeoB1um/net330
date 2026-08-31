# Week 1 Overview

Pretty simple. Just did the learning down below and a short packet tracer assignment.

# how do switches work

## Dumb switch:
have a table like this

|Port|MAC address|Timestamp|
|-|-|-|
|1|AF:af:ad:ad:ac:ac|10|10:10pm|

You build out the table and when your table is full you know whos on every port and where. it directly connects too.

No password. the password is the key to the room you put it in.

##Smart Switch:
have a table like this

|Port|MAC address|VLAN|Timestamp|
|-|-|-|-|
|1|AF:af:ad:ad:ac:ac|10|10:10pm|

Does a similar thing but also compairs VLANs. You can also set specific ports for inputs and you can assign a MAC or if you dont want to know you can set it so that the first MAC address it gets is the only MAC that gets set for that port.

Smart switches can have a password meaning that even in public environments then you dont need to 

***

If you connect the both ends of a cable to the same switch it creates whats called a NetStorm and it essentially kills the switch

Smart switches should have STP. (Spanning tree protocol) It will transfer connections to the next best connection. you do need to enable it though.


#LOOK UP HOW TO RESET PASSWORD ON A CATYLIST 3750 SWITCH, A 1841 ROUTER, AND 


#Instrcutions on resetting 1841

## Initial Connection

Install the standalone version of PuTTY for windows (avoid admin prompt) from their website

Open PuTTY

Navigate to Connections -> Serial and put in the following information

Baud: 9600
data bits: 8
Parity: no
Stop Bits: 1
Flow control: 1

Then go to `Session` in putty

Select `serial` as your connection type

Ensure you are using the correct COM port

Hit connect in PuTTY

within the first 60s press `Ctrl + Pause/Break`. Never used this key before. (In the spot above the arrow keys, may need to hold fn key as well depending on keyboard)

You will know you have succeeded when you see rommon 2> or rommon 1> , something with rommon.

# Resetting password

Type `confreg 0x2142` and hit enter

Then type `reset` once finished, (and hit enter)


when the router boots up after reset, type no when prompted for yes or no. (a bit into the boot sequence so pay attention)

Once it stops printing try pressing enter to see if it puts you into `Router>` mode

once in that mode type enable and hit enter, your terminal should look like this: `Router#`

Then type `Configure Terminal` and hit enter, your terminal should look like this: `Router (config) #`

Then type `enable secret <new password>` and hit enter to set your new password

then type `config-register 0x2102` and hit enter

then type `end` and hit enter

then type `reload` and hit enter, to restart your console and type yes when prompted