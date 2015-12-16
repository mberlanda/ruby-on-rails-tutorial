# Ruby on Rails Tutorial: sample application

This is the sample application for the 
[*Ruby on Rails Tutorial:
Learn Web Development with Rails*] (http://www.railstutorial.org/)
by [Micheal Hartl] (http://www.michealhartl.com/).

* **[Chapter 3: Mostly Static Pages](#cap3)**
* **[Chapter 4: Rails-Flavored Ruby](#cap4)**
* **[Chapter 5: Filling in the Layout](#cap5)**
* **[Chapter 6: Modeling Users](#cap6)**
* **[Chapter 7: Sign Up](#cap7)**
* **[Chapter 8: Log In, Log Out](#cap8)**
* **[Chapter 9: Updating, Showing, and Deleting Users](#cap9)**
* **[Chapter 10: Account Activation and Password Reset](#cap9)**


<h2 id="cap3">Chapter 3: Mostly Static Pages</h2>

branch: *static-pages*, *static-pages-exercises*

#### Initial commit:
```bash
$ rails new sample_app
# edit GemFile
$ bundle update
$ mv README.rdoc README.md
```
#### Generate StaticPages:
```bash
$ git checkout master
$ git checkout -b static-pages
$ rails generate controller StaticPages home help
$ git push --set-upstream origin static-pages
```

#### Custom StaticPages:
* edit *app/views/static_pages/home.html.erb*
* edit *app/views/static_pages/help.html.erb*

#### Getting Started with Testing:
* $ ls test/controllers
* look at *static_pages_controller_test.rb*
* a. comment out 'guard-minitest'
* b. install guard
* b. $ bundle exec rake db:migrate #if the app is using the database
* $ bundle exec rake test
* add test "should get about" in class StaticPagesControllerTest

####  StaticPagesControllerTest#test_should_get_about:

* error 1:
  ActionController::UrlGenerationError: No route matches {:action=>"about", :controller=>"static_pages"}
* solution 1:
  *config/routes.rb* add get 'static_pages/about'

* error 2:
  AbstractController::ActionNotFound: The action 'about' could not be found for StaticPagesController
* solution 2:
  app/controllers/static_pages_controllers.rb add def about;end

* error 3:
  ActionView::MissingTemplate: Missing template static_pages/about, application/about with {:locale=>[:en], :formats=>[:html], :variants=>[], :handlers=>[:erb, :builder, :raw, :ruby, :coffee, :jbuilder]}
* solution 3:
  $ touch app/views/static_pages/about.html.erb
  edit the content of the file

#### Slightly Dynamic Pages:
* # Red-Green-Refactor cycle
* $ mv app/views/layouts/application.html.erb layout_file
* assert_select "title", "Home | Ruby on Rails Tutorial Sample App"
* add the page title: <% provide(:title, "Home") %>
* $ mv layout_file app/views/layouts/application.html.erb
* add to layout_file: <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
* root the home page in config/routes.rb

#### Finish Static Page:
* merge static-pages and master branch

Minitest Reporters:
*test/test_helper.rb*
```ruby
require "minitest/reporters"
Minitest::Reporters.use!
```

<h2 id="cap4">Chapter 4: Rails-Flavored Ruby</h2>

branch: *rails-flavored-ruby*

#### Title Application Helper:

*app/helpers/application_helper.rb*
```ruby
def full_title(page_title = '')
  base_title = "Ruby on Rails Tutorial Sample App"
  if page_title.empty?
    base_title
  else
    "#{page_title} | #{base_title}"
  end
end
```

*app/views/layouts/application.html.erb*
```ruby
<title><%= full_title(yield(:title)) %></title>
```

####  Example User Class:

*example_user.rb*
```ruby
2.2.1 :001 > require './example_user'
 => true 
2.2.1 :002 > example = User.new
 => #<User:0x0000000142ae90 @name=nil, @email=nil> 
2.2.1 :003 > example.name
 => nil 
2.2.1 :004 > example.name = "Example User"
 => "Example User" 
2.2.1 :005 > example.email = "user@example.com"
 => "user@example.com" 
2.2.1 :006 > example.formatted_email
 => "Example User <user@example.com>" 
```

```bash
$ rm example_user.rb
```

<h2 id="cap5">Chapter 5: Filling in the Layout</h2>

branch: *filling-in-layout*

#### Adding Some Structure:

*app/views/layouts/application.html.erb*
```html
<head>
  <!-- [if lt IE 9]>
    <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js"></script>
  <![endif]-->
</head>
```

* create in body an header using CSS **class** and a **div** container
```html
<body>
  <header class="navbar navbar-fixed-top navbar-inverse">
    <div class="container">
    <%= link_to "sample_app", "#", id: "logo" %>
      <nav>
        <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home", "#" %></li>
        <li><%= link_to "Help", "#" %></li>
        <li><%= link_to "Log in", "#" %></li>
        </ul>
      </nav>
    </div>
  </header>
  <div class="container">
    <%= yield %>
  </div>
</body>
```

* change _layouts/application_ from .html.erb to .html.haml
```haml
  %body
    %header.navbar.navbar-fixed-top.navbar-inverse
      .container
        = link_to "sample_app", "#", id: "logo"
        %nav
          %ul.nav.navbar-nav.navbar-right#navbar
            %li= link_to "Home", "#{root_path}"
            %li= link_to "Help", "#"
            %li= link_to "Log in", "#"  
    .container
      = yield
```

* add some features to _home.html.haml_
```haml
- provide(:title, "Home")
.center.jumbotron
  %h1 Sample App
  %h2
    This is the home page for the
    %a{:href => "http://www.railstutorial.org/"} Ruby on Rails Tutorial
    sample application.

  .btn.btn-lg.btn-primary= link_to "Sign up now!", "#" 

  = link_to image_tag("rails.png", alt: "Rails logo"), 'http://rubyonrails.org/'
  ```

* add the image to _/app/assets/images_
```bash
$ cd app/assets/images/
$ curl -O http://rubyonrails.org/images/rails.png
```

#### Bootstrap and Custom CSS:

*Gemfile*
```ruby
gem 'bootstrap-sass'
```

```bash
$ bundle
$ touch app/assets/stylesheets/custom.css.scss
```

*app/assets/stylesheets/custom.css.scss*
```scss
@import "bootstrap-sprockets";
@import "bootstrap";
```

#### Partials:

*app/views/layouts/_footer.html.haml*
```haml
%footer.footer
  %small
    The
    %a{:href => "http://www.railstutorial.org/"} Ruby on Rails Tutorial
    by
    %a{:href => "http://www.michealhartl.com/"} Micheal Hartl
  %nav
    %ul
      %li= link_to "About", "#"
      %li= link_to "Contact", "#"
      %li= link_to "News", "http://news.railstutorial.org/"
```

*app/stylesheets/custom.css.scss*

```scss
/* footer */

footer {
  margin-top: 45px;
  padding-top: 5px;
  border-top: 1px solid #eaeaea;
  color: #777;
}
footer a {
  color: #555;
}
footer a:hover {
  color: #222;
}
footer small {
  float: left;
}
footer ul {
  float: right;
  list-style: none;
}
footer ul li {
  float: left;
  margin-left: 15px;
}
```

#### Sass and the Asset Pipeline:
```scss
/* footer nested and with variables */
footer {
  margin-top: 45px;
  padding-top: 5px;
  border-top: 1px solid $gray-medium-light;
  color: $gray-light;
  a {
   color: $gray;
   &:hover {
     color: $gray-darker;
    }
  }
  small {
   float: left;
  }
  ul {
    float: right;
    list-style: none;
    li {
      float: left;
      margin-left: 15px;
    }
  }
}
```

#### Layout Links:

*config/routes.rb*
```ruby
Rails.application.routes.draw do
  root 'static_pages#home'
  get 'help' => 'static_pages#help'
  get 'about' => 'static_pages#about'
  get 'contact' => 'static_pages#contact'
```

*app/views/layouts/_footer.html.haml*
```haml
%nav
  %ul
    %li= link_to "About", "#{about_path}"
    %li= link_to "Contact", "#{contact_path}"
    %li= link_to "News", "http://news.railstutorial.org/"
```

*test/integration/site_layout_test.rb*
```bash
$ rails g integration_test site_layout
  invoke  test_unit
  create    test/integration/site_layout_test.rb
```
* get the root path
* verify that the right page template is rendered
* check for the correct links to the Home, Help, About and Contact pages
```ruby
require 'test_helper'

class SiteLayoutTest < ActionDispatch::IntegrationTest

  test "layout links" do 
    get root_path
    assert_template  'static_pages/home'
    assert_select "a[href=?]", root_path, count: 2 

  end
end
```

#### User Sign-up:

*Generate new User controller*
```bash
$ rails g controller Users new
$ rake test
```
* *app/controllers/users_controller.rb*
* *app/views/users/new.html.haml*
* *test/controllers/users_controller_test.rb*

*config/routes.rb*
```ruby
  get 'signup' => 'users#new'
```
*app/views/static_pages/home.html.haml*
```haml
= link_to "Sign up now!", "#{signup_path}", class: "btn btn-lg btn-primary" 
```

#### Including Application Helper in tests:

*test/test_helper.rb*
```ruby
class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
  include ApplicationHelper
end
```

*test/integration/site_layout_test.rb*
```ruby
class SiteLayoutTest < ActionDispatch::IntegrationTest

  test "layout links" do 
  # ...
  get signup_path
    assert_select "title", full_title("Sign up")
```

*test/helpers/application_helper_test.rb*
```ruby
require 'test_helper'

class ApplicationHelperTest < ActionView::TestCase
  
  def setup
    @base_title = "Ruby on Rails Tutorial Sample App"
  end

  test "full title helper" do
    assert_equal full_title, "#{@base_title}"
    assert_equal full_title("Help"), "Help | #{@base_title}"
  end

end
```

<h2 id="cap6">Chapter 6: Modeling Users</h2>

branch: *modeling-users*

#### User Model

```bash
$ rails g model User name:string email:string
      invoke  active_record
      create    db/migrate/20151210095655_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml
$ bundle exec rake db:migrate
$ rails console --sandbox
Loading development environment in sandbox (Rails 4.2.5)
Any modifications you make will be rolled back on exit
2.2.1 :001 > User.new
 => #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil> 
2.2.1 :002 > user = User.new(name: "Micheal Hartl", email:"mhartl@example.com")
 => #<User id: nil, name: "Micheal Hartl", email: "mhartl@example.com", created_at: nil, updated_at: nil> 
2.2.1 :003 > user.valid?
 => true 
2.2.1 :004 > user.save
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.5ms)  INSERT INTO "users" ("name", "email", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "Micheal Hartl"], ["email", "mhartl@example.com"], ["created_at", "2015-12-10 10:08:41.225529"], ["updated_at", "2015-12-10 10:08:41.225529"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
 => true 
2.2.1 :005 > user
 => #<User id: 1, name: "Micheal Hartl", email: "mhartl@example.com", created_at: "2015-12-10 10:08:41", updated_at: "2015-12-10 10:08:41"> 
2.2.1 :006 > User.create(name: "A Nother", email: "another@example.com")
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.1ms)  INSERT INTO "users" ("name", "email", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "A Nother"], ["email", "another@example.com"], ["created_at", "2015-12-10 10:09:56.057812"], ["updated_at", "2015-12-10 10:09:56.057812"]]
   (0.0ms)  RELEASE SAVEPOINT active_record_1
 => #<User id: 2, name: "A Nother", email: "another@example.com", created_at: "2015-12-10 10:09:56", updated_at: "2015-12-10 10:09:56"> 
2.2.1 :007 > foo = User.create(name: "Foo", email: "foo@bar.com")
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.1ms)  INSERT INTO "users" ("name", "email", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "Foo"], ["email", "foo@bar.com"], ["created_at", "2015-12-10 10:10:28.138126"], ["updated_at", "2015-12-10 10:10:28.138126"]]
   (0.0ms)  RELEASE SAVEPOINT active_record_1
 => #<User id: 3, name: "Foo", email: "foo@bar.com", created_at: "2015-12-10 10:10:28", updated_at: "2015-12-10 10:10:28"> 
2.2.1 :008 > foo.destroy
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.1ms)  DELETE FROM "users" WHERE "users"."id" = ?  [["id", 3]]
   (0.0ms)  RELEASE SAVEPOINT active_record_1
 => #<User id: 3, name: "Foo", email: "foo@bar.com", created_at: "2015-12-10 10:10:28", updated_at: "2015-12-10 10:10:28"> 
2.2.1 :009 > User.find(1)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT 1  [["id", 1]]
 => #<User id: 1, name: "Micheal Hartl", email: "mhartl@example.com", created_at: "2015-12-10 10:08:41", updated_at: "2015-12-10 10:08:41"> 
2.2.1 :010 > User.find(4)
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT 1  [["id", 4]]
ActiveRecord::RecordNotFound: Couldn't find User with 'id'=4
2.2.1 :011 > User.find_by(email: "another@example.com")
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."email" = ? LIMIT 1  [["email", "another@example.com"]]
 => #<User id: 2, name: "A Nother", email: "another@example.com", created_at: "2015-12-10 10:09:56", updated_at: "2015-12-10 10:09:56"> 
2.2.1 :012 > User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users"  ORDER BY "users"."id" ASC LIMIT 1
 => #<User id: 1, name: "Micheal Hartl", email: "mhartl@example.com", created_at: "2015-12-10 10:08:41", updated_at: "2015-12-10 10:08:41"> 
2.2.1 :013 > User.all
  User Load (0.2ms)  SELECT "users".* FROM "users"
 => #<ActiveRecord::Relation [#<User id: 1, name: "Micheal Hartl", email: "mhartl@example.com", created_at: "2015-12-10 10:08:41", updated_at: "2015-12-10 10:08:41">, #<User id: 2, name: "A Nother", email: "another@example.com", created_at: "2015-12-10 10:09:56", updated_at: "2015-12-10 10:09:56">]> 
```

#### User Validations

* presence
* length
* email format
* email uniqueness


*app/models/user.rb*
```ruby
class User < ActiveRecord::Base

  before_save { self.email = email.downcase}
  validates :name, presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX},
                    uniqueness: { case_sensitive: false }
end
```
```bash
# Migration for enforcing email uniqueness
$ rails g migration add_index_to_user_email
      invoke  active_record
      create    db/migrate/20151210110627_add_index_to_user_email.rb
```

*db/migrate/20151210110627_add_index_to_user_email.rb*
```ruby
class AddIndexToUserEmail < ActiveRecord::Migration
  def change
    add_index :users, :email, unique: true
  end
end
```
```bash
$ bundle exec rake db:migrate
$ rake test
# empty db/fixtures/users.yml to fix ActiveRecod::RecordNotUnique exception
$ rake test
```

#### Adding a Secure Password

* Add has_secure_password in *app/models/user.rb*
```bash
# Migration for enforcing add
$ rails g migration add_password_digest_to_users password_digest:string
      invoke  active_record
      create    db/migrate/20151210112418_add_password_digest_to_users.rb
$ bundle exec rake db:migrate
```
*db/migrate/20151210112418_add_password_digest_to_users.rb*
```ruby
add_column :users, :password_digest, :string
```
* uncomment in *Gemfile* gem 'bcrypt' and launch $ bundle install
* add password to setup in *test/models/user_test.rb*
* create password length test and validation
```bash
2.2.1 :001 > User.create(name: "foo bar", email: "foo@bar.com", password: "foobar2015", password_confirmation: "foobar2015")
2.2.1 :002 > user = User.find_by(email: "foo@bar.com")
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."email" = ? LIMIT 1  [["email", "foo@bar.com"]]
2.2.1 :003 > user.authenticate("foobar2015")
 => #<User id: 1, name: "foo bar", email: "foo@bar.com", created_at: "2015-12-10 11:44:13", updated_at: "2015-12-10 11:44:13", password_digest: "$2a$10$8Me1CLJuqs8U4SUtEz.3NOyJkuMyeXNqaT/VO/PC2ma..."> 
```

<h2 id="cap7">Chapter 7: Sign Up</h2>

branch: *sign-up*


<h2 id="cap8">Chapter 8: Log In, Log Out</h2>

branch: *log-in-log-out*

#### Sessions:
```bash
$ rails g controller Sessions new
    create  app/controllers/sessions_controller.rb
     route  get 'sessions/new'
    invoke  haml
    create    app/views/sessions
    create    app/views/sessions/new.html.haml
    invoke  test_unit
    create    test/controllers/sessions_controller_test.rb
    invoke  helper
    create    app/helpers/sessions_helper.rb
    invoke    test_unit
    invoke  assets
    invoke    coffee
    create      app/assets/javascripts/sessions.coffee
    invoke    scss
    create      app/assets/stylesheets/sessions.scss
```
*config/routes.rb*
```ruby
get 'login' => 'sessions#new'
post 'login' => 'sessions#create'
delete 'logout' => 'sessions#destroy'
```
* edit *app/views/sessions/new.html.haml*
* edit *app/controllers/sessions_controller.rb*
```ruby
  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      # Log the user in and redirect to the user's show page
    else
      # Create an error message
      flash.now[:danger] = 'Invalid email/password combination' # Not quite right
      render 'new'
    end
  end
```
* create a test
```bash
$ rails generate integration_test users_login
      invoke  test_unit
      create    test/integration/users_login_test.rb
```

#### Logging In:

* include SessionsHelper in *app/controllers/application_controller.rb*
*app/controllers/sessions_controller.rb*
```ruby
  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      # Log the user in and redirect to the user's show page
      log_in user
      redirect_to user
```
*app/helpers/sessions_helper.rb*
```ruby
module SessionsHelper
  #Log in the given user
  def log_in(user)
    session[:user_id] = user.id
  end
  # Returns the current logged-in user (if any)
  def current_user
    @current_user ||= User.find_by(id: session[:user_id])
  end
  # Returns true if the user is logged in, false otherwise
  def logged_in?
    !current_user.nil?
  end
end
```
* change the layout links for logged-in users in *app/views/layouts/_headers.html.haml*

* test the login with valid information
*app/models/user.rb*
```ruby
  # Returns the hash digest of the given string.
  def User.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
  end
``` 
*test/fixtures/users.yml*
*test/integration/users_login_test.rb*
* login upon sign-up in *app/controller/users_controller.rb*
*test/test_helper.rb*
```ruby
  # Returns true if a test user is logged in
  def is_logged_in?
    !session[:user_id].nil?
  end
```
* assert is_logged-in in *test/integration/users_signup_test.rb*

#### Logging Out:

* log out function as session.delete(:user_id) in *app/helpers/sessions_helper.rb*

*app/controllers/sessions_controller.rb*
```ruby
  def destroy
    log_out
    redirect_to root_url
  end
```

#### Remember Me:

* Rememeber Token and Digest
```bash
$ rails g migration add_remember_digest_to_users remember_digest:string
      invoke  active_record
      create    db/migrate/20151214162937_add_remember_digest_to_users.rb
$ bundle exec rake db:migrate
```
*app/models/user.rb*
```ruby
class User < ActiveRecord::Base

  attr_accessor :remember_token
  ...
  # Returns a random token
  def User.new_token
    SecureRandom.urlsafe_base64
  end

  # Remembers a user in the database for use in persisten sessions
  def remember
    self.remember_token = User.new_token
    update_attribute(:remember_digest, User.digest(remember_token))
  end

  # Returns true if the given token matches the digest
  def authenticated? (remember_token)
    BCrypt::Password.new(remember_digest).is_password?(remember_token)
  end

  # Forget a user
  def forget
    update_attribute(:remember_digest, nil)
  end
end
```
* Update def create > remember_user in *app/controllers/sessions_controller.rb*

*app/helpers/sessions_helper.rb*
```ruby
  # Remembers a user in a persistent session
  def remember(user)
    user.remember
    cookies.permanent.signed[:user_id] = user.id
    cookies.permanent[:remember_token] = user.remember_token
  end
  
  # Returns the user corresponding to the remember toekn cookie
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end

    # Forgets a persistent session
  def forget(user)
    user.forget
    cookies.delete(:user_id)
    cookies.delete(:remember_token)
  end
  
  # Log out the current user:
  def log_out
    forget(current_user)
    session.delete(:user_id)
    @current_user = nil
  end
```
*app/controllers/sessions_controller.rb*
```ruby
  def destroy
    log_out if logged_in?
    redirect_to root_url
  end
```

* Add "remember me" check box to the login form


<h2 id="cap9">Chapter 9: Updating, Showing, and Deleting Users</h2>


#### Update Users:

* def edit in *app/controllers/users_controller.rb*
*app/views/users/edit/.html.haml
```haml
- provide(:title, "Edit user")
%h1 Update your Profile
.row
  .col-md-6.col-md-offset-3
    = form_for(@user) do |f|
      = render 'shared/error_messages'

      = f.label :name
      = f.text_field :name, class: 'form-control'

      = f.label :email
      = f.email_field :email, class: 'form-control'

      = f.label :password
      = f.password_field :password, class: 'form-control'

      = f.label :password_confirmation, "Confirmation"
      = f.password_field :password_confirmation, class: 'form-control'

      = f.submit "Save changes", class: "btn btn-primary"

    .gravatar_edit
      = gravatar_for @user
      = link_to "change", "http://gravatar.com/emails", target:"_blank"
```

*app/controllers/users_controller.rb*
```ruby
  def edit
    @user = User.find(params[:id])
  end

  def update
    @user = User.find(params[:id])
    if @user.update_attributes(user_params)
        flash[:success] = "Profile updated"
        redirect_to @user
    else
      render 'edit'
    end
  end
```
* password, allow_blank: true in *app/models/users.rb*
```bash
$ rails g integration_test users_edit
```

#### Authorization:

* Require logged-in users

*app/controllers/users_controller.rb*
```ruby
class UsersController < ApplicationController
  before_action :logged_in_user, only: [:edit, :update]
  ...
  # Before Filters

  def logged_in_user
    unless logged_in?
      flash[:danger] = "Please log in."
      redirect_to login_url
    end
  end
```

* Require the Right User
```ruby
class UsersController < ApplicationController
  before_action :logged_in_user, only: [:edit, :update]
  before_action :correct_user, only: [:edit, :update]
  # ...
  # Before Filters
  # ...
  def correct_user
    @user = User.find(params[:id])
    redirect_to(root_url) unless @user == current_user
  end
```

*app/helpers/sessions_helper.rb*
```ruby
  def current_user? (user)
    user == current_user
  end
```
*app/controllers/users_controller.rb*
```ruby
  def correct_user
    @user = User.find(params[:id])
    redirect_to(root_url) unless current_user?(@user)
  end
```

* Friendly Forwarding:

*app/helpers/sessions_helper.rb*
```ruby
  # Redirects to stored location (or to the default)
  def redirect_back_or(default)
    redirect_to(session[:forwarding_url] || default)
    session.delete(:forwarding_url)
  end

  # Stores the URL tryung to be accessed
  def store_location
    session[:forwarding_url] = request.url if request.get?
  end
```
*app/controllers/users_controller.rb*
```ruby
  def logged_in_user
    unless logged_in?
      store_location
      flash[:danger] = "Please log in."
      redirect_to login_url
    end
  end
```
*app/controllers/sessions_controller.rb*
```ruby
  def create
    @user = User.find_by(email: params[:session][:email].downcase)
    if @user && @user.authenticate(params[:session][:password])
      log_in @user
      params[:session][:remember_me] == '1' ? remember(@user) : forget(@user)
      redirect_back_or @user
```

#### Showing All Users:

*app/controllers/users_controller.rb*
```ruby
class UsersController < ApplicationController
before_action :logged_in_user, only: [:index, :edit, :update]
...
  def index
    @users = User.all
  end
```
*app/views/users/index.html.haml*
```haml
- provide(:title, "All users")
%h1 All users
%ul.users
  - @users.each do |user|
    %li
      = gravatar_for user, size: 50
      = link_to user.name, user
```
*app/stylesheets/custom.css.scss*
```css
/* User index */
.users{
  list-style: none;
  margin: 0;
  li {
    overflow: auto;
    padding: 10px 0;
    border-bottom: 1px solid $gray-lighter;
  }
}
```

* Sample Users: Add 'faker' to Gemfile
*db/seeds.rb*
```ruby
User.create!(name: "Example User",
             email: "example@railstutorial.org",
             password: "foobar",
             password_confirmation: "foobar")

99.times do |n|
  name = Faker::Name.name
  email = "example-#{n+1}@railstutorial.org"
  password = "password"
  User.create!( name: name, 
                email: email, 
                password: password, 
                password_confirmation: password )
end
```
```bash
$ bundle install
$ bundle exec rake db:migrate:reset
$ bundle exec rake db:seed
```

* Pagination: Add 'will_paginate' and 'bootstrap-will_paginate' to Gemfile
*app/views/users/index.html.haml*
```haml
- provide(:title, "All users")
%h1 All users
= will_paginate
%ul.users
...
= will_paginate
```
*app/controllers/users_controller.rb*
```ruby
class UsersController < ApplicationController
before_action :logged_in_user, only: [:index, :edit, :update]
...
  def index
    @users = User.paginate(page: params[:page])
  end
```

* Users Index Test
*test/fixtures/users.yml*
```yml
<% 30.times do |n| %>
user_<%= n %>:
  name: <%= "User #{n}" %>
  email: <%= "user-#{n}@example.org" %>
  password_digest: <%= User.digest('password') %>
<% end %>
```
```bash
$ bundle install
$ rails generate integration_test users_index
```
*test/integartion/users_index_test.rb*
```ruby
require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:micheal)
  end

  test "index including pagination" do
    log_in_as(@user)
    get users_path
    assert_template 'users/index'
    assert_select 'div.pagination'
    User.paginate(page: 1).each do |user|
      assert_select 'a[href=?]', user_path(user), text: user.name
    end   
  end
end
```

* Partial refactoring
*app/views/users/index.html.haml*
```haml
%ul.users
  - @users.each do |user|
    = render user
```
*app/views/users/_user.html.haml*
```haml
%li
  = gravatar_for user, size: 50
  = link_to user.name, user
```
*app/views/users/index.html.haml*
```haml
%ul.users
  render @users
```

#### Deleting Users:
```bash
#Create administrator users
$ rails generate migration add_admin_to_users admin:boolean
# Add default: false in the migration file
$ bundle exec rake db:migrate
$ rails c --sandbox
>> user = User.first
>> user.toggle!(:admin) # change the value from false to true
# change db/seeds.rb to add admin: true for the first user
$ bundle exec rake db:migrate:reset
$ bundle exec rake db:seed
```
*app/views/users/_user.html.haml*
```haml
%li
  = gravatar_for user, size: 50
  = link_to user.name, user
  - if current_user.admin? && !current_user?(user)
    | #{link_to "delete", user, method: :delete, data: { confirm: "You sure?" }}
```
*app/controllers/users_controller.rb*
```ruby
class UsersController < ApplicationController
before_action :logged_in_user, only: [:index, :edit, :update, :destroy]
before_action :admin_user, only: [:destroy]
...
  def destroy
    User.find(params[:id]).destroy
    flash[:success] = "User deleted"
    redirect_to users_url
  end
...
  def admin_user
    redirect_to(root_url) unless current_user.admin?
  end
```
*test/fixtures/users.yml*
```yml
# make one user admin
  admin: true
```
*test/controllers/users_controller_test.rb*
```ruby
  test "should redirect destroy when not logged in" do
    assert_no_difference 'User.count' do
      delete :destroy, id: @user
    end
    assert_redirected_to login_url
  end

  test "should redirect destroy when logged user as non-admin" do
    log_in_as(@other_user)
    assert_no_difference 'User.count' do
      delete :destroy, id: @user
    end
    assert_redirected_to root_url
  end
```

<h2 id="cap10">Chapter 10: Account Activation and Password Reset</h2>

#### Account activation:

```bash
$ rails generate controller AccountActivations --no-test-framework
      create  app/controllers/account_activations_controller.rb
      invoke  haml
      create    app/views/account_activations
      invoke  helper
      create    app/helpers/account_activations_helper.rb
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/account_activations.coffee
      invoke    scss
      create      app/assets/stylesheets/account_activations.scss
```
*config/routes.rb*
```ruby
  resources :account_activations, only: [:edit]
```
```bash
$ rails generate migration add_activation_to_users \
> activation_digest:string activated:boolean activated_at:datetime
# edit migration file adding :activated, default: false
$ bundle exec rake db:migrate
```
*app/models/user.rb*
```ruby
class User < ActiveRecord::Base
  attr_accessor :remember_token, :activation_token
  before_save { email.downcase!}
  before_create :create_activation_digest
  ...  
  private

    # Creates and assigns the activation token and digest
    def create_activation_digest
      self.activation_token = User.new_token
      self.activation_digest = User.digest(activation_token)
    end
```
```bash
# update db/seeds.rb and test/fixtures/users.yml
$ bundle exec rake db:migrate:reset
$ bundle exec rake db:seed
# Account activation mailer method
$ rails generate mailer UserMailer account_activation password_reset
      create  app/mailers/user_mailer.rb
      create  app/mailers/application_mailer.rb
      invoke  haml
      create    app/views/user_mailer
      create    app/views/layouts/mailer.text.haml
      create    app/views/layouts/mailer.html.haml
      create    app/views/user_mailer/account_activation.text.haml
      create    app/views/user_mailer/account_activation.html.haml
      create    app/views/user_mailer/password_reset.text.haml
      create    app/views/user_mailer/password_reset.html.haml
      invoke  test_unit
      create    test/mailers/user_mailer_test.rb
      create    test/mailers/previews/user_mailer_preview.rb
```
*app/mailers/application_mailer.rb*
```ruby
class ApplicationMailer < ActionMailer::Base
  default from: "noreply@example.com"
  layout 'mailer'
end
```
*app/views/user_mailer/account_activation.html.haml*
```haml
%h1 Sample App
%p
  Welcome to the Sample App! Click on the link below to activate your account:
= link_to "Activate", edit_account_activation_url(@user.activation_token, email: @user.email)
```
*config/environments/development.rb*
```ruby
  config.action_mailer.raise_delivery_errors = false
  config.action_mailer.delivery_method = :test
  host = 'localhost:3000'
  config.action_mailer.default_url_options = { host: host }
```
*test/mailers/previews/user_mailer_preview.rb*
```ruby
# Preview all emails at http://localhost:3000/rails/mailers/user_mailer
class UserMailerPreview < ActionMailer::Preview
  # Preview this email at http://localhost:3000/rails/mailers/user_mailer/account_activation
  def account_activation
    user = User.first
    user.activation_token = User.new_token
    UserMailer.account_activation(user)
  end
  ```
*test/mailers/user_mailer_test.rb*
```ruby
class UserMailerTest < ActionMailer::TestCase
  test "account_activation" do
    user = users(:micheal)
    user.activation_token = User.new_token
    mail = UserMailer.account_activation(user)
    assert_equal "Account activation", mail.subject
    assert_equal [user.email], mail.to
    assert_equal ["noreply@example.com"], mail.from
    assert_match user.name, mail.body.encoded
    assert_match user.activation_token, mail.body.encoded
    assert_match CGI::escape(user.email), mail.body.encoded
  end
end
```
*config/environments/test.rb*
```ruby
  config.action_mailer.delivery_method = :test
  config.action_mailer.default_url_options = { host: 'example.com' }
```
*app/controllers/users_controller.rb*
```ruby
def create
    @user = User.new(user_params)
    if @user.save
      UserMailer.account_activation(@user).deliver_now
      flash[:info] = "Please check your email to activate your account."
      redirect_to root_url
    else
      render 'new'
    end
  end
```
*app/models/user.rb*
```ruby
  # Returns true if the given token matches the digest
  def authenticated? (attribute, token)
    digest = send("#{attribute}_digest")
    return false if digest.nil?
    BCrypt::Password.new(digest).is_password?(token)
  end
=begin
Create a generalized authenticated? method allows to authenticate any token (e.g. remember_digest, activation_token)
need to fix 
app/helpers/sessions_helper.rb
app/test/models/user_test.rb
=end
```
*app/controllers/account_activations_controller.rb*
```ruby
  def edit
    user = User.find_by(email: params[:email])
    if user && user.authenticated?(:activation, params[:id])
      user.update_attribute(:activated, true)
      user.update_attribute(:activated_at, Time.zone.now)
      log_in user
      flash[:success] = "Account activated!"
      redirect_to user
    else
      flash[:danger] = "Invalid activation link"
      redirect_to root_url
    end
  end
# update app/controllers/sessions_controller.rb
```
*app/models/user.rb*
```ruby
  # Activate an account
  def activate
    update_attribute(:activate, true)
    update_attribute(:activate_at, Time.zone.now)
  end
  # Sends activation email
  def send_activation_email
    UserMailer.account_activation(self).deliver_now
  end

# refactor app/controllers/users_controller.rb
# refactor app/controllers/account_activations_controller.rb
```

#### Password Reset:

```bash
$ rails generate controller PasswordResets --no-test-framework
      create  app/controllers/password_resets_controller.rb
      invoke  haml
      create    app/views/password_resets
      invoke  helper
      create    app/helpers/password_resets_helper.rb
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/password_resets.coffee
      invoke    scss
      create      app/assets/stylesheets/password_resets.scss
```
*config/routes.rb
```ruby
resources :password_resets, only:[:new, :create, :edit, :update]

# Add a link to password resets in app/views/sessions/new.html.haml
```
```bash
$ rails generate migration add_reset_to_users \
> reset_digest:string reset_sent_at:datetime
$ bundle exec rake db:migrate
$ touch app/views/password_resets/new.html.haml
```
*app/views/password_resets/new.html.haml*
```haml
- provide(:title, "Forgot password")
%h1 Forgot Password
.row
  .col-md-6.col-md-offset-3
    = form_for(:password_reset, url: login_path) do |f|
      = f.label :email
      = f.email_field :email, class: 'form-control'
      = f.submit "Submit", class: "btn btn-primary"
```
*app/controllers/password_resets_controller.rb*
```ruby  
  def create
    @user = User.find_by(email: params[:password_reset][:email].downcase)
    if @user
      @user.create_password_digest
      @user.send_password_reset_email
      flash[:info] = "Email sent with password reset instructions"
      redirect_to root_url
    else
      flash[:danger] = "Email address not found"
      render 'new'
  end
```
*app/models/user.rb
```ruby
  attr_accessor :remember_token, :activation_token, :reset_token
  ...
  # Sets the password reset attributes
  def create_reset_digest
    self.reset_token = User.new_token
    update_attribute(:reset_digest, User.digest(reset_token))
    update_attribute(:reset_sent_at, Time.zone.now)
  end

  # Sends password reset email
  def send_password_reset_email
    UserMailer.password_reset(self).deliver_now
  end
```
*app/mailers/user_mailer.rb*
```ruby
  def password_reset(user)
    @user = user
    mail to: user.email, subject: "Password reset"
  end
```
*app/views/user_mailer/password_reset.text.haml*

*app/views/user_mailer/password_reset.html.haml*

*test/mailers/previews/user_mailer_preview.rb*
```ruby
  # Preview this email at http://localhost:3000/rails/mailers/user_mailer/password_reset
  def password_reset
    user = User.first
    user.reset_token = User.new_token
    UserMailer.password_reset(user)
  end

```
* create form in *app/views/password_resets/edit.html.haml*
*app/controllers/password_resets_controller.rb*
```ruby

  before_action :get_user, only: [:edit, :update]
  before_action :valid_user, only: [:edit, :update]
  before_action :check_expiration, only: [:edit, :update]
  ...
  def create
    @user = User.find_by(email: params[:password_reset][:email].downcase)
    if @user
      @user.create_reset_digest
      @user.send_password_reset_email
      flash[:info] = "Email sent with password reset instructions"
      redirect_to root_url
    else
      flash[:danger] = "Email address not found"
      render 'new'
    end
  end

  def update
    if password_blank?
      flash.now[]:danger = "Password can't be blank"
      render 'edit'
    elsif @user.update_attributes(user_params)
      log_in @user
      flash[:success] = "Password has been reset."
      redirect_to @user
    else
      render 'edit'
    end
  end

  private

    def user_params
      params.require(:user).permit(:password, :password_confirmation)
    end

    def password_blank
      params[:user][:password].blank?
    end

    def get_user
      @user =   User.find_by(email: params[:email])
    end

    def valid_user
      unless (@user && @user.activated && @user.authenticated?(:reset, params[:id]))
        redirect_to root_url
      end
    end

    def check_expiration
      if @user.password_reset_expired?
        flash[:danger] = "Password reset has expired."
        redirect_to new_password_reset_url
      end
    end
```

#### Password Reset:

