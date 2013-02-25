---
layout: post
title: Blogging with Jekyll + GitHub Pages
tags: WMATA
comments: true
---

Jekyll, a static blog generator built in Ruby, is a powerful tool and has been immensely useful in creating this website. If you don't want to support a heavy CMS, are fond of the GitHub workflow, and are willing to delegate the responsibility of serving dynamic content to third parties, then Jeykll is a tool to definitely look into.

<!-- more -->

Here are some of the situations where there advantages to using Jekyll:

<ul>
	<li><strong>You don't need a database in the backend.</strong>

		<p>Blog posts are written in markdown, which is processed by jekyll into HTML. This content that you are reading is static-- no databasing required. For a site such a this, it's perfectly acceptable and reasonable to go without a database. In my opinion, a heavyweight CMS would be overkill. If you want something you can throw on GitHub pages and have it simply work, this is excellent. Jekyll has built-in support for comments by Disqus, Facebook, and others. It took me all of three minutes to setup commenting here.</p></li>

	<li><strong>You don't want to conform to a pre-installed CMS theme.</strong>

		<p>Out of the box, you don't get a theme. With jekyll, you create your own layouts, which are then applied to markdown files. Partials can be added into layouts via the <code>include</code> directive. Include is particularly helpful for content such as headers and footers. You're writing a component once, and using it everywhere. For my site, I wanted something homegrown; Jekyll let me do precisely that.</p></li>

	<li><strong>You want your blogging to complement a GitHub workflow.</strong>

		<p>I don't know why, but something about logging into my blog and typing some text into a WSIWYG content editor simply didn't interest me. Being able to pop up Sublime Text Editor, create a markdown file, and committing to my GitHub pages repo was more appealing. GitHub will automatically build the project for you, so there's no need to store Jekyll's generated site in your repository.</p></li>

</ul>

With the advantages come disadvantages. These are mostly limits to using GitHub Pages, but Jekyll's routing could be improved.

<ul>
	<li><strong>Limited routing options.</strong>

		<p>In the _config.yaml file, you can specify <code>permalink: pretty</code> to get a post URL format <code>/date/month/year/post-title/</code>. While convenient for readers, I would have preferred something more RESTful in addition to pretty permalinking, e.g., <code>/posts/id</code>. Id's are not associated with a post, so this isn't currently possible. Is it a dealbreaker? No. It'd be more of a 'nice-to-have' feature.</p></li>

	<li><strong>No plugins are permitted with GitHub pages.</strong></li>

		<p>You're going to have alternative hosting to make use of plugins. Since plugins are user-written, they could pose a potential security risk to GitHub. However, I haven't found a critical need for any plugins as of yet.</p>

</ul>