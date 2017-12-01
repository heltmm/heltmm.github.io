---
layout: post
title:  "Create Avatars for users using Devise and Paperclip"
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

{% highlight bash %}
 brew install imagemagick
{% endhighlight %}

{% highlight ruby %}
gem 'paperclip'
gem 'devise'
{% endhighlight %}

{% highlight bash %}
  bundle install
{% endhighlight %}

{% highlight bash %}
rails generate migration add_avatars_to_users
{% endhighlight %}

{% highlight ruby %}
class AddAvatarsToUsers < ActiveRecord::Migration
  def self.up
    change_table :users do |t|
      t.attachment :avatar
    end
  end

  def self.down
    drop_attached_file :users, :avatar
  end
end
{% endhighlight %}

{% highlight bash %}
rake db:migrate
{% endhighlight %}

{% highlight ruby %}
class User < ActiveRecord::Base
  # existing code

  has_attached_file :avatar, :styles => { :medium => "300x300>", :thumb => "100x100#" }, :default_url => "/images/:style/missing.png"
  validates_attachment_content_type :avatar, :content_type => /\Aimage\/.*\Z/
end
{% endhighlight %}


{% highlight ruby %}
class ApplicationController < ActionController::Base
    # Prevent CSRF attacks by raising an exception.
    # For APIs, you may want to use :null_session instead.
    protect_from_forgery with: :exception
    before_filter :configure_permitted_parameters, if: :devise_controller?

    protected

    def configure_permitted_parameters
        devise_parameter_sanitizer.for(:sign_up) { |u| u.permit(:name, :email, :password) }
        devise_parameter_sanitizer.for(:account_update) { |u| u.permit(:name, :email, :password, :current_password, :is_female, :date_of_birth, :avatar) }
    end
end
{% endhighlight %}

{% highlight bash %}
rails generate devise:install
{% endhighlight %}

{% highlight bash %}
rails generate devise user
{% endhighlight %}

{% highlight bash %}
rails generate devise:install
{% endhighlight %}