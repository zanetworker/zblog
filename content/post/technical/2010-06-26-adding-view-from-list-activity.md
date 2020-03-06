---
title:  "Adding a view within your ListActivity"
date:   2010-06-26 15:04:23
categories: [technical]
tags: [Android, Java]
---

I spent some time trying to find out how to put other views in the same place with a ListActivity and I figured out 2 different ways :

First,  you can create a new XML file that should contain a <em>listView</em> like this for example:


```xml
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent"
android:background="@color/black">

android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:src="@drawable/home" />

android:layout_width="fill_parent"
android:layout_height="fill_parent"
android:drawSelectorOnTop="false" />

```


Then, in your activity type the following


```java
setContentView("xmlFile");
```

Here for example, I would like  to put that image within my <em>ListActivity</em> .

Second, you can either use add a header or a footage view. Most importantly do this before setting any adapters ,


```java

ListView lv = getListView();
LayoutInflater inflater = getLayoutInflater();
ViewGroup header = (ViewGroup)inflater.inflate(R.layout.messageinfo, lv, false);
lv.addFooterView(header, null, false);
```


Or

```java

lv.addHeaderView(header,null,false) ;

```


I hope this helps : ) .
