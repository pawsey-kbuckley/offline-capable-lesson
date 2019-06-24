---
title: "Some things to think about"
teaching: 0
exercises: 0
questions:
- "Is there any room for improvement in the offline viewing experience?"
- "Why do some links still have targets that might be confusing?"
objectives:
- "Become aware of the way in which some links become \"valid\" when
viewed via a webserver."
- "Become aware of we might use the Software Carpentry build process
to handle both online and offline viewing."
keypoints:
- "Some links in the offline rendered Lesson still have confusing targets
when the lesson is viewed offline, or in Jekyll's own server ."
- "Software Capentry's build process allows for alterations in the
content that gets rendered, based on global/lesson variables."
- "It may be possible to render an offilne resource so that all the
links have valid targets."
---

The previous  episode left us with just three links, on each of the pages,
episodes and auxilliary content, that would, when served by a webserver,
point, correctly, to remote resources but which when viewed offline, have
targets that may appear broken.  

This episode will merely discuss ways in which the Lesson styles'
templates might be altered, so as to address those issues.

## How many navigational links remain broken, when viewed offline ?

Same question as before, but now we are down to just three links

Main title bar
```
* Improve this page
    * RemoteRepoURI/edit/path
```


LHS Footer Menu
```
* Edit on GitHub
    * RemoteRepoURI/edit/path

* Source
    * RemoteRepoURI/
```
although two of those are duplicates, albeit under different names within
the page, to the same target.

It is instructive to look at what targets these links have under different
viewing regimes and in this instance we will consider the "Home Page" of
the Lesson, `index.html`.

Viewed as the GitHub repo's `gh-pages` branch at `github.io` 
```
https://github.com/vuw-ecs-kevin/offline-capable-lesson/edit/gh-pages/index.md

https://github.com/vuw-ecs-kevin/offline-capable-lesson/
```
Following a `make serve`
```
http://127.0.0.1:4000/edit/gh-pages/index.md

http://127.0.0.1:4000/
```
Following a `make site`
```
file:///edit/gh-pages/index.md

file:///
```

It should be pretty clear what is going on here but, to make things
obvious, we'll break it down

```
https://github.com/vuw-ecs-kevin/offline-capable-lesson/ + edit/gh-pages/index.md

https://github.com/vuw-ecs-kevin/offline-capable-lesson/


http://127.0.0.1:4000/                                   + edit/gh-pages/index.md

http://127.0.0.1:4000/


file:///                                                 + edit/gh-pages/index.md

file:///
```

Basicaly then, the targets of these links are only valid when the lesson
content has been rendered within an environment where the path to the
Lesson's GitHub repo can be determined.

The targets for these links would typically be determined by using the 
`jekyll-github-metatdata` gem, as described here

[https://github.com/jekyll/github-metadata](https://github.com/jekyll/github-metadata)

however that can require one to have access to the GitHub repo at the
time that one creates the rendered Lesson.

An alternative approach would be to check for the existence of the 
variables that `jekyll-github-metatdata` would populate and, if they
don't exist, the use a local variable, that can be added into the
Lesson's `_config.yml` to provide the desired link target.

Note that, just as with the definition of the variable to hold `index.html`,
so as to allow for offline navigation within a Lessons, if a Lesson's authors
decide not to populate a variable to hold the URL of the repository, then 
the rendered content will remain the same.

If we have defined two site variables `static_repo_url` and `static_def_bramch`
then we just need  to edit two files in the current Lesson styles template

- `_includes/lesson_footer.html`
- `_includes/navbar.html`

so that references to `site.github.repository_url` get wrapped in an
if-then-else clause similar to this one, from `_includes/lesson_footer.html`

```
{% raw %}---
    {% if site.github.repository_url %}
    <a href="{{ site.github.repository_url }}/">Source</a>
    {% else %}
    <a href="{{ site.static_repo_url }}/">Source</a>
    {% endif %}
---{%endraw %}
```

Note that this modification also fixes up the targets of the external links
when the lesson is viewed using Jekyll's own webserver, during a `make serve`.

Whilst there is a suggestion, in the current styles' `_config.yml` that says

```
{%raw%}
# Magic to make URLs resolve both locally and on GitHub.
# See https://help.github.com/articles/repository-metadata-on-github-pages/.
# Please don't change it: <USERNAME>/<PROJECT> is correct.
repository: <USERNAME>/<PROJECT>
{%endraw %}
```

be aware that there is no such thing as magic, well, not here anyway!

When the site is being viewed locally, via a `make serve` then the URIs of
the "Edit on GitHub" and "Source" links are merely as follows

- http://127.0.0.1:4000/edit//_episodes/04-things-think-about-one.md
- http://127.0.0.1:4000

as so don't point to the external repository.

{% include links.md %}

