## Enable faster deploys

Add 'turbo-sprockets-rails3' to Gemfile.

Run this on your Heroku app:

```
heroku config:add BUILDPACK_URL=https://github.com/ndbroadbent/heroku-buildpack-turbo-sprockets.git
```
