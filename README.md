# Async Rails 3 stack demo

Simple async demo stack with Rails 3 + EventMachine and Fibers.

 * Hit localhost:3000/widgets to do a 1s async mysql query
 * Hit localhost:3000/widgets/http to make an HTTP call back to /widgets - recursive! :-)
 * Hit localhost:3000/twitter to load a mounted async Sinatra app (reports latests rails 3 tweets)

Howto / example commits:

  * [Convert ActiveRecord to use async mysql driver](http://github.com/igrigorik/async-rails/commit/72ea38433246cc58cd31e3863f4ed4e0c861ad28)
  * [Use async HTTP fetching within Rails](http://github.com/igrigorik/async-rails/commit/6307f3f416f21a40304d2f4a07509b923051744b)
  * [Mount async Sinatra app](http://github.com/igrigorik/async-rails/commit/50c5e4fd6701dfa2b3ecfc697ca53b40f8c57827)

Requirements:

 * Ruby 1.9.x
 * Async app server (thin)
 * Rails 3

Environment setup:

 * rvm install 1.9.2-preview3
 * rvm use 1.9.2-preview3%rails3
 * gem install rails3 --pre

Starting up Rails:

 * bundle install
 * thin -D start

Test:

ab -c 5 -n 10 http://127.0.0.1:3000/widgets/http

        Concurrency Level:      5
        Time taken for tests:   2.740 seconds
        Complete requests:      10

Scenario:

 * AB opens 5 concurrent requests (10 total)
 * Each request to /widgets/http opens an async HTTP request to /widgets - aka, we ourselves spawn another 5 requests
 * Because the fiber pool is set to 10, it means we can process all 5 requests within ~1s (each mysql req takes 1s)
 * 10 requests finish in ~2s

So, keep in mind that the size of 'database pool' is basically your concurrency throttle. In example above, we spawn
10 requests, which open another 10 internally, so in total we process 20 req's in ~2s on a single thing server. Just as expected.

Resources:

 * [No callbacks, No threads - RailsConf 2010 Presentation](http://www.slideshare.net/igrigorik/no-callbacks-no-threads-railsconf-2010)
 * [Rails performance needs an overhaul](http://www.igvita.com/2010/06/07/rails-performance-needs-an-overhaul/)
 * [Non-blocking ActiveRecord](http://www.igvita.com/2010/04/15/non-blocking-activerecord-rails/)
 * [Untangling evented code with Ruby fibers](http://www.igvita.com/2010/03/22/untangling-evented-code-with-ruby-fibers/)
 * [Introducing Phat, and Async Rails app](http://www.mikeperham.com/2010/04/03/introducing-phat-an-asynchronous-rails-app/)
