# HTTP caching

## In the browser

[HTTP caching for Rails responses](https://devcenter.heroku.com/articles/http-caching-ruby-rails#timebased-cache-headers)

[Rails conditional GET docs](http://apidock.com/rails/v3.2.8/ActionController/ConditionalGet/fresh_when)

Cache for a period of time:

```
  expires_in 3.minutes, :public => true
```

Cache, but check for freshness:

```
  if stale?(@record)
  
  	respond_to….
  	
  end
```

or 

```
@people = People.scoped
fresh_when last_modified: @people.maximum(:updated_at), public: true

```

## In an intermediate cache layer

[Rack::Cache](https://devcenter.heroku.com/articles/rack-cache-memcached-rails31) for storing and serving assets, public responses

