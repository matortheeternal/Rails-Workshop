# Rails-Workshop
SBCC Workshop for Ruby on Rails.


## Installation instructions

### Step 1:
Download [MySQL 5.6](https://dev.mysql.com/downloads/mysql/5.6.html)
(do the x64 zip installer)

### Step 2:
Extract the folder from the archive wherever you want. I'd recommend using folder like `C:\dev\db\`.

### Step 3:
Download [Ruby 2.0.0-p647 (x64) and the mingw 64-bit devkit](http://rubyinstaller.org/downloads/)

### Step 4:
Run the ruby installer and make sure to check the box to add ruby executables to your path.  
I recommend installing in a folder like `C:\dev\lang\`.  
Extract the devkit in the ruby folder in a folder called devkit.  
Follow the [Quick Start instructions for setting up the devkit](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit).  
Make sure to edit the `config.yml` file to add your ruby path if it wasn't found automatically.

### Step 5:
Add the mysql bin folder to your system's [%path% environment variable](https://dev.mysql.com/doc/mysql-windows-excerpt/5.1/en/mysql-installation-windows-path.html).
Run the mysqld service using `Win+R`.

### Step 6:
Run a command window in any directory, and type `gem install rails '~> 4.2.5'`.  You'll have to wait a while for it to fully download and install all required packages.

### Step 7:
You're good to go!


## Creating a new project
### Step 1:
Open the command line in the folder where you want to store your projects.  E.g. My Documents\Projects.  You can open the command line a folder quickly by holding shift, right-clicking, and choosing "open command window here".

### Step 2:
Type `rails new ProjectName` replacing ProjectName with the name you want to use for your project.

### Step 3:
Use cd to enter into the project directory.  E.g. `cd ProjectName`

### Step 4:
Configure your `gemfile` with a text editor or install the one I have provided in this repository.  A gemfile is a text file which lists the "gems" to be used by your project.  Gems are essentially libraries - packages of code - that can be used to speed up your project.  E.g. the Devise gem provides a full solution for user authentication.

### Step 5:
Configure your `database.yml` file with your database information.  We'll be using the `mysql` database for this workshop, but Rails will allow you to use other databases with your web application just as well.  A template `database.yml` file is provided with this repository.

### Step 5:
Type `bundle install`.  This will install any missing dependencies (gems) and link them to your project.  You can use `bundle update` in the future to update existing gems to newer versions.

### Step 6:
Use the `rails g scaffold` to generate resources to be served up by your web application.  For instance, a person record could be generated with the command `rails g Person first_name:string last_name:string` would generate a number of files and routes so you can store records in your database with both a `first_name` and a `last_name` field and perform basic CRUD (Create, Read, Update, Delete) on them from your web application.

### Step 7:
Run `rake db:migrate` to update your database.

### Step 8:
Run `rails s` to start the Ruby on Rails server.  You can access it from your web browser at [localhost:3000](http://localhost:3000).  You'll be able to access the pages creates through scaffolding by going to the following routes:

* `/people`: Will display an index of all people in your database in the form of a table.  This page shouldn't display anything until you create some person records.
* `/people/:id`: Replace `:id` with the id of the person record you wish to view.  This page is the "Show" page for a single person record.
* `/people/:id/edit`: Allows you to edit a person record with the id `:id`
* `/people/:id/new`: Allows you to create a new person record.

Note that all of these routes (and a destroy route, which I didn't mention) are created for resources when you scaffold them.


## Routing in Rails
A route is a path a client can go to in their web browser to access some content.  Ruby on rails defines routes for your web application in `config\routes.rb`.  When you use `rails g scaffold`, you'll notice that the only route created is a `resources people` one.  This is actually a macro for a number of routes, listed below:

* `match "/people", to: "people#index", via: :get`
* `match "/people/:id", to: "people#show", via: :get`
* `match "/people/:id/new", to: "people#new", via: :get`
* `match "/people/:id/edit", to: "people#edit", via: :get`
* `match "/people/:id", to: "people#update", via: :post`
* `match "/people", to: "people#create", via: :post`
* `match "/people/:id", to: "people#destroy", via: :delete`

This format for a route has three major parts.  The first part is the route itself, which is the path the user navigates to in their web browser for the route to be triggered.  The second part is the controller action to handle the route.  In Ruby on Rails objects called "controllers" are what handle what should occur when a route is triggered.  More on them in the next section!  The last part is the [HTTP verb](http://www.restapitutorial.com/lessons/httpmethods.html) to match to the route. 

### Other resources
* [Ruby on Rails Guide to Routing](http://guides.rubyonrails.org/routing.html)

## Controllers in Rails
Rails works under a model-view-controller model (MVC).  This is an architectural pattern.  You can read more about it [here](http://www.tutorialspoint.com/ruby-on-rails/rails-framework.htm).

I'll give a really practical overview of controllers.  Open the `app\controllers\people_controller.rb` file generated from scaffolding in your text editor.

### Line 1: Class Declaration
`class peopleController < ApplicationController`

This declares the `peopleController` class and says it inherits from the `ApplicationController`.  You generally won't change this line unless you make a controller which does some actions which you want other controllers to be able to do too, then you can inherit from that controller instead of `ApplicationController`.

### Line 2: Before Action
`  before_action :set_person, only: [:show, :edit, :update, :destroy]`

This line says that, before executing the methods show, edit, update or destroy, execute the method set_person.  You can use before actions as a means of controlling what users can do (authorization) or to load shared resources (which is what the `set_person` method does).

Another note: in Ruby you can declare an immutable (unchangeable) string by prepending a colon.  These are called symbols.  E.g. `:this_is_a_symbol`.  You generally want to use these for internal logic, but you can use ordinary strings just as well (the [difference is with memory](http://stackoverflow.com/questions/16621073/when-to-use-symbols-instead-of-strings-in-ruby), and is more in depth than this workshop will be).

### Line 4: Some generated comments
`# GET /people`
`# GET /people.json`

The scaffold action will generate these comments to help you understand how the routes and the controller actions interact.  I'd say it's good practice to have these, in general.

In Ruby you use the hash character `#` to designate a comment.

### Line 6: Index action
`  def index`

In Ruby you use the format `def` to define a method.  You then put the name of the method and can define parameters it takes in parenthesis (like most languages).

### Line 7: Load data
`  @people = Person.all`

This line loads all Person records from the database.  Capital P `Person` is a reference to the person model, which is the object which represents/interacts with Person records in your database.

### Line 9: Respond to format
`  respond_to do |format|`

This sets up a block of code which tells what the server should do depending on the data format requested by the user.  E.g. HTML, JSON, PlainText, XML, CSV, etc.

Inside of this block you'll see lines like `format.html` or `format.json`, followed by nothing (which causes the default render action to be performed), or by a render action in a code block (which is designated by squiggly braces like Java and C-like languages).

`  format.json { render :json => @people }`

This line says: "if the requested data format is [JSON](https://en.wikipedia.org/wiki/JSON), render (to the user) :json data for the `@people` data object.  You can see this route in action by navigating to the route `/people.json`.

### Params
Skipping forward a bit, you may notice that in the private methods at the bottom of the controller we reference the `params` variable.  This is a variable that is accessible in every controller which is the collection of params being sent to the user.  (e.g. from form inputs, or from the `:id` input defined in the route (as discussed earlier).  You can also set params through URL [query strings](https://en.wikipedia.org/wiki/Query_string).

### The rest
I could continue through the file - there's a lot to talk about here!  The general idea is more of the same, however, and we're going to step into the models next.  The only other really important thing I want to mention here is the `def person_params` method.  This is a method that leverages something called strong parameters, which only allows the controller to process parameters you define (which minimizes the chances of exploits or errors).

## Models
The model is what interfaces with the database.  This is the piece of code that does the heavy lifting in the platform.  The model defines the relationships between different data in your database (not important right now because we only have one piece of data, but much more important later on!).  You can find the person model in `app\models\person.rb`.  You'll note that this file is pretty empty.  More on that next.

### Attributes
Rails used to require you to define the attributes for the models with `attr_accessible`.  But, as of Rails 4, you don't have to do that.  Instead, the models just *magically* interface with your database and create methods for getting/setting all the values from the database.  So, if the Person record has a first_name and a last_name column, you can access those from an individual person record `@person` with `@person.first_name` and `@person.last_name`.  

### Methods
There are a huge number of standard ActiveRecord methods for finding, creating, saving, and deleting records.

I urge you to check out [Active Record Basics](http://guides.rubyonrails.org/active_record_basics.html) for more information on what is provided through the Active Record interface, which is a huge part of what makes Rails awesome.


## Views
Alright, last thing is views.  Views are what you render for the user.  These are usually HTML documents.  You can find them in `app\views\person\`.  You'll note there are multiple views here, but they aren't complete HTML documents.  This is because views are always rendered inside of a layout.  You can find layouts in `app\views\layouts\`.  The default layout is `application.html.erb`.

You'll note that the extension for these views is `.html.erb`, not just `.html`.  That `html.erb` files are HTML files that use ERB (embedded ruby) templating.  You can read more about ERB templating [here](http://www.stuartellis.eu/articles/erb/).

ERB templting allows you to embed data into your views.  You can treat these files as ordinary HTML files that occassionally execute ruby code inside of `<% %>` tags.  In the view you can access any instance variables you defined in the controller action that renders the view.  NOTE: The associated controller action directly matches the view filename (this is called the default view).  Unless you specifically tell ruby to `render` a view, it will render the default view associated with the controller action.


# Updating
I'll be updating this document more in the near future, but I think this covers some of the ideas.  You can generate more data, edit the HTML files, or edit the asset files (e.g. stylesheets).  There's a lot of good resources out there for learning about Ruby/the Rails framework, so don't be afraid to [google](http://google.com)!
