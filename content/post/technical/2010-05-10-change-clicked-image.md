---
title:  "How to Change the Color of an image when Clicked in Android ?"
date:   2010-05-10 15:04:23
categories: [technical]
tags: [Android, Java]
---

In advanced mobile applications or normal ones , a developer should communicate with the user using some feed backs so changing the color of an image or label when clicked gives the user the feedback that he has really clicked something, here how it is done.  In your ```onClick()``` method , use the following method :

```java
 setColorFilter("color in hexa" , "mode")
```

In my case I use

```java
button.setColorFilter(0x77000000,Mode.SRC_ATOP);
```

doing this you will change the Colour but if you pressed the back button , you will find it stuck on the colour you changed ,to return things as they were you can either use

```java
setColorFilter(null);
```

or

```java
setColorfilter(Color.TRANSPARENT,Mode.SRC_ATOP)
```

I use ``setColorFilter(null)`` and this does the trick for me.

I hope that helped :)
