# Tuning Rails, Day 1

## Tuning page load speeds at the browser 

We glossed over some [studies](http://www.stevesouders.com/blog/2010/05/07/wpo-web-performance-optimization/) on performance gains and drops that drastically affect big players' business.

All optimizations involve tradeoffs. I mentioned the [space-time tradeoff](http://en.wikipedia.org/wiki/Space%E2%80%93time_tradeoff) as the most common kind. But, don't trade understanding your stack for convenience.

Some optimizations are not _premature_ because they cost little, are standard and bring significant speed gains.

We seeded our database with fake data using [Forgery](https://github.com/sevenwire/forgery).

We gain speed by [caching static assets in the browser](http://betterexplained.com/articles/how-to-optimize-your-site-with-http-caching/).

I showed you an example of a one-line production code change that crashed their online store. The folly was _lack of load testing_ against real production data.

Use [Google Page Speed Chrome extension](https://developers.google.com/speed/docs/insights/using_chrome) to check our compliance with [web performance best practices](https://developers.google.com/speed/docs/best-practices/rules_intro). You followed some of these recommendations to Raise Page Speed score from 25 to 90.

Use [http://www.webpagetest.org/](http://www.webpagetest.org) for deeper profiling across browsers.

Use [ShowSlow](http://showslow.com) to track frontend profile results from multple tools _over time_.

The default [Rails Asset Pipepine](http://guides.rubyonrails.org/asset_pipeline.html) minifies Javascript and generates unique filenames for expiring cached assets at deploy time.

[Gzip compress](http://betterexplained.com/articles/how-to-optimize-your-site-with-gzip-compression/) assets and Rails responses using [this one-liner](https://gist.github.com/jsierles/5035014) or the [heroku-deflater gem](https://github.com/romanbsd/heroku-deflater). The gem also caches assets with the _Cache-Control_ header and avoids compressing binary data like images.

[turbolinks](https://github.com/rails/turbolinks), default-on in Rails 4, speeds up apps by performing all GET requests via AJAX, replacing only the contents of the <body> tag.   [Why it might be dangerous to leave enabled](http://staal.io/blog/2013/01/18/dangers-of-turbolinks/).

[Deferring javascript parsing](http://css-tricks.com/thinking-async/) speeds page load. See alternative solutions to ours here. In our app, [adding async to the carousel script tag](https://github.com/tuningrails/coolspots/commit/04574fc6b2bd9072c170b876f5765d90abb81a2d) fixes loading order in Chrome. Not tested on other browsers.

## Testing web server performance 

A process is a running instance of a program. On Heroku dynos, the web server is the only type of process. 'Web server' can mean lots of things. In our case, we're referring to thin, unicorn or puma which handle requests and pass them to our application.

The Heroku routing mesh, in its Cedar stack, randomly assigns requests to dynos and doesn't provide optimizations as they did in the Bamboo stack. [RapGenius weighs in](rapgenius.com/James-somers-herokus-ugly-secret-lyrics), so does [Heroku](https://blog.heroku.com/archives/2013/2/16/routing_performance_update/) and a [sympathetic customer](http://artsy.github.com/blog/2013/02/17/impact-of-heroku-routing-mesh-and-random-routing/).

[These cute bears](http://i.imgur.com/yU7bA.gif) demonstrate the challenges facing a web request router.

A [slo-mo replay of the Facebook news feed](http://vimeo.com/17248120) to visualize how web requests look: streams of little packets.

We learned more about Heroku's [dyno](https://devcenter.heroku.com/articles/dynos), including its 512 MB memory limit, 30 second request time limit and 60 second application boot time limit.

We used the Heroku [Blitz.io](http://blitz.io) addon to benchmark our Rails app using [thin](http://code.macournoyer.com/thin/), [puma](http://puma.io) and [unicorn](http://unicorn.bogomips.org).

I touched on the different between events, threads and child processes, the request handling models used by these servers respectively. We learned that:

* Thin handles one request at a time
* Unicorn handles more requests at a time, but uses more memory
* Puma can run more requests in threads, but behaves less predictably with Rails gems

We saw high variation in results, so we tested the servers in relative isolation against a simple, predictable controller action. This helps us understand why it makes sense to test in isolation. *We want to know the optimal baseline web server setup before testing deeper into our stack*. In this setup, some of you found Puma performed best.


## Random discussions

At lunch, we touched on Netflix's [CHAOS MONKEY](http://techblog.netflix.com/2012/07/chaos-monkey-released-into-wild.html), an agent of destruction designed to test the resilience of production infrastructure.

One of you asked about a tool for [speeding up asset precompliaton](https://github.com/ndbroadbent/turbo-sprockets-rails3). See instructions for [Heroku](https://github.com/ndbroadbent/turbo-sprockets-rails3).


## Things we'll cover tomorrow on Day 2

* [HTTP caching for Rails responses](https://devcenter.heroku.com/articles/http-caching-ruby-rails#timebased-cache-headers)
* [Rack::Cache](https://devcenter.heroku.com/articles/rack-cache-memcached-rails31) for storing and serving assets, public responses
* Tools for integrating performance profiling in your development workflow: [mini-profiler](https://github.com/SamSaffron/MiniProfiler/tree/master/Ruby), [Rails Performance Tests](http://guides.rubyonrails.org/performance_testing.html)
* Moving work into the background with Resque and Sidekiq

## Useful commands

rake middleware: see which classes process your full Rails request

mate \`bundle show heroku-deflater\`: open this gem in Textmate. Note backticks, not quotes.