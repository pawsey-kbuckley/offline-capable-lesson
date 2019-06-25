---
title: "Re/De-branding the lessons"
teaching: 0
exercises: 0
questions:
- "Is there a way to stop people confusing our own lessons with official
Carpentries material?"
- "Is there a way in which we can give the Carpentri credi
where it's due?"
objectives:
- "Become aware of how to modify the Lesson styles template so that it 
contains your own branding"
- "Become aware of how to modify the Lesson styles template so that it 
gives credit and complies with the original licence"
keypoints:
- "Some people would like to give material not covered within the official
Software Carpentry syllabus but give it in a way that has the students taking
such a lesson using resources in a format that they will be familar with."
- "Software Carpentry's approach to presenting Lesson content lends itself
to a simple Re/De-branding"
---

With the various Carpentries gaining a lot of exposure as a way to get 
the basics of research practice, indeed good research practice, across,
some people would like to give unofficial lessons, but do so in way that
doesn't imply that the lessons are official whilst having the students
taking their lessons do so in an environment that they will have become
comfortable with, as a result of previously taking official
Software/Data/Library Carpentry lessons, or in an environment that prepares
them for what they will come across should they take official
Software/Data/Library Carpentry lessons.

Similarly, some people would like to, or may be under pressure from 
other people to, ensure that their brand is "put out there" for the
world to see.

Along with its distinctive style, the two places where students taking
official Software/Data/Library Carpentry material are reminded of the
fact that the lesson is "official' are the logo in the main menu bar,
and the licensing statements in the footer.

In this episode, we'll look at a couple of small changes we can make to 
the current Lesson styles template that allows us to present both our
own branding on a lesson whilst ensuring that the Software/Data/Library
Carpentries get the credit that they so richly deserve.

### Making sure people know it's a derived work

The easiest change is simply point out that the lesson has been 
derived from work that is copyright of the relevant Carpentry,
and that merely requires the adddition of a string containing
the words

```
Derived from work that is<br/>&nbsp;
```

to

- _includes/lesson_footer.html

### Removing the Carpentry logo

Rather than remove the area that contains the various logos, we decided 
to replace each logo with a transparent image that maintains the indentation 
of the menu items, as they would appear in the official lesson rendering.

The transparent logos should go alongside the official logos

```
$ ls assets/img/
$ ls assets/img/
cp-logo-blue.svg   lc-icon-black.png  lc-logo-black.svg  swc-logo-blue.svg
dc-icon-black.svg  lc-icon-black.svg  swc-icon-blue.svg  swc-logo-white.png
dc-logo-black.svg  lc-logo-black.png  swc-logo-blue.png  swc-logo-white.svg
```

and are named

```
cp-icon-transp.svg dc-icon-transp.svg lc-icon-transp.svg swc-icon-transp.svg
```

which allows for a simple change to the filenames in 

- _includes/navbar.html

### Adding our own branding

Rather than disrupt the look and feel of the existing lessons, we
merely placed our branding into another navigation bar at the top
of each page, having again placed the extra image required into
the `assets/img` directory, as this allows for our branding area
to collapse in the same way as the main navigational menu.

The additional code is currently placed in the file

- _includes/navbar.html

and if we follow the naming convention of the CSS class that defines
the navigation bar, then we can even re-use the block of CSS code in 

- assets/css/lesson.scss

to apply a similar style to our extra "branding" bar.

The final modification is to add the URL of our institution as a variable
in

- _config.yml

so that we can link to it, from underneath our logo, although it's not
clear that one's institution's home page is the place that people will
want to be taken to.


