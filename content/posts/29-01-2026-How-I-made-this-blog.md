---
title: "How I Made This Blog"
date: 2026-01-29T13:46:12+02:00
summary: A simple guide on how I made this blog using Hugo
tags: ["projects", "web"]
draft: false
---


# Tech stack

 Because this is a *static* website, there is no need to use something too complex.
 Also, I wanted something that is easy to update, while also being fast and lightweight.

 **Hugo** checks all these. It is a lightweitght static website builder, perfect for tasks like personal blogs.
- More details on Hugo, [here](https://gohugo.io/about/)

The next challenge is hosting the website, and as I don't really have the money for an hosting service, I choose the Github free hosting service.
The website  may not be that SEO optimized, but that is of little interest as this is mainly used to showcase projects of mine. 

The hosting works by putting out the website code in a new repository named as your Github username.
Then you can access your website at `https://your-username.github.io`

 
# The setup


After setting up the [Hugo site](https://gohugo.io/getting-started/quick-start/#create-a-site), it's time to add content.

First start the Hugo server so that you can see in real time your changes:
- `hugo server -D`, *-D* for showing the drafts posts too.


Then, add a blog post.
This can be easily done using the command:
- `hugo new posts-directory/name-of-file.md`


When you are content (pun intended) with your new file, you can let Hugo build the site:
- `hugo`

This command will put all your website files into the `/public` folder.

## Integrating with Github

The flow is the same as tracking a usual repository with Github, but be aware of 2 things:

- Only track the `/public` folder. ***Don't*** track all your Hugo files like posts, themes, etc. 
- The name of the repository should be the same as your Github username.

And that's it.
Commit the files and you have a blog.

---
*T.*


