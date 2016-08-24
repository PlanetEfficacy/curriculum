---
layout:
title: Rush Hour
---

## Abstract

In this project you'll use Ruby, Sinatra, and ActiveRecord to build a web traffic tracking and analysis tool.

RushHour will be an application that aggregates and analyzes visitor data from another website. A RushHour customer/client will embed JavaScript in their website that will gather and send their visitor data to our site. It is important to note that we will not be creating this JavaScript. Instead we will simulate the process of gathering and receiving data, which we will call a payload. Our job is to build the application that can accept the submission of these payloads, analyze the data submitted, and display it through a HTML interface.

We will use pre built payloads to simulate the gathered data from a customer/client's website. They will look like this:

```
payload = '{
  "url":"http://jumpstartlab.com/blog",
  "requestedAt":"2013-02-16 21:38:28 -0700",
  "respondedIn":37,
  "referredBy":"http://jumpstartlab.com",
  "requestType":"GET",
  "userAgent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1309.0 Safari/537.17",
  "resolutionWidth":"1920",
  "resolutionHeight":"1280",
  "ip":"63.29.38.211"
}'
```

The payloads are in a hash-like format called JSON. You will need to learn how to interact with JSON in ruby. Find the Ruby JSON docs [here](http://www.ruby-doc.org/stdlib-2.0/libdoc/json/rdoc/JSON.html).

RushHour will simulate sending these requests using a cURL command. This is a command we can run in our terminal that sends an HTTP request. You can checkout the details of the cURL command by running `curl --manual` in your terminal.


## Learning Goals

* Understand how web traffic works
* Dig into HTTP concepts including headers, referrers, and payload
* Design a normalized SQL-based relational database structure
* Use ActiveRecord to interface with the database from Ruby
* Practice fundamental database storage and retrieval
* Understand and practice HTTP verbs including GET, PUT, and POST
* Practice using fundamental HTML and CSS to create a usable web interface

## Getting Started

### Clone the Project

1. One team member forks the skeleton repository at [https://github.com/turingschool-examples/rush-hour-skeleton](https://github.com/turingschool-examples/rush-hour-skeleton)
2. Add the other team members as collaborators
3. Add instructors as collaborators

### Requirements

The project must use:

* [Sinatra](http://www.sinatrarb.com/)
* [PostgreSQL](http://www.postgresql.org/)
* [ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html)

You'll want to set up the [DatabaseCleaner](https://github.com/DatabaseCleaner/database_cleaner) gem in order to have a clean database each time you run tests. Follow the instructions for setting up the gem. Due to a bug in the most recent version of the gem, you'll need to use this line when you set the strategy in your test helper file:

```ruby
  DatabaseCleaner.strategy = :truncation
```

__You will also probably want to set up a [rake task to run your tests](http://ruby-doc.org/stdlib-2.0/libdoc/rake/rdoc/Rake/TestTask.html).__

See the "Resources" section at the bottom of this page for additional helpful documentation.

Before starting, make sure you have the [Postgres App](http://postgresapp.com/) installed.

### Restrictions

The project may not use:

* `Rails`

## Instructions

Iterations 0-8 must be completed to consider the project complete. Please use TDD every step of the way. Tests are expected for all features and all the models - including iterations 0-1.

* __NOTE__ - Some of the work done in early iterations of this project may not make it into the final production code. And that's ok - it's to be expected that early work in most projects may not make the final cut.


## Base Expectations:

### Iteration 0

The core idea behind RushHour is that your application will store and analyze data from a clients website about visitors to their site. For iteration 0 let's begin by storing some of that data.

To store data with ActiveRecord/Postgresql we need to create migrations and a model for a particular resource. In this example we have a PayloadRequest resource, so we need a ```PayloadRequest``` model, and a migration that will create a ```PayloadRequest``` table with the necessary attributes.

Our payload looks like this:

```
payload = '{
  "url":"http://jumpstartlab.com/blog",
  "requestedAt":"2013-02-16 21:38:28 -0700",
  "respondedIn":37,
  "referredBy":"http://jumpstartlab.com",
  "requestType":"GET",
  "userAgent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1309.0 Safari/537.17",
  "resolutionWidth":"1920",
  "resolutionHeight":"1280",
  "ip":"63.29.38.211"
}'
```

* Create a migration that creates a ```PayloadRequest``` table that has a column for each of the attributes.

Now that we have a database table for the ```PayloadRequest``` we start with our model.

Let's create a new file within our model directory - `payload_request.rb`. Don't forget to inherit from `ActiveRecord::Base`. The start of the file should look something like this:

```ruby
class PayloadRequest < ActiveRecord::Base

end

```

If we didn't inherit from ActiveRecord::Base we wouldn't get the ActiveRecord database query methods that are nice and easy to work with.

Now that we have a PayloadRequest model started, finish it off by creating validations for the PayloadRequest attributes.

* All the attributes must be present in the request.

You can use ActiveRecord's [validations feature](http://guides.rubyonrails.org/active_record_validations.html) to make sure no record is saved without having all attributes present.


### Iteration 1


Now that we have our basic database design in place, we can see that it isn't quite normalized. Our ```PayloadRequest``` violates _normal form_ and is structured in a way that will generate a lot of repetitive data. What's __normal form__ you ask? You can learn more [here.](https://gist.github.com/Carmer/f9e060bf1ac30e3ab7b3) Extract the data necessary to normalize the database so far. Do this by creating migrations, models and establishing appropriate relationships between models.

[This tool](http://ondras.zarovi.cz/sql/demo/) is a database modeling tool that can help you quickly design and iterate on your database design.


### Iteration 2

Hopefully our database design is looking better. Now, let's start to manipulate some of that data we're storing.

We want to analyze all the payload requests for the following stats. Some methods will be built directly on the PayloadRequest model, while other methods will be built __in the most appropriate class__. For example:

```ruby
class Url  < ActiveRecord::Base
  has_many :payload_requests

  def self.most_to_least_requested
    #implement this method :)
  end
end
```

* Average Response time for our clients app across all requests
* Max Response time across all requests
* Min Response time across all requests
* Most frequent request type
* List of all HTTP verbs used
* List of URLs listed from most requested to least requested
* Web browser breakdown across all requests(userAgent)
* OS breakdown across all requests(userAgent)
* Screen Resolutions across all requests (resolutionWidth x resolutionHeight)

Our client also finds it valuable to have stats on specific URLs. For a specific URL, let's find the following Stats:

* Max Response time
* Min Response time
* A list of response times across all requests listed from longest response time to shortest response time.
* Average Response time for this URL
* HTTP Verb(s) associated with this URL
* Three most popular referrers
* Three most popular user agents. We can think of a 'user agent' as the combination of OS and Browser.

### Iteration 3

Now that we've set up a basic app that can store data from a client, let's expand the functionality so we can support multiple clients.

We already have a `PayloadRequest` model and database table, and we know that a `PayloadRequest` will belong to a `Client`, and a `Client` will have many `PayloadRequests`. That means we need to figure out a way to store `Client` data and somehow relate that to our `PayloadRequest` data.

A Client has two attributes, an `identifier`, and a `rootUrl`.

Create migrations to do the following:
* Create the `Client` table with the necessary attributes
* Create a migration to add a reference to the `Client` on the `PayloadRequest` table. This migration will establish the one-to-many relationship that `PayloadRequest`s and `Client`s have.

Now that we have a place to store out client data, make sure you go into the models and establish the relationships between `PayloadRequest`, and `Client`, and you set up appropriate validations for the `Client`.

Join the client table with its resources. For example:

```ruby
class Client < ActiveRecord::Base
  #More Code Here
  has_many :payload_requests
  #More Code Here
end
```

If your schema utilizes a join table to connect resources, make sure you remember to link the resources with `:through`. Take a look at [this documentation on active record associations](http://guides.rubyonrails.org/association_basics.html#the-has-many-through-association) to get it to work.

### Iteration 4

Now let's get into the nitty gritty that is the internet. Currently our app works by feeding it data directly, but that's not how we plan for it to be used in the real world. We want our app to be accessible by feeding it data from an external client/source.

First, let's have clients register their application by submitting a post request to the following address:

```
http://yourapplication:port/sources
```

so while you're in development it will looks something like this:

```
http://localhost:9393/sources
```

The parameter that we will require a client to pass will be:

* identifier
* rootUrl

We can send a request with this specific information via the Terminal and the `curl` command. Check out how to use the `curl` command via your Terminal by typing `man curl`. This will bring up the manual for `curl`.

We will send a request like this:

```
$ curl -i -d 'identifier=jumpstartlab&rootUrl=http://jumpstartlab.com'  http://localhost:9393/sources
```

Wondering what `-i` and `-d` mean? Check the manual.


A post to `http://yourapplication:port/sources``` will require one of three possible responses from our application.

* 1. Missing Parameters - 400 Bad Request

If missing any of the required parameters return status `400 Bad Request` with a descriptive error message.

Wondering how to send back a status code from a Sinatra app? Check out the [Sinatra docs](http://www.sinatrarb.com/intro.html).

* 2. Identifier Already Exists - 403 Forbidden

If that identifier already exists return status `403 Forbidden` with a
descriptive error message.

* 3. Success - 200 OK

When the request contains all the required parameters return status `200 OK` with the following data for the client:

```
{"identifier":"jumpstartlab"}
```

* identifier - the unique identifier for the application that has been created for the client.

### Iteration 5

After completing iteration 4, we can now register Clients and their applications. However, there is still no way to get their data into our application. Let's change this by adding an endpoint for Clients to post their payload data.

A registered application will send `POST` requests to the following URL:

```
http://yourapplication:port/sources/:IDENTIFIER/data
```

IDENTIFIER, in this URL, is the unique identifier for the client.

If you recall from iterations 0 and 1 we've already structured our app to accept payload data. If everything was set up correctly we should not need to change much if anything for this to work with the payload being sent over HTTP.

Everything sent over HTTP by nature is a string. That makes JSON structure perfect for sending data over HTTP. We will send our payload request as a parameter called 'payload' which contains the payload as JSON data.

Here is an example of sending a payload to our application:

```
curl -i -d 'payload={"url":"http://jumpstartlab.com/blog","requestedAt":"2013-02-16 21:38:28 -0700","respondedIn":37,"referredBy":"http://jumpstartlab.com","requestType":"GET","userAgent":"Mozilla/5.0 (Macintosh%3B Intel Mac OS X 10_8_2) AppleWebKit/537.17 (KHTML, like Gecko) Chrome/24.0.1309.0 Safari/537.17","resolutionWidth":"1920","resolutionHeight":"1280","ip":"63.29.38.211"}' http://localhost:9393/sources/jumpstartlab/data
```

Find the Ruby JSON docs [here](http://www.ruby-doc.org/stdlib-2.0/libdoc/json/rdoc/JSON.html).

Our application should process the request with one of the 4 following outcomes.

* 1. __Missing Payload__ - 400 Bad Request
  * If the payload is missing, return status `400 Bad Request` with a descriptive error message.
* 2. __Already Received Request__ - 403 Forbidden
  * If the request payload has already been received return status `403 Forbidden` with a descriptive error message.
* 3. __Application Not Registered__ - 403 Forbidden
  * When data is submitted to an application URL that does not exist, return a `403 Forbidden` with a descriptive error message.
* 4. __Success__ - 200 OK
  * When the request contains a unique payload return status `200 OK`.

### Iteration 6

Now that we have a site up and endpoints for our clients to register their applications and submit payload data for their applications, our clients want a place to go to view the statistics we have generated for their applications.

We'll want an endpoint for a client to see their aggregate site data:

```
http://yourapplication:port/sources/:IDENTIFIER
```

When the IDENTIFIER exists and a Client goes to their endpoint they should be able to view statistics for:

* Average Response time across all requests
* Max Response time across all requests
* Min Response time across all requests
* Most frequent request type
* List of all HTTP verbs used
* List of URLs listed form most requested to least requested
* Web browser breakdown across all requests
* OS breakdown across all requests
* Screen Resolutions across all requests (resolutionWidth x resolutionHeight)

When an identifier does not exist return a page that displays the following:

* Message that the identifier does not exist

When an identifier does exist, but no payload data has been submitted for the source.

* Message that no payload data has been received for this source

__Keep in mind the difference between instance methods and class methods in ActiveRecord. A class method will be looking at all of the records in your database, and an instance method is already referring to a particular instance/row of data. If we know a particular instance we can easily use ActiveRecord to find any of the related objects.__

### Populate/Seed data

Now that you have everything set up and ready to go for accepting data submitted by clients, you may notice that it kind of sucks to have to write a cURL command for every payload. [Here](https://github.com/turingschool/rush_hour_test_script) you will find the test script that will send a bunch of clients info and a bunch of payloads. Follow the directions and with one command you will have a bunch of data for your app.

### Iteration 7

We also have stats we generated that are specific to a Clients URLs. Let's create a view that will show our URL specific stats.

The URL we will create for this will be:

```
http://yourapplication:port/sources/:IDENTIFIER/urls/:RELATIVEPATH

Examples:

http://yourapplication:port/sources/jumpstartlab/urls/blog
http://yourapplication:port/sources/jumpstartlab/urls/article/1
http://yourapplication:port/sources/jumpstartlab/urls/about
```

First - let's set up our client's specific statistics to have the URLs link to their respective URL specific page.

on the page that is found at this endpoint:

```
http://yourapplication:port/sources/:IDENTIFIER
```

make sure you have: Hyperlinks of each url to view url specific data.


If the url for the identifier __DOES__ exist let's display the url specific stats in this view:

* Max Response time
* Min Response time
* A list of response times across all requests listed from longest response time to shortest response time.
* Average Response time for this URL
* HTTP Verb(s) associated used to it this URL
* Three most popular referrers
* Three most popular user agents

If the url for the identifier __DOES NOT__ exist:

* Display a message that the url has not been requested

### Iteration 8

Our client would like to have a 24hour breakdown of their specific events:

```
http://yourapplication:port/sources/IDENTIFIER/events/EVENTNAME

Examples:

http://yourapplication:port/sources/jumpstartlab/events/startedRegistration
http://yourapplication:port/sources/jumpstartlab/events/addedSocialThroughPromptA
http://yourapplication:port/sources/jumpstartlab/events/addedSocialThroughPromptB
```

Implement a hour by hour breakdown of when the event was received. How many were shown at noon? at 1pm? at 2pm? Do it for all 24 hours. Also, display on this page the overall number of times the specific event was received.

When the event has not been defined:
Display a message that no event with the given name has been defined and then a hyperlink to the Application Events Index.


## Extensions

### Javascript to Send PayloadRequest

Currently the only way to get data to our app is to:

1.) Use the browser to send a request through the user interface

2.) send a cUrl request from our terminal to the correct endpoint.

For this extension, write some JavaScript/AJAX that you can give a client to embed in their site that will automate sending the PayloadRequest for a specific page from the client's site to your app.

### Deleting Account

For this extension implement a feature that allows a customer to delete their account and all their associated data. Be sure to only allow a client to delete their own account.

## Evaluation Rubric

The project will be assessed with the following rubric:

#### 1. Functional Expectations

* 4: Application fulfills base expectations and adds two extensions
* 3: Application fulfills base expectations
* 2: Application has some small missing base functionality
* 1: Application is not usable

#### 2. Test-Driven Development

* 4: Application is broken into components which are well tested in both isolation and integration using appropriate data
* 3: Application is well tested but does not balance isolation and integration/feature tests
* 2: Application makes some use of tests, but the coverage is insufficient
* 1: Application does not demonstrate strong use of TDD

#### 3. Encapsulation / Breaking Logic into Components

* 4: Application is expertly divided into logical components each with a clear, single responsibility
* 3: Application effectively breaks logical components apart but breaks the principle of SRP
* 2: Application shows some effort to break logic into components, but the divisions are inconsistent or unclear
* 1: Application logic shows poor decomposition with too much logic mashed together

#### 4. Fundamental Ruby & Style

* 4: Application demonstrates excellent knowledge of Ruby syntax, style, and refactoring
* 3: Application shows strong effort towards organization, content, and refactoring
* 2: Application runs but the code has long methods, unnecessary or poorly named variables, and needs significant refactoring
* 1: Application generates syntax error or crashes during execution

#### 5. Sinatra / Web and Business Logic

* 4: Application takes advantage of all the features Sinatra has to offer and effectively separates the web application from the business logic.
* 3: Application makes good use of Sinatra but has some mixing of the web and business logic.
* 2: Application has web and business logic totally mixed together
* 1: Application demonstrates a weak understanding of Sinatra and how applications should be built.

#### 6. View Layer

* 4: Application expertly breaks components out to view partials and makes use of both built-in and custom-written view helpers.
* 3: Application breaks components out to view partials but has some logic or complexity leaking into the view
* 2: Application has messy views that mix logic and presentation
* 1: Application shows a lack of understanding around view templates and how they should be used/constructed.

While you won't be graded on workflow for Rush Hour, here's an example of how you will be evaluated on workflow later in the module:

#### Workflow (NOT GRADED)

* 4: Excellent use of branches, pull requests, and a project management tool.
* 3: Good use of branches, pull requests, and a project-management tool.
* 2: Sporadic use of branches, pull requests, and/or project-management tool.
* 1: Little use of branches, pull requests, and/or a project-management tool.
