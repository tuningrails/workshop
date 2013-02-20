# Tuning Rails Workshop

For all the techniques we'll learn in this workshop, there are tradeoffs we need to be aware of. At the end of each lab, we'll discuss those tradeoffs and how to be aware of them. Let's think of responsiveness as our goal, and focus on the critical path: the places in our app where users feel the most pain from lack of responsiveness.

All web performance tuning comes down to two core concepts: Moving work out of our critical application path, and reducing work by saving its results somewhere.

With that in mind, here's my planned outline for our class. We may not get to it all, so let me know what most interests from the following topics. Thanks!

## Monday

To kick off this workshop, we'll discuss:

* How different ways of perceiving speed informs performance tuning
* The importance of accurate measurement in performance tuning
* Common measurement misunderstandings and mistakes like trusting averages, and what alternatives exist
* Clarifying performance terms like response time, load time and throughput
* What the Heroku platform looks like under the hood any why it matters
* When optimization is not premature

We should discuss these topics and share our experiences. These concepts will permeate the entire course.

Then we'll get down and dirty with a slow Rails app deployed on Heroku. We'll start from the browser, moving deeper into the stack.

### The page load: In-browser Caching, compression and page load score

Using Google Chrome developer tools, we'll learn:

* How to measure page load speed
* Names and function of key HTTP headers for cacheing and compression
* The 'gold standard' rules of page load optimizations
* How a CDN (content delivery network) can improve responsiveness
* Useful tools for measuring across different browsers 
* How Rails caches by default and what we can do with its built-in browser cacheing methods
* Modern page navigation with pushState

Your challenges:

* Use the command line to automate a page load test
* Tune the Rails configuration to bring the page load score as high as possible
* improve app responsiveness by cacheing a public page in the browser
* Speed up navigation by using pushState
* Use AJAX to update dynamic sections of a cached page
 
### The web server: Load benchmarking, tuning for optimal throughput and conditional page caches

Using some free load testing tools, we'll learn:

* How different web servers work and why it matters: thin, unicorn and puma
* Limitations of the Heroku platform: random request routing and memory limits
* How Rails boot time, and your app size, affects performance
* How to interpret application log reports
* How to interpret graphs from two performance tuning tools
* Conditional page cacheing to avoid passing requests through Rails

Your challenges:

* Try different web server configurations to get the best throughput from our app in production
* Make a case for why one configuration is better than another
* Add code to our app that sends custom performance tests to a performance graphing service
* Slim our app down for faster boot times
* Save our site from crashing by cacheing a public page in Rack::Cache
* Configure Rails to store page caches in a Heroku memcache add-on

## Tuesday

We'll move down the stack into the application itself. We'll cover different techniques to alleviate or move application work.

### Reducing work by caching HTML

Assisted by our growing suite of performance testing tools, we'll learn:

* How cache expiration and 'russian doll caching' work
* Where to store cache and how it could affect app availability
* How cache size affects performance
* When caching is more of a band-aid than a solution
* How Rails built-in performance tests help track performance tuning

Your challenges:

* Identify good candidates in our app for caching based on research in New Relic
* Setup the Rails cache to store in a Heroku memcache add-on
* Cache HTML on a specific page and ensure the cache expires under certain conditions
* Write Rails performance tests as proof of improved speed

### Moving work into the background

Focusing on the biggest jobs in our application, like sending lots of emails or importing data files, we'll learn:

* Different ways we could move work into the background: internal worker threads vs external work queues
* Practical differences between popular job processing libraries: run_later/sucker_punch (threads only), resque (processes and threads),  sidekiq (both)
* What kinds of work are good candidates for immediate backgrounding
* Common pitfalls like duplicated jobs, lost jobs and memory bloat

Your challenges:

* Identify a slow spot in the app and rewrite it to process the work in the background
* Write code to prevent job duplication
* Write code to avoid lost jobs due to errors or timeouts

## Wednesday

We'll look two types of databases: Postgresql and Redis, and we'll compare and debate the results of our tuning efforts.

We'll learn:

* How to read Postgres query analyses (EXPLAIN statements)
* Basic concepts of database usage: in-memory access and disk usage
* What kinds of indexes you should usually create
* How a key-value store like Redis can help alleviate database write load
* What database slaves are and how they fit into performance work
* Three Rails techniques for tuning queries: eager loading, counter caches and selecting specific data
* Why things like 'cascading deletes' can slow your app down
* Why raw SQL is OK

Your challenges:

* Identify our app's slowest queries and tune them by adding indexes
* Identify unnecessary queries and implement eager loading and counter caches as needed
* Speed a specific page up by reducing the amount of data retrieved from Postgres
* Work out how to stop a user deletion from locking the app up
* Reimplement the slow 'online user tracking' in Redis
* Choose the quickest method to import a large data file
* Create a database slave to take over some heavy read queries

### Debate and Review

If we have time, I'd love to discuss how you feel about your tuning work on this application, your real world experiences with performance problems and how you think you can apply these techniques to them.