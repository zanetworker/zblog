---
title:  "How to Get A MAP API Key for Android"
date:   2011-07-06 15:04:23
categories: [technical]
tags: [Android, Java]
---

Steps to get your key:

- you need to get an MD5 finger print
	- go to C:\Program Files\Java\jdk1.6.0_24\bin</li>
	- copy the file "debug.keystore" you will find this in C:\Users\User\.android\</li>
	- Type the following command:

  ```bash
    keytool.exe -list -alias androiddebugkey -keystore "C:\android\debug.keystore" -storepass
    android -keypass android
  ```
- Now you should be getting a finger print like this:

```bash
A5:83:11:BB:56:EE:3E:87:58:6D:49:D3:90:E6:61:11
```

- Now go to this page: http://code.google.com/android/maps-api-signup.html
- Insert your MD5 finger print and get your key: 0uNikOV3dR5gYi2qwuoNRrfR1OZRmM0uG5Arbvg
