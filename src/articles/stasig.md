<!--
title: Introducing Stasig
-->
STAtic SIte Generator
=====================

In the early days of the Internet, all *htm* pages were hand-crafted. While the
main content changed from page to page, certain sections like *header* and *footer*
had to remain the same on all pages. So these sections were necessarily copy-pasted
in all pages. It all worked fine except when you have to make any change to the *header*
or *footer*. That would require making changes in all html pages... a major
maintenance hazard.

Then came server side technologies like CGI, ASP, PHP, JSP which had the ability
to include a file contents into another file. This basically solved the
problem described above. Also, html pages were now created dynamically, on the fly.
This is all good, but now a basic Web Server is not enough. Depending on your
language of choice, you will need to find a web host which supports your preferred
server side tech.

Finding a host isn't really an issue. But what if we could create a site without
any Server Side technology and also without the maintenance hazard of the old days.
A curious mind would ask *WHY???*. Well, because we can!

The idea is to store all articles in separate files, but without the site's
repeating elements like header and footer. Each article file would contain some
meta-data at the start, followed by the content. An example of the file would be:

    <!--
    title: hello world
    -->
    <h1>Hello World</h1>

All such articles are stored in **articles** folder. We also have a **layout.html**
file which contains the basic template of the site. Something like this:

    <html>
        <head><title>{{title}}</title></head>
        <body>{{content}}</body>
    </html>

Now, a preprocessor program will iterate over all files in the **articles** folder
and combine it with **layout.html** to generate static html files. The generated
html files can then be uploaded to any web host(**Github** in my case) and the
site will be running without any server side processing.

Before we move forward, I would like to point out that such tools already exists:
+ DocPad
+ Jekyll
But I wanted something custom made, simple & sweet; which would help me maintain
my blog. And thats what **Stasig** is!

Of course, a blog feels incomplete without *Comments* section. This is where I got
initially stuck up. Without a database, where am I going to store the comments?
Looking over the Internet, found [Disqus](http://disqus.com/). Just go to their
site, register an account, copy paste a piece of JS on your website. And you have
a full featured comment system embedded... really MAGIC! They automatically
create comment threads based on the URL of your site. The funny thing is, I had
always seen *Disqus* on many other blogs, most notably, jQuery. But it never
really hit me that it is a comment handling service. Of course, the downside is,
the comments are on *their* system and not yours. But you get free spam-blocking
and oAuth logins, so its fair enough.

If you are reading this, it means my blog is online again, created with
HTML/CSS & Javascript *only*. The source code of Stasig has been hosted on
[Github](https://github.com/niloy/stasig). Feel free to check it out and
leave your comments.
