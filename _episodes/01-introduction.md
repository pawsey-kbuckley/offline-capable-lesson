---
title: "Introduction"
teaching: 0
exercises: 0
questions:
- "What issues does a Lesson being \"Offline Capable\" address?"
- "What content does a webserver serve when presented with a
bare directory path URI?"
- "What URIs does SWC's standard rendering of a lesson's content
produce as link targets?"
objectives:
- "Become aware of browsing local web pages through `file:///` URIs."
- "Become aware that SWC's standard rendering of a lesson's content,
produces bare directory path URIs."
keypoints:
- "Web browsers can display local files, as well as content delivered
by webervers."
- "Webservers, when given a bare directory path URI, are able to
automatically append the name of a file, and serve that
content to the browser."
- "Webservers, when given a URI comprising a directory path and
the name of the automatically appened file will present the same
content to the browser."
- "SWC's standard rendering of a lesson's content produces
bare directory path URIs."
---

This episode looks at the problem, as perceived by the authors,
that the rendering of a fully offline capable lesson would solve.

One of the plus points about the Software Carpentry's lesson content,
as produced with the current rendering approach, is that everything
needed to use the lesson is contained within the `_site` directory.

One could imagine that lessons might even be distributed, within
teaching arenas where connectivity is not guaranteed, simply by
passing around some media that merely contained the `_site` directory.

## Browsing lesson content with `file:///` URIs

### The Lesson's home page

The first impression, of the rendering of a Lesson's content following a
`make site`, when viewed via a `file:///` URI, probably doesn't give much
cause for concern, and indeed, since the first version of this lesson was 
created, some of its ideas have been taken on board by the Carpentries,
however, as we'll see, the current styles still don't provide as seamless
an experience as one might wish for. 

<img style="border:2px solid black" src="{{ page.root }}/fig/foc-browse_home_file_uri.png" alt="Home Page as file URI" />

Let's see what happens when we click on the link to Episode 1, Introduction.

### The target of the Episode 1 links, as currently rendered 

Yes, the plural "links" in this section's title is correct.
As well as the link in the Schedule, there's also one in the Episodes menu.

<img style="border:2px solid black" src="{{ page.root }}/fig/foc-browse_lesson1_index_file.png" alt="Lesson1 Page as file URI" />

Compared to the older revisions of styles, this is much better,
we are presented with the content of the Episode page, and the
images representing the navigation buttons are displayed.

But what happens when we click on one of those, so as to return
to the Lesson's home page?

### The target of the link under the Episode 1 page's up-arrow image, as currently rendered 

<img style="border:2px solid black" src="{{ page.root }}/fig/foc-click_lesson1_link.png" alt="Lesson1 link destination" />

Oh dear!

Rather than presenting us with Lesson's home page, the link underneath
the Episode's up-arrow image merely takes us to a directory index of the 
top-level directory for the Lesson and shows us a file listing for it?

An inspection of the URI in the address field does show
a bare directory path, so, in all fairness to the browser, it has
actually presented to us what we *requested*, even if what we actually
*wanted* was the Lesson's home-page's actual content, as contained in 
the `index.html` file that we can see.

Let's  see what happens when we click on the link to `index.html`

### The target of the Lesson's directory's index.html link, as currently rendered 

<img style="border:2px solid black" src="{{ page.root }}/fig/foc-browse_home_file_uri.png" alt="Home Page as file URI" />

Admittedly, it has taken us two clicks to get here, but we do now
have the Lesson's home-page's actual content presented to us.

### So what's going on? Isn't this the same content that GitHub's gh-pages branch uses?
 
Indeed, it is the same content as presented by the GitHub webserver
from a Lesson's `gh-pages` branch and furthermore, the `_site` directory
is also exactly the same directory hierachy of content that Jekyll's own
minimal webserver would present to us, if started locally, and both of
those provide a seamless navigation around the Lesson.

In order to understand why our file-based browsing of a Software
Carpentry Lesson's content is currently a sub-optimal experience,
we need to take a look at how webservers respond to various requests
to present data back to the browser. 

## Webserver 101

Clearly, as we have seen above, the Software Carpentry's current rendering
of Lesson content results in link targets that merely contain path components
as far as the directory containing the file containing the actual content.

### What we see from the browser

Perhaps the ideal example to use to highlight what happens with Software
Carpentry Lesson content as presented by a webserver would be to use a
browser to view the rendered contents of the previous version of this 
`lesson-example` repository, as presented by the webserver at `github.io`

[https://vuw-ecs-kevin.github.io/offline-capable-lesson/]([https://vuw-ecs-kevin.github.io/offline-capable-lesson/)

What we may find surprising, given that we know that Software Carpentry
home page content is actually stored in a file named `index.html`, is that the
URIs we see in our browser's address bar is merely the path to the directory,
not to the file itself, and yet we are not getting the directory content
index that our file-based browsing of the same content produced.

Clearly then, our file-based browsing of a directory has no way to
make a choice of what to display, and so displays an index of all
the files and sub-directories, whereas a webserver, or rather the
webserver at `github.io`, appears to be deciding that it will present
the file named `index.html` to us.

(Aside to develop: back in the days of 8.3 filenames, `index.htm`
might have been the file presented.)

### What the webserver does when our browser makes requests of it

As you might imagine, the name of the file, containing the content
that a webserver will present back when it receives a request for 
a directory is usually configurable and indeed, in the documentation
for this particular configuration of the Apache (**defn?**) webserver,

[https://httpd.apache.org/docs/2.4/mod/mod_dir.html#directoryindex](https://httpd.apache.org/docs/2.4/mod/mod_dir.html#directoryindex)

we get a very concise explanation of what's going on, and we see that 
the concept is even referred to as the `DirectoryIndex Directive`.

```
DirectoryIndex Directive

Description:	List of resources to look for when the client requests a directory
Syntax:		DirectoryIndex disabled | local-url [local-url] ...
Default:	DirectoryIndex index.html
Context:	server config, virtual host, directory, .htaccess
Override:	Indexes
Status:		Base
Module:		mod_dir


The DirectoryIndex directive sets the list of resources to look for,
when the client requests an index of the directory by specifying a / at
the end of the directory name.

Local-url is the (%-encoded) URL of a document on the server relative to
the requested directory; it is usually the name of a file in the directory.

Several URLs may be given, in which case the server will return the first
one that it finds. If none of the resources exist and the Indexes option is
set, the server will generate its own listing of the directory.
```

So now we know what's happening when we access the content via a webserver:
the webserver is automatically presenting the content from a default
filename `index.html`, as though it had been appended onto our URI.

In other words, what we are actually being presented with, when we view
the previosus work's rendered content at `github.io`, are the files

[https://vuw-ecs-kevin.github.io/offline-capable-lesson/index.html]([https://vuw-ecs-kevin.github.io/offline-capable-lesson/index.html)

and not the directory index, despite the fact that our requests to the
webserver don't explcityly contain the file names.

## So the answer to `file:///` navigation problems is: just append `index.html` ?

When it's written like that, youl'd have to say that it does seem simple
doesn't it, however as you may already have discovered, on occasions 
where you have suggested to someone that it's **just** a matter of doing
something, the size of a "just" can vary widely, depending on the
beholder!

Indeed, one argument, for not **just** appending `index.html` throughout
the existing canon of Software Carpentry Lesson material, is that there
are a lot of links within that existing body of work and few if anyone
will want go through it all, **just** adding `index.html` in the required
places.

As we'll see in the next episode though, Software Carpentry's rendering
methodology, built as it is upon templates and lesson-wide variables,
so as to make Lesson content creation as simple as possible, is not
only the perfect vehicle for automating the addition of the `index.html`
strings into the Lesson content, but also means that we can do so in
a way that preserves the existing behaviour where we wish, thereby
reducing the size of the "just".

{% include links.md %}

