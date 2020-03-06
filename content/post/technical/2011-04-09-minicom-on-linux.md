---
title:  "How to Setup & Use Minicom on Linux"
date:   2011-04-09 15:04:23
categories: [technical]
tags: [Linux, Networking, Cisco]
---

Some people are windows oriented, others are linux oriented. Well I consider my self more of a windows user (I am not proud of that xD)  so I decided to give linux a try and use minicom to configure Cisco devices. Here is how to download, Setup and Install minicom on ubuntu to be ready for use.

- First you have to download and install minicom:

```bash
sudo apt-get install minicom
```

Okay cool, now that you have installed it, you might want to find out the name of your serial port, Here is how:

```bash
dmesg | grep tt # This will bring all the port that are used to connected to outside terminals
```
Now it is time to configure minicom:

```bash
sudo minicom -s
```

After opting-in this command, follow these steps:

- Press A and change the serial device to the one you say in the previous step “For example: /dev/tty0”
- Press F to change the flow control to “No”
- Finally and most importantly, Press E to change your baud rate to “9600”

Now Press Esc to go back to main menu, and then save your configuration as dfl or you could save as and choose a name. For example cisco, now every time you would like to use the same configuration just type ```sudo minicom cisco```.

I hope this helps !!

Have a great day.
