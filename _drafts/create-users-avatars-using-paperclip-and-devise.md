---
layout: post
title:  "Create Avatars for users using Devise and Paperclip"
date:   2017-12-01 12:00:43 -0800
categories: jekyll update
---

Devise is a great way to quickly setup user authentication quickly on your site.  It provides the helper methods, views, routes, and controllers you will need for authentication.

First you need to install the gem in your Gemfile
{% highlight ruby %}
gem 'devise'
{% endhighlight %}

Next in the terminal run
{% highlight bash %}
rails generate devise:install
{% endhighlight %}

Now we want to to add a model for devise to authenticate.  Lets choose user.
{% highlight bash %}
rails generate devise user
{% endhighlight %}

Now that we have a user we can generate the views to login, logout, sign up etc.
{% highlight bash %}
rails generate devise:views
{% endhighlight %}

When we created the user model we also created a migration to store the user in our database. Let's update our schema.
{% highlight bash %}
rails db:migrate
{% endhighlight %}

And that is it! We can now successfully authenticate users!

While we can sign in by going to /users/sign_in that isn't very practical.  Lets make a navbar so that users can log in and out.  In our app/views/layouts/application.hmtl.erb add this to the body above yield.
{% highlight erb %}
...
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <% if current_user %>
    <p> Welcome <% username.email %>
    <a class="navbar-brand"><%= link_to 'Edit Profile',edit_user_registration_path %></a>
    <a class="navbar-brand  ml-auto"><%= link_to 'Logout', destroy_user_session_path, method: :delete %></a>
  <% else %>
    <a class="navbar-brand ml-auto"><%= link_to 'Login', new_user_session_path %></a>
  <% end %>
</nav>
...
{% endhighlight %}

This navbar will not work unless you have bootstrap in your project. To add it to your project install bootstrap in your Gemfile.
{% highlight ruby %}
gem 'bootstrap'
{% endhighlight %}
Then go to app/assets/stylesheets/application.css.  Rename the file from .css to .scss to make it a Sass stylesheet and add this line of code to the top.
{% highlight scss %}
@import "bootstrap";
{% endhighlight %}

Now a user can quickly log in and out using the navbar.  But its a bit odd to welcome a user by their email.  It would be better to use a username.  Devise did not generate a username in our database or views so we will need to make them ourselves.

{% highlight bash %}
 rails g migration add_username_column
{% endhighlight %}

open the file created in the db/migrate folder and add inside of class AddUsernameColumn:
{% highlight ruby %}
def change
  add_column :users, :username, :string
  add_index :users, :username, unique: true
end
{% endhighlight %}
This creates a column in user that stores username as a string, the index requires username to be unique so that multiple users can't have the same username.

Great now we need to take user input from sign up and add an input for them to put a username.
Open app/views/devise/registrations both files new.html.erb and edit.html.erb.  Right below the field for email add this code for a username field.
{% highlight erb %}
  <div class="field">
    <%= f.label :username %><br />
    <%= f.text_field :username, autofocus: true %>
  </div>
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
rails generate devise:views
{% endhighlight %}
