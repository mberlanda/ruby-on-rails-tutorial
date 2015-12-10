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
```
```bash
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
