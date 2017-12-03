---
layout: post
title:  "Creating a Dropdown Navbar With Users and Avatars"
date:   2017-12-01 12:00:43 -0800
categories: jekyll update
---
This guide will use Devise, Paperclip, and Bootstrap to quickly implement a powerful navbar to allow users to create accounts, log in, log out, edit profiles, add avatars, all from an elegent drop down navbar.


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
    <p> Welcome <%= current_user.email %>
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

Now a user can quickly log in and out using the navbar.  But its a bit odd to welcome a user by their email.  It would be better to use a username.  Looking at the schema build by Devise there is no column to store a username in our database.
![User Schema]({{ "/assets/default_user_schema.png" | absolute_url }})

We will have to do that ourselves in the terminal:
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

If we were to go to the sign up view our code isn't showing on the page! This is because our devise configuration doesn't allow views to be changed.  Go to config/initializers/devise.rb and un comment this line.
{% highlight ruby %}
  config.scoped_views = true
{% endhighlight %}

Great now users can sign up with a username! But wait they can input the username but it isn't being saved.  This is because of how rails deals with security issues.  Each controller must explicitly say what params it will accept.  Open the file app/controllers/application_controller.rb and add this below protect_from_forgery.
{% highlight ruby %}
...
before_action :configure_permitted_parameters, if: :devise_controller

protected
def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:username, :email, :password])
    devise_parameter_sanitizer.permit(:account_update, keys: [:username, :email, :password, :current_password])
end
...
{% endhighlight %}

Now we can save username! Lets display that on our navbar changing from email to username
{% highlight erb %}
...
    <p> Welcome <%= current_user.username %>
...
{% endhighlight %}

So now We have successfully implemented devise and customized it for username.  Lets customize user again with an avatar that will display in our navbar.

We will user paperclip uploading the image but we will also need imagemagick to process the image. In the console type:
{% highlight bash %}
 brew install imagemagick
{% endhighlight %}
Next add paperclick to your gem file:
{% highlight ruby %}
gem 'paperclip'
{% endhighlight %}

Run:
{% highlight bash %}
  bundle install
{% endhighlight %}

Just like adding usernames we need to add avatars to our users with a migration
{% highlight bash %}
rails generate migration add_avatars_to_users
{% endhighlight %}

In our created migration file add this.
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

Again we migrate to update the schema.
{% highlight bash %}
rake db:migrate
{% endhighlight %}

The commands in that migration were pretty new to me so a quick look at the schema shows what happened to our users tables.
![After Migration]({{ "/assets/after_migration.png" | absolute_url }})

As you can see four lines were added.

Now we need to update our User mode with the following code.
{% highlight ruby %}
class User < ActiveRecord::Base
  # existing code

  has_attached_file :avatar, :styles => { :medium => "300x300>", :thumb => "100x100#" }, :default_url => "/images/:style/missing.png"
  validates_attachment_content_type :avatar, :content_type => /\Aimage\/.*\Z/
end
{% endhighlight %}
This attaches the uploaded file to our user model


Again in order to user the avatar params we must permit it in the application_controller.rb, lets add :avatar to those params
{% highlight ruby %}
class ApplicationController < ActionController::Base
    protect_from_forgery with: :exception

    before_action :configure_permitted_parameters, if: :devise_controller

    protected
    def configure_permitted_parameters
        devise_parameter_sanitizer.permit(:sign_up, keys: [:username, :email, :password, :avatar])
        devise_parameter_sanitizer.permit(:account_update, keys: [:username, :email, :password, :current_password, :avatar])
    end
end
{% endhighlight %}

Now we add the form input in the views like we did for username.  In the new and edit views add above the  username field.
{% highlight erb %}
<div class="field">
  <%= f.label :avatar  %>
  <%= f.file_field :avatar %>
</div>
{% endhighlight %}

A link to the finished repository for this project can be found at



[dropdown-navbar-template]: https://github.com/heltmm/Dropdown-NavBar-Template
