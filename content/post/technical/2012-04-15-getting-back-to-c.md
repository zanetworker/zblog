---
title:  "Getting Back to C++ | Public, Private, and Protected (PPP) "
date:   2012-04-15 15:04:23
categories: [technical]
tags: [Coding, C++]
---


I have been away for a while now but every time I think I won’t need to lay a hand on C++ or any object oriented programming language I find my self mandated to do so. So I thought It would be helpful for people like me to look at this post if they got lost and forgot some of the basic concepts.

In this post, I will talk specifically about the difference between public, private and protected key words. To explain this I will use the House analogy. This House analogy I came across long time ago when I was looking for the difference between those keywords. Anyhow, let’s get into business.

For you to picture this correctly, think about three things that you have in your house,

- Your Post box
- Your Washing machine
- Your safe where you keep your money

Now let’s say that the house represent  s the Class that you are working on. Then what does those three items presented above represents ?

- Normally, the post box is accessible to Everyone, anyone can put post in that box and let’s assume that everyone can take post out of it (Just saying :D), this maps exactly  to the public keyword. Declaring a variable as public means that is is accessible from anywhere outside the class but withing the program.

- The washing machine in my house is accessible for me and my let’s say “future children” :D. In this case my Washing machine is protected  because no body from outside my house (the class) can access it except the children and friends.

- Finally the “Safe” who dares to enter my domain and access my safe ? No body is allowed to access my safe from outside my house. This is where the I declare the safe as private so by declaring the safe as private no body from outside the class can access it. I might some times give the key to my best friend to access the safe in case something bad happened to me so friends can have access if you allow them :D.

I know this example might not be perfectly valid compared to really life, but I hope at least that the message is conveyed.

Thanks for viewing.
