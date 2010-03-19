---
title: "Migrated To Octopress"
---
Like [all](http://jonasboner.com/2009/01/07/blogging-like-a-hacker-using-git-and-jekyll.html) [cool](http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html) [kids](http://wiki.github.com/mojombo/jekyll/sites) blogging nowadays, as part of the attempt to rejuvinate the blog, I've switched to [Jekyll](http://github.com/mojombo/jekyll). Or, rather, the extended fork [Octopress](http://github.com/imathis/octopress).

As part of the migration I wanted to extract all the old posts from Wordpress into the proper markdown format used by Jekyll.

Jekyll includes a migration script for Wordpress, but this used a direct database connection to the Wordpress MySQL server to extract a minimum amount of information for each blog post. To avoid missing information that I later might want after having shut down the Wordpress server, I wanted to base the migration on a full XML export from Wordpress. This way I could go back and extract more information from the old posts, if, and when, needed.

The source for the Wordpress XML converter is available in the [blog branch of my fork of Octopress](http://github.com/melwin/octopress/blob/blog/source/_import/wordpress_xml_import.rb). To use, just run with the filename of the Wordpress XML export as the first argument.

It's my first publicly available Ruby program - so be careful. Improvements welcome!

/M
