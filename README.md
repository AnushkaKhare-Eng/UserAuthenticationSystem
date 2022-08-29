# UserAutheticationSystem
A User Authentication System developed using Rails 
## Gemfile
Starting off with building a Gemfile and installing all necesarry Rails Packages. We also installed the bundler to ensure that the bundler unstaller will install any missing gems and download updates to existing gems. 
```ruby
#Installing Rails packages required and bundle installer

gem install bycrpt 
gem install bootstrap-sass
bundle install

```
## User Model and Creating a database
Then we will create models for authenticating users
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
In this step we ensure that the data saved is not null and assign unqiue email addresses to the users. We then perform format validation before the email is stored in the database
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
## Views
### Welcome Page
### Sign Up Page 
### New User
## User Controllers
### User Controller
### Application Controller
### Session Controller
