---
title: "Migrated To Octopress"
---
Like [all][] [cool][] [kids][] blogging nowadays, as part of the attempt to rejuvinate the blog, I've switched to [Jekyll][]. Or, rather, the extended fork [Octopress][].

 [all]: http://jonasboner.com/2009/01/07/blogging-like-a-hacker-using-git-and-jekyll.html
 [cool]: http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html
 [kids]: http://wiki.github.com/mojombo/jekyll/sites
 [Jekyll]: http://github.com/mojombo/jekyll
 [Octopress]: http://github.com/imathis/octopress

As part of the migration I wanted to extract all the old posts from Wordpress into the proper markdown format used by Jekyll.

Jekyll includes a migration script for Wordpress, but this used a direct database connection to the Wordpress MySQL server to extract a minimum amount of information for each blog post. To avoid missing information that I later might want after having shut down the Wordpress server, I wanted to base the migration on a full XML export from Wordpress. This way I could go back and extract more information from the old posts, if, and when, needed.

Seeing this as a chance to play with Ruby - a language I've only dabbled in previously - I set out to build a simple Wordpress XML->Jekyll converter. The converter reads the Wordpress export and converts all blog posts into separate files with a proper Yaml frontmatter. It also converts the syntax highlighting markup, code markup and headings into the corresponding markdown conventions. The rest of the HTML is left alone.

For the images I just copied the files in the old Wordpress structure into the new blog directory layout so that the URLs still match. Not the nicest way, but I was too lazy to do anything about it just yet.

Anywho - the source for the Wordpress XML converter is available in the [blog branch of my fork of Octopress][fork]. The source is also given below. To use, just run with the filename of the Wordpress XML export as the first argument.

 [fork]: http://github.com/melwin/octopress/blob/blog/source/_import/wordpress_xml_import.rb

It's my first publicly available Ruby program - so be careful. Improvements welcome!

/M

{% highlight ruby%}
require 'fileutils'
require 'date'
require 'yaml'
require 'rexml/document'
include REXML


file = File.new(ARGV[0])
doc = Document.new file

FileUtils.mkdir_p "_posts"

doc.elements.each("rss/channel/item[wp:status = 'publish' and wp:post_type = 'post']") do |e|
    post = e.elements
    slug = post['wp:post_name'].text
    date = DateTime.parse(post['wp:post_date'].text)
    name = "%02d-%02d-%02d-%s.markdown" % [date.year, date.month, date.day, slug]

    content = post['content:encoded'].text

    content = content.gsub(/<code>(.*?)<\/code>/, '`\1`')

    content = content.gsub(/<pre lang="([^"]*)">(.*?)<\/pre>/m, '{% highlight \1 %}\2{% endhighlight %}')
    
    3.times do |i|
	i += 1
        content = content.gsub(/<h#{i}>([^<]*)<\/h#{i}>/, ('#'*i) + ' \1')
    end

    puts "Converting: #{name}"

    data = {
        'layout' => 'blog_post',
        'title' => post['title'].text,
        'excerpt' => post['excerpt:encoded'].text,
        'wordpress_id' => post['wp:post_id'].text,
        'wordpress_url' => post['guid'].text
     }.delete_if { |k,v| v.nil? || v == ''}.to_yaml

     File.open("_posts/#{name}", "w") do |f|
         f.puts data
         f.puts "---"
         f.puts content
     end
 
end
{% endhighlight %}
