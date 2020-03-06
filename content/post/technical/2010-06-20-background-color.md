---
title:  "Changing the Background Colour or Image of a ListActivity"
date:   2010-06-20 15:04:23
categories: [technical]
tags: [Android, Java]
---


I was stuck for like three hours trying to figure out how to change the background of a ListActivity because I have used an adapter with a layout for each item so when I change the background at the layout, only the item's background is changed, but at the end I figured that it was very easy here is how I did it :-

inside your ```onCreate()``` preferably before setting any adapters or putting any views, you insert the following line : ```getListView().setBackgroundResource("your Image")```; that did the trick .

Hope it helps!!
