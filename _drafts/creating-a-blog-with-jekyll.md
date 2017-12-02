---
layout: post
title:  "Creating a blog with Jekyll"
date:   2017-12-01 12:00:43 -0800
categories: jekyll update
---
{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}


Follow along with the jekyll documentation on setting up a blog.  Then document that as my first blog post.

Installation requirements:
* > Ruby 2.1
* RubyGems
* GCC check by running gcc-v
* Make check by running make-v
* Gem install Jekyl
* Gem install Rouge for syntax highlighting
* jekyll new my_blog
* add gem 'jekyll-livereload' into Gemfile in the group :jekyll_plugins do
* jekyll serve
* create drafts folder to create posts that are unfisnished, drafts are just posts without a update
* jekyll serve --drafts
