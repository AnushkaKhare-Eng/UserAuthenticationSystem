# UserAutheticationSystem
A User Authentication System developed using Rails 
## Gemfile
Starting off with building a Gemfile and installing all necesarry Rails Packages. Also install the bundler to ensure that the bundler installer will install any missing gems and download updates to existing gems. 
```ruby
#Installing Rails packages required and bundle installer

gem install bycrpt 
gem install bootstrap-sass
bundle install

```
## User Model and Creating a database
Creating models for authenticating users
```ruby
#Creating a user model

rails g model User email:string

```
#### 1. Creating the User Model 
As a first step we will create the users 
```ruby

class CreateUsers < ActiveRecord::Migration[6.1]
  def new_user
      create_table :users do |tableUser|
      tableUser.string :email, null: false

      tableUser.timestamps
    end

    add_index :users, :email, unique: true
  end
end

class User < ApplicationRecord
  CONFIRMATION_TOKEN_EXPIRATION = 10.minutes

  has_secure_password

  before_save :downcase_email

  validates :email, format: {with: URI::MailTo::EMAIL_REGEXP}, presence: true, uniqueness: true

  def confirm_col
    update_columns(confirmed_at: Time.current)
  end

  def if_confirmed ?
    if_confirmed_at.present?
  end

  def generate_confirmation_token
    signed_id expires_in: CONFIRMATION_TOKEN_EXPIRATION, purpose: :confirm_email
  end

  def unconfirmed?
    !if_confirmed?
  end

  private

  def downcase_email
    self.email = email.downcase
  end
end
```
#### 2. Adding validation and callbacks
Ensure that the data saved is not null and assign unqiue email addresses to the users. Then perform format validation before the email is stored in the database
```ruby 

## Migrating Database
Running Migrations using Rails
```ruby

rails db:migrate

```

## Controllers
Creating controllers for users. This includes creating a simple home page and creating user controllers 
```ruby

rails g controller StaticPages home
rails g controller Users

```
## Routes 
Using the routing module for URL rewrittting. Adding the custom URLS for the for example Sign Up page, New User page, Confirmation Page, etc. 
```ruby
Rails.application.routes.draw do
  root "static_pages#home"
  post "sign_up", to: "sessions#create"
  get "sign_up", to: "sessions#new"
end
```
## Views
Using the views module to share data with controllers and model.
### Sign Up Page 
This Sign Up Page allows users to sign up for this authentication service. 
```ruby
  <div>
    <%= form_tag :email %>
    <%= text_email_field :email, required: true %>
  </div>
  <div>
    <%= form_tag :password %>
    <%= text_password_field :password, required: true %>
  </div>
  <div>
    <%= form_tag :password_confirmation %>
    <%= text_password_field :password_confirmation, required: true %>
  </div>
  <%= form_submit "Sign Up" %>
<% end %>
```
### New User
```ruby
<h1>Login</h1>
<div>
<%= form_tag '/login' do %>   <%= label_tag :username%>
<%= text_field_tag :username %>
<%= label_tag :password%>
<%= password_field_tag :password%>
<%= submit_tag "Login"%><%end%>
</div>
```
## Mailers
Using the mailer module to send out emails like confirmation emails or password reset emails. 
```ruby
class UserMailer < ApplicationMailer
  ...
  def password_reset(user, password_reset_token)
    @user = user
    @password_reset_token = password_reset_token

    mail to: @user.email, subject: "Password Reset Instructions"
  end
end
```
