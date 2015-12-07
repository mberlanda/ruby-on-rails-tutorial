# Ruby on Rails Tutorial: sample application

This is the sample application for the 
[*Ruby on Rails Tutorial:
Learn Web Development with Rails*] (http://www.railstutorial.org/)
by [Micheal Hartl] (http://www.michealhartl.com/).

Initial commit:
*  $ rails new sample_app
*  # edit GemFile
*  $ bundle update
*  $ git mv README.rdoc README.md

 Generate StaticPages:
 * $ git checkout master
 * $ git checkout -b static-pages
 * $ rails generate controller StaticPages home help