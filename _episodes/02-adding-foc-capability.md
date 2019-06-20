---
title: "Adding fully offline capability"
teaching: 0
exercises: 0
questions:
- "How simple would it be to add \"Fully Offline\" capablity to the current style?"
- "What changes to the current style's files would be required?"
- "What changes to a typical lesson's content would be required?"
- "Would these changes prevent the existing behaviour from being
produced if required?"
objectives:
- "Become aware of `_config.yml` and the way in which the lesson style
makes use of \"global variables\" and templates."
- "Become aware of of the small set of changes, to the current style's
files, that are needed to add \"Fully Offline\" capablity."
- "Become aware of the additions to lesson content that are needed."
- "Become aware that the existing behavious can be preserved."
keypoints:
- "A small set of changes to the existing style templates would allow
for a \"Fully Offline Capble\" rendering of a lesson's content."
- "Using the modifed style templates with existing lesson content 
will see that rendered as it was with the existing style templates."
- "The existing behaviour can be preserved within a lesson that uses
the modifed style templates."
---

This episode looks at how the style templates use \"global variables\",
that is, variables that can be referenced within any source file for 
a Lesson template, for any Episode's, or for auxilliary, content, and
then shows how to use the power of Software Carpentry's rendering
methodology, and add a variable to provides the filename that is 
currently missing from the link targets of current Lessons that are
browsed via `file:///` URIS.

Many of the \"global variables\" that can be used with an SWC Lesson's
template, Episodes, or auxilliary source files, are defined in the
top-level file `_config.yml`, and the following snippet of such a
file shows how these vaiables are defined:

```
#------------------------------------------------------------
# Values for this lesson.
#------------------------------------------------------------

# Which carpentry is this ("swc", "dc", or "lc")?
carpentry: "swc"

# Overall title for pages.
title: "Offline Capable Lessons"

...

# Sites.
amy_site: "https://amy.software-carpentry.org/workshops"
dc_site: "http://datacarpentry.org"
swc_github: "https://github.com/swcarpentry"
swc_site: "https://software-carpentry.org"

...

# Set the default layout for things in the episodes collection.
defaults:
  - values:
      root: ..
  - scope:
      path: ""
      type: episodes
    values:
      layout: episode
...
```

whilst the top part of the source file for this Episode, looks like this

```
title: "Adding fully offline capability"
teaching: 0
exercises: 0
questions:
- "How simple would it be to add \"Fully Offline\" capablity to the current style?"
...
```

Note that a variable called `title` has been defined in two places, once
in the lesson's `_config.yml` file and once in the episode's souce file.

In order to see how these variables can be used, we need to 
take a look at a section from one of the standard Lesson style's
template files, in this case `_includes\main_title.html`

```{% raw %}
{% comment %}
  Main title for lesson pages.
{% endcomment %}
<h1 class="maintitle"><a href="{{ page.root }}/">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
{% endraw %}```

The first three lines are simply a comment block that gets ignored when
the file is used to render the Lesson's content, whilst the last line
contains a mixture of HTML markup, ome strings within pairs of double
braces - `page.root`, `site.title` and `page.title` - and  a block of
`if-then-else-endif` logic in which the clauses of the statement are
contained within blocks denoted by `brace-percent` pairs.

If we now allow the rendering of this Lesson to present to us what
the above template puts into the stream of content it sends to the
browser we see

```
<h1 class="maintitle"><a href="{{ page.root }}/">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
```

that the instance of `site.title` with the double brace pair has been
replaced with the string from the lesson's (think `site`) `_config.yml`
value for `title`, which the `page.title` with the double brace pair
has been replaced with the string from this episode's (think `page`) 
value for `title`.

Note also that the `page.root` for this episode has been replaced by
the string `../` in the rendered output, even though a `root` variable
is only defined in `_config.yml` and not this episode's source file.

We can now see that double-brace pair variables that are qualified
with a prepended `site.` refer to variables defined in `_config.yml`,
whilst variables that are qualified with a prepended `page`, refer
to variables defined in the episode then being rendered, unless no
such variable exists in which case the `page` values inherits the
value defined as the `site` level.

We can also see that when we click on the link to the Lesson's title ,
we are only going to be sent to a target that is the directory above
the one that this episode's HTMl file resides in, ie the `href="../"`,
whereas we know that we really want `href="../index.html"`

We might consider the addition of our known default filename into the
style's template file, `_includes\main_title.html` as follows

```{% raw %}
{% comment %}
  Main title for lesson pages.
{% endcomment %}
<h1 class="maintitle"><a href="{{ page.root }}/index.html">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
{% endraw %}```

which would then render

```
<h1 class="maintitle"><a href="{{ page.root }}/index.html">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
```

which is what's needed for navigating through ~lesson content that is being
browsed offline, as well as being a link target that we know will allow us
to navgiate through lesson content that is being presented by a webserver.

### So that's "just" what we need then?

No: not quite.

Firstly, the file `_includes\main_title.html` only handles the link in the
main title bar, and not the various links in the other places where navigation
links are presented.

Secondly, the hard-coding of the filename into the template would 

   1. Force everyone using such a mofified template to use that filename,
      and there may be reasons where that wouldn't be the desired name, and
   2. Force them to render their Lessons with a filename appearing in them
      even when presented via webservers that "do the right thing" as regards
      a bare directory name.

Let's address that second issue first.

Rather than hard-code the filename into parts of the template, we can
define a Lesson/site-wide variable, by adding one into the `_config.ywml`
and then refering to it in the template files, so we might now have,
in `_config.ywml`


```
#------------------------------------------------------------
# Values for this lesson.
#------------------------------------------------------------

# Which carpentry is this ("swc", "dc", or "lc")?
carpentry: "swc"

# Overall title for pages.
title: "Offline Capable Lessons"

# Filename matching webserver default, for offline browsing
index: "index.html"
...
```

and in `_includes\main_title.html`,


```{% raw %}
{% comment %}
  Main title for lesson pages.
{% endcomment %}
<h1 class="maintitle"><a href="{{ page.root }}/{{ site.index }}">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
{% endraw %}```

As modified above, that would render the main title bar as

```
<h1 class="maintitle"><a href="{{ page.root }}/index.html">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
```

but note what happens if we chose to define the value of `site.index` to
be empty, by specifying it in `_config.ywml` as follows


```
#------------------------------------------------------------
# Values for this lesson.
#------------------------------------------------------------

# Which carpentry is this ("swc", "dc", or "lc")?
carpentry: "swc"

# Overall title for pages.
title: "Offline Capable Lessons"

# Filename matching webserver default, for offline browsing
index: ""
...
```

Now our modified template generates the following HTML

```
<h1 class="maintitle"><a href="{{ page.root }}/">{{ site.title }}</a>{% if page.title %}: {{ page.title }}{% endif %}</h1>
```

which, a bare directory path, is exactly what the current template would
generate.

This would appear to be a very good thing, in that someone who writes their
Lesson content using the modified template could do most of their work with
an empty definition of the `site.index` variable, and see all previous
behaviours - renderings of GitHub's `gh-pages` branches, and when rendered
via a `make serve` in a local source tree - maintained, whilst allowing for
the creation of a "fully offline capable" rendering, merely by altering one
line in `_config.yml`, invoking a `make site`, and then copying the `_site`
directory as a standalone resource.

### So just where else, within the lesson style's template, do we need to make changes ?

Let's now address the first of the two questions we raised above, namely, 
"the various links in the other places where navigation links are presented"..

Because Software Carpentry's approach to generating content makes such a big
use of templates for common sections within each episode, or auxiliary content,
it may come as a pleasant surprise to find out just how few files, and in just
how few places within those files, require the introduction of the `site.index`
variable.

The full list of files, along with

   - _config.yml
   - _includes/main_title.html

which we saw edited above, is just

   - _includes/all_keypoints.html
   - _includes/episode_navbar.html
   - _includes/navbar.html
   - _includes/syllabus.html

and the actual changes, (smply inserting `site.index`) just

```
    _config.yml                    1 change
    _includes/all_keypoints.html   1 change
    _includes/episode_navbar.html  5 changes
    _includes/main_title.html      1 change
    _includes/navbar.html         10 changes
    _includes/syllabus.html        2 changes
```
and of course, once those changes were in the official style's template
files, no-one would need to make that set of changes ever again.

### But wait: there's more !

Even though the changes described above are enough to allow for
an "offline" navigation through the Lesson's episodes, there 
are still one or two places where we can enhance the "offline"
experience, and present to the reader auxiliary content that,
although one might expect it be "local to the Lesson", is
currently only available within the Lesson's remote, so at 
GitHub, repository.

In the next episode though, we'll address that too.

{% include links.md %}
 
