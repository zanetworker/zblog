---
title:  "Recovering Passwords For Cisco Routers"
date:   2011-09-05 15:04:23
categories: [technical]
tags: [Cisco, Networking]
---

Here are the brief steps of how to recover passwords on Your Cisco Router:

1. Type show version to check on the current value of the configuration
register. The configuration register is usually set set to 0x2102 or 0x102.
2. Turn the router off and turn it back on.
3. Within the first 60 seconds, press Ctrl+Break to exit the Boot sequence
and enter the Rommon mode.
4. In the rommon mode type confreg 0x2142.
5. Type reset to get out of the Rommon mode.
6. Now the router will reboot and will ignore the saved configuration.
7. Now type configure memory or copy startup-config running-config to
copy the configuration from the NVRAM to the memory (RAM)
8. Now you can change the enable secret and enable password passwords.
9. After changing the configuration and retrieving the passwords, we must
change back the configuration register to boot from the startup config.
10. Finally, we save by typing write memory or copy running-config startup-
config.


### Recovering Passwords On Cisco Switches
Here are the brief steps of how to recover passwords on Your Cisco Switch:

1. Power on the switch and bring it switch: prompt. For 2960, 2970
Release the Mode button when the SYST LED blinks amber and then
turns solid green. When you release the Mode button, the SYST LED
blinks green.
2. Now at the switch: prompt issue the flash-init command.
3. Issue the load-helper command.
4. Now, Issue the dir flash: command to look into the flash.
5. Type rename flash: config.text flash:config.old.
6. Issue the boot command.
7. Enter“n“ at the prompt to abort the initial configuration.
8. Now enable at the switch prompt.
9. Type rename flash:config.old flash:config.text to rename the configura-
tion file with its original name.
10. Copy the configuration file into memory using copy flash:config.text
system:running-config
11. Finally, reset all your passwords and save the configuration onto the
NVRAM using write memory.
