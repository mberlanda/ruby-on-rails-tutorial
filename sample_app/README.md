# Ruby on Rails Tutorial: sample application

This is the sample application for the 
[*Ruby on Rails Tutorial:
Learn Web Development with Rails*] (http://www.railstutorial.org/)
by [Micheal Hartl] (http://www.michealhartl.com/).

**Chapter 3: Mostly Static Pages**

branch: *static-pages*, *static-pages-exercises*

Initial commit:
```bash
$ rails new sample_app
# edit GemFile
$ bundle update
$ mv README.rdoc README.md
```
Generate StaticPages:
```bash
$ git checkout master
$ git checkout -b static-pages
$ rails generate controller StaticPages home help
$ git push --set-upstream origin static-pages
```

Custom StaticPages:
* edit *app/views/static_pages/home.html.erb*
* edit *app/views/static_pages/help.html.erb*

Getting Started with Testing:
* $ ls test/controllers
* look at *static_pages_controller_test.rb*
* a. comment out 'guard-minitest'
* b. install guard
* b. $ bundle exec rake db:migrate #if the app is using the database
* $ bundle exec rake test
* add test "should get about" in class StaticPagesControllerTest

StaticPagesControllerTest#test_should_get_about:

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

Slightly Dynamic Pages:
* # Red-Green-Refactor cycle
* $ mv app/views/layouts/application.html.erb layout_file
* assert_select "title", "Home | Ruby on Rails Tutorial Sample App"
* add the page title: <% provide(:title, "Home") %>
* $ mv layout_file app/views/layouts/application.html.erb
* add to layout_file: <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
* root the home page in config/routes.rb

Finish Static Page:
* merge static-pages and master branch

Minitest Reporters:
*test/test_helper.rb*
```ruby
require "minitest/reporters"
Minitest::Reporters.use!
```

**Chapter 4: Rails-Flavored Ruby**

branch: *rails-flavored-ruby*

Title Application Helper:

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

Example User Class:

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

**Chapter 5: Filling in the Layout**

branch: *filling-in-layout*

Adding Some Structure:

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

Bootstrap and Custom CSS:

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

Partials:

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

Sass and the Asset Pipeline:
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