---
title: "Making more use of existing content"
teaching: 0
exercises: 0
questions:
- "Can we reduce the number of external links even further, in order
to make the lessons even more self-contained?"
- "What content does a checked-out lesson have that isn't currently
rendered into the lesson?"
- "Could we make more use of remote content that we have local copies of?"
objectives:
- "Become aware of files in a locally checked-out Lesson repo that
aren't used when creating the Lesson."
- "Become aware of how to use that local content to make the rendered
lesson even more self-contained."
keypoints:
- "Software Capentry's current build system sees files maintaned within
the Lesson repository that are not rendered into the Lesson."
- "Software Capentry's current navigational link targets point to
remote files in the Lesson's repository even when the same content,
held within those files, in available in a local copy of the repository."
- "Small modifcations, to the current Lesson style's templates
and auxilliary files would allow for more content, already 
maintained within a Lesson's repository, to be rendered directly
as part of the lesson."
---

The previous  episode took us almost as far as we can get towards
producing a "Fully Offline Capble" rendering of a Lesson's content,
however there are still one or two places with the current Lesson
styles template, where navigation breaks when the Lesson is being 
viewed offline, even though the content that those navigational
links would take the reader to, are available for rendering within
the Lesson itself.

## How many navigational links remain broken, when viewed offline ?

In the previous episode we saw how to modify all of a rendered
lesson's "raw directory path" links, so that they can contain a
deafult filename target, if required.

If we now take a look at all of the navigational links within a
rendered Lesson's HTML, we see the following set of targets

### Main title bar

The navigational links in the main title bar, and their targets, are:

* Home
    * top_level/_site/
* Code of Conduct
    * top_level/conduct/_site/
* Setup
    * top_level/setup/_site/
* Episode sub-menu items
    * top_level/_site/00-episode-dir/
* Extras sub-menu items
    * top_level/_site/reference/
    * top_level/_site/about/
    * top_level/_site/discuss/
    * top_level/_site/figures/
    * top_level/_site/guide/
* Licence
    * top_level/_site/license/
* Improve this page
    * RemoteRepoURI/edit/path

so, for all of those, bar the direct link to the Lesson's repository
within GitHub itself, our previous modifications allow for a working
offline navigation of a rendered Lesson, whilst the link to the 
Lesson's remote repository should not be expected to take an offline
reader anywhere within the Lesson anyway.

Nothing needed here.

### Up, Prev and Next Episode, and Lesson Title, Links 

These are the link targets underneath the navigational arrows to the
relavent previous or next Episode, or up to the home page in the case
of the first Episode's "previous" target, and underneath the Lesson
title that appears above the Episode title and so will be either

* The Prev or Next Episode
    * top_level/_site/00-episode-dir/
* The home page
    * top_level/_site/

and again, our previous modifications allow for a working offline
navigation to those targets.

So again, nothing needed here.

### Footer Menus

The are two seperate parts in the footer, on the left-hand side,

* Copyright (Software Carpentry Foundation) link
    * https://software-carpentry.org/

which is clearly a link to remote website, whilst, on the right-hand side,

* Edit on GitHub
    * RemoteRepoURI/edit/path
* Contributing
    * RemoteRepoURI/blob/gh-pages/CONTRIBUTING.md
* Source
    * RemoteRepoURI/
* Cite
    * RemoteRepoURI/eblob/gh-pages/CITATION
* Contact
    * mailto:address

where the "Edit on GitHub" link, duplicating, as it does, the "Improve
this page" link from the main menu bar; the link to the remote source
repository, and the contact email address should all be expected to be
remote.

However, in the case of the "Contributing" and "Cite" link targets,
do they need to point back to files in the remote repository?

Considering that within a locally checked-out copy of a Lesson's repository,
we already have a copy of the `CONTRIBUTING.md` file and a copy of the plain
text `CITATION` files that the links are targetting, is there any reason that
we cannot use our checked-out copies of those files to render their content
into the Lesson?

In the case of `CONTRIBUTING.md`, the answer is clearly, "No", after all,
we already use the following Markdown files to create local-to-the-lesson
content, 

* CONDUCT.md
    * top_level/conduct/_site/
* LICENSE.md
    * top_level/_site/license/
* reference.md
    * top_level/_site/reference/
* setup.md
    * top_level/setup/_site/

so why not operate on `CONTRIBUTING.md` in the same manner, so as to have it
appear at

* Contributing
    * top_level/_site/contributing/

Similary, in the case of the plain text `CITATION` file, if that were
turned into a Markdown file, so `CITATION.md`, it could be rendered
so as to appear as

* Cite
    * top_level/_site/citation/

It might even be possible to make a case for the contents of the,
as currently maintained, plain text `AUTHORS` file to be treated
in the same way and so rendered somewhere with the lesson (perhaps
within the `Extras` menu?) but, as that is not a link target within the
current Lesson styles template, we'll leave that as just a suggestion
for now.

## Rendering existing, but unused, local copy content into the Lesson

If we use the `setup.md` file as an example of how such a file gets
rendered into the Lesson we see this stanza at the top of the file

```
$ head -5 setup.md
---{% raw %}
layout: page
title: Setup
permalink: /setup/
{%endraw %}---
```

after which comes the content for the Setup page itself.

It should thus be a simple case of adding the following stanza

```
---{% raw %}
layout: page
title: Contributing
permalink: /contributing/
{%endraw %}---
```

to the top of the exiting `CONTRIBUTING.md` file, for it to
become available, within the Lesson, as

* top_level/_site/contributing/

Similarly, if simply replace the plain text file `CITATION` with
a `CITATION.md` file that has the following stanza at the top

```
---{% raw %}
layout: page
title: How to cite this lesson
permalink: /citation/
{%endraw %}---
```

then we would have the content of that file available, within the Lesson, as

* top_level/_site/citation/

Once those link targets become available, we can simply alter this file

* _includes/lesson_footer.html

and have our local copies of the repository's content, for the `Contributing`
and `Cite` links, become part of the Lesson itself.

It really is that simple.

{% include links.md %}

