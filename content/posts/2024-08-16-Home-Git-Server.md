---
title: Git Home Server
summary: How I made a Git home server from an old PC I had 
date: 2024-08-16
---

## The hardware

A few years back, when Covid was all around, my brother was in need of a PC 
so that he would be able to attend the now online classes at his school 
(believe it or not, he didn't need a PC up until then, so he just didn't buy one).

So he buys a cheap refurbished Lenovo Thinkcentre PC with an SSD of 256 GB and 
a mediocre proccesor and calls it a day.

Fast forward to today and that PC is collecting dust in the attic.
So I get on the web and research what could I do with old hardware.
Most of the answers were about Home Servers, and as I play sometimes with all sorts of 
programming deeds I thougth a local Git server would be of great service, as sometimes I am just
too lazy to setup a private Github repo and push my code to it.
Also, it is objectively cool to own a git server, period.

## The setup

I decided to use Ubuntu Server for my OS and the installation was quick and painless,
and after an half an hour I was running a Ubuntu on my server. 
The next step was to create a new user called 'git', as the [official guide](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server) told me.

Then I created a bare repository using the newly created 'git' user and I all needed next was to 
create a SSH server so that people can access this repo.

An importan step was creating authentification keys, used to better secure the server but also for
a more easy access.

All you have to do is create a `~/.ssh/authorized_keys` directory on the server and then add the individual key of every PC you want to have access to the server. Generating a kew is also quite easy, just run `ssh-keygen -o` in the terminal then copy the contents of `cat ~/.ssh/id_rsa.pub`.

A great video that helped me it [this one](https://www.youtube.com/watch?v=ju9loeXNVW0).

And there you have it, the minimum of a git home server is done.
Now users can clone,commit and push their code to the server.

---

*T.*
