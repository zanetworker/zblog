---
title:  "SSH Configuration: The Easy Way"
date:   2016-04-24 15:04:23
categories: [technical]
tags: [Linux]
---

So having problems using SSH to log into your servers? don't fret, I am writing this to make your life easier.

There are two ways to use SSH, one using "PasswordAuthentication" and one using ssh-generated public and private Keys. The keyed methods is the one you will end up using most of the time. Now. here is how to go about it:
<!--more-->

<strong>1. Install openssh-server</strong>

```bash
sudo apt-get install openssh-server
```
<strong>2. Backup your <em>ssh </em>configuration </strong>

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.factory-defaults
sudo chmod a-w /etc/ssh/sshd_config.factory-defaults
```
<strong>3. On the client create <em>.ssh </em> directory (if it does not exists already) and generate public and private RSA keys</strong>

```
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa

Generating public/private rsa key pair.
Enter file in which to save the key (/home/b/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/b/.ssh/id_rsa.
Your public key has been saved in /home/b/.ssh/id_rsa.pub.</pre>
Congratulations! you now have SSH keys ready for use.
```

<strong>4. Transfer your public key to the server, I will assume that you still have password authentication enabled there. </strong>

```
ssh-copy-id <username>@<host>
```

Type the password, and kaboom! you have SSH  keyed access to your server. If you have already disabled password authentication, use the following to add your generated keys to the list of authorized keys

```
cp authorized_keys authorized_keys_Backup
cat id_rsa.pub >> authorized_keys
```

<strong> 5. Disable password authentication from the server (optional) </strong>

Using your favorite editor, edit ```/etc/sshd_config``` by adding  ```PasswordAuthentication no```

 

Have fun ssh-ing!!

 
