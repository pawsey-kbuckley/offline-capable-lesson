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
however there are still one or two places within the current Lesson
styles template, where navigation breaks when the Lesson is being 
viewed offline, even though the content that those navigational
links would take the reader to, are available for rendering within
the Lesson itself.

## How many navigational links remain broken, when viewed offline ?

In the previous episode we saw how to modify links rendered as a
"raw directory path" in a lesson's upper navigation bar, so that
they can contain a default filename target, if required.

If we now take a look at all of the navigational links within a
rendered Lesson's HTML, we will observe the following set of 
targets that don't "look right"

### Main title bar

Navigational links in the main title bar, and their targets:

* Improve this page
    * `file:///edit//_episodes/01-introduction.md`

so, for all of those, bar the direct link to the Lesson's repository
within GitHub itself, our previous modifications, now applied by the
Carpentries to the style template, allow for a working offline navigation
of a rendered Lesson, whilst the link to the Lesson's remote repository
should not be expected to take an offline reader anywhere within the
Lesson anyway, however, the fact that the link contains a `file:///` URI
does suggest that it could be pointing to something local to the lesson.

### Footer Menus

The are three seperate parts in the footer, on the left-hand side,

* Licensed under CC-BY 4.0 
    * https://creativecommons.org/licenses/by/4.0/

and then the two Carpentry links,

* by The Carpentries link
    * https://carpentries.org/

* by Software Carpentry Foundation link
    * https://software-carpentry.org/

all three of which are clearly links to remote websites, then, centered
and underneath the left and right-hand sides,

* Using The Carpentries style
    * https://github.com/carpentries/styles/
* version M.N.P
    * https://github.com/carpentries/styles/releases/tag/vM.M.P

which, again, are clearly links to remote websites, and then, the right-hand
side link list,

* Edit on GitHub
    * `file:///edit//_episodes/01-introduction.md`
* Contributing
    * `file:///blob/CONTRIBUTING.md`
* Source
    * `file:///`
* Cite
    * `file:///blob/CITATION`
* Contact
    * mailto:address@somedomain.tld

where the "Edit on GitHub" link, duplicating, as it does, the "Improve
this page" link from the main menu bar; the link to the remote source
repository, and the contact email address should all be expected to be
remote.

Clearly though, those links are not remote: so what's happening here?

The answer lies in yet another template file, this time `_includes/lesson_footer.html`
where we see that the source for the links is constructed from a template
that contains the site variable `repo_url`, for example.

```
{% raw %}
<a href="{{ repo_url }}/blob/{{ source_branch }}/CONTRIBUTING.md" data-checker-ignore>Contributing</a>
{%endraw %}
```

Knowing that, we can see that, because we have rendered this lesson
offline, our `repo_url` variable gets replaced by `file:///` which
the reader may find a little confusing.

We will return to this issue later.

However, in the case of the "Contributing" and "Cite" link targets,
the question is not one of needing to find a way to point to a remote
resource but one of, "do they really need to point back to files in
the remote repository"?

If we take another look at the directory listing that the broken behvaiour
of the current template exposed to us in Episode one, we can even see that
three files `AUTHORS`, `CITATION` and `CONTRIBUTING.md` are even copied 
into the `_site` directory for us.

Considering that within a locally checked-out copy of a Lesson's repository,
we already have a copy of the `CONTRIBUTING.md` file and a copy of the plain
text `CITATION` files, both containing content that the links are targetting,
is there any reason that we cannot use our checked-out copies of those files
to render their content into the Lesson?

In the case of `CONTRIBUTING.md`, the answer is clearly, "No", after all,
we already use the following Markdown files to create local-to-the-lesson
content, 

* CONDUCT.md
    * top_level//_site/Code_of_Conduct.html
* LICENSE.md
    * top_level/_site/LICENSE.html
* reference.md
    * top_level/_site/reference.html
* setup.md
    * top_level/_site/setup.html

so why not operate on `CONTRIBUTING.md` in the same manner, so as to have it
appear at

* Contributing
    * top_level/_site/contributing.html

Similary, in the case of the plain text `CITATION` file, if that were
turned into a Markdown file, so `CITATION.md`, it could be rendered
so as to appear as

* Cite
    * top_level/_site/citation.html

It might even be possible to make a case for the contents of the,
as currently maintained, plain text `AUTHORS` file to be treated
in the same way and so rendered somewhere with the lesson (perhaps
within the `Extras` menu?) but, as that is not a link target within the
current Lesson styles template, we'll leave that as just a suggestion
for now.

## Rendering existing, but unused, local copy content into the Lesson

If we use the `CODE_OF_CONDUCT.md` file as an example of how such a file
gets rendered into the Lesson we see this stanza at the top of the file

```
$ head -5 CODE_OF_CONDUCT.md
---{% raw %}
---
layout: page
title: "Contributor Code of Conduct"
---
As contributors and maintainers of this project,
...
{%endraw %}---
```

after which comes the content for the Setup page itself.

Similarly in the template `_includes/navbar.html` we see how
the source translates our local files into links:

```
{% raw %}
<a href="{{ relative_root_path }}{% link CODE_OF_CONDUCT.md %}">Code of Conduct</a>
{%endraw %}
```

It should thus be a simple case of adding the following stanza

```
---{% raw %}
layout: page
title: Contributing
{%endraw %}---
```

to the top of the exiting `CONTRIBUTING.md` file, for it to
become available, within the Lesson, as

* top_level/_site/CONTRIBUTING.html

once we have edited the  `_includes/lesson-footer.html` to have the
following instead of the code to link to the external repo:

{% raw %}
<a href="{{ relative_root_path }}{% link CONTRIBUTING.md %}">Contributing</a>
{%endraw %}

Similarly, if simply replace the plain text file `CITATION` with
a `CITATION.md` file that has the following stanza at the top

```
---{% raw %}
layout: page
title: How to cite this lesson
{%endraw %}---
```

then we would have the content of that file available, within the Lesson, as

* top_level/_site/CITATION.html

Once those link targets become available, we can simply alter this file

* _includes/lesson_footer.html

and have our local copies of the repository's content, for the `Contributing`
and `Cite` links, become part of the Lesson itself.

It really is that simple.

{% include links.md %}

