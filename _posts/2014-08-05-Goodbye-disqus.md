---
layout: post
title: Goodbye Disqus
---

Hi folks,

I just trash [Disqus][3] from my website cause of some disgusting issues.

- A bunch of `js` imported
- A bunch of ajax requests everywhere
- A big performance issue on page rendering, specially on mobile
- A urls migration wizard that just doesn't work

I have tested [Isso][1], a Python commentary mechanize. But still young and I fail to import all comments from Disqus.
I make some try and thinking about [Discourse][2] integration. It's a very good software. All crappy *phpbb* are just a shame compare to Discourse. But still a little heavy for a blog commentary system like mine.

So guys, I just remove Disqus and leave my email at the end of each article.

My requirements are the followings:

- Lightweight commentary system
- Less maintenance is better
- If possible no third party service like Disqus
- Or a more open third party, like *Reddit*, *HN*, etc...


**Don't hesitate to leave me an email**, I'll update article if you do some work that must be show on or if you have some great idea.

Let's consider that article are not static and can evolved when people read it or want to add content.


[1]: https://github.com/posativ/isso
[2]: https://github.com/discourse/discourse
[3]: https://disqus.com/
