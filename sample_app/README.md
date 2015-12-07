# Ruby on Rails Tutorial: sample application

This is the sample application for the 
[*Ruby on Rails Tutorial:
Learn Web Development with Rails*] (http://www.railstutorial.org/)
by [Micheal Hartl] (http://www.michealhartl.com/).

Initial commit:
*  $ rails new sample_app
*  # edit GemFile
*  $ bundle update
*  $ mv README.rdoc README.md

Generate StaticPages:
* $ git checkout master
* $ git checkout -b static-pages
* $ rails generate controller StaticPages home help
* $ git push --set-upstream origin static-pages

Custom StaticPages:
* edit app/views/static_pages/home.html.erb
* edit app/views/static_pages/help.html.erb

Getting Started with Testing:
* $ ls test/controllers
* look at static_pages_controller_test.rb
* a. comment out 'guard-minitest'
* b. install guard
* b. $ bundle exec rake db:migrate #if the app is using the database
* $ bundle exec rake test
* add test "should get about" in class StaticPagesControllerTest

StaticPagesControllerTest#test_should_get_about:

* error 1:
  ActionController::UrlGenerationError: No route matches {:action=>"about", :controller=>"static_pages"}
* solution 1:
  config/routes.rb add get 'static_pages/about'

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