# rlp-proxy

This is the source code for the proxy used in <a href='https://github.com/dhaiwat10/react-link-preview'>react-link-preview</a>.



## Important note
The `master` branch version of the proxy requires Redis & Supabase for rate-limiting & caching purposes. If you don't want these features, please switch to the [`no-redis-caching`](https://github.com/Dhaiwat10/rlp-proxy/tree/no-redis-caching) branch and use the code residing there.

RedisToGo that has been used with this project is shutting down in August 2022. We will not be using RedisToGo, but migrate to Upstash-Redis. Simply create an account, and copy the implementation code to replace the current RedisToGo code in this project. Or we could deploy the proxy server and install Upstash directly on Heroku, and use the 'host', 'port', 'password' that are provided upon installation in our code like below.

```
const Redis = require('ioredis');    //import ioredis package  
let client = new Redis(
  'rediss://:upstash-password@upstash-host:upstash-host'
 );

client.set('foo', 'bar');
```

To be able to use limiter, we simply replace the code ```const limiter = require('express-limiter')(app, redis);``` with ```const limiter = require('express-limiter')(app, client);``` since client is an instance of 'ioredis', redis is an instance of 'redis' package, and limiter takes in 2 parameters that are instance of express and instance of redis or ioredis.

## Environment variables & pre-requisites

1. `SERVER_URL`: The URL where your server is runnning (used for serving static files). For example, if you are running the server locally it will be `localhost:3000` and if your server is live at `abc.xyz`, the value for this variable will be `abc.xyz`. This variable is necessary to serve static files correctly.
2. `SUPABASE_KEY`: Your Supabase project's anon key. This proxy uses Supabase to cache query results. You will need to (i) Create a new project on [Supabase](https://app.supabase.io), (ii) Create a new table called `meta-cache` and create the following columns: `url` (text), `title` (text), `description` (text), `siteName` (text), `image` (text), `hostname` (text).
3. Redis. If you're deploying to Heroku, you can use the [Redis To Go](https://elements.heroku.com/addons/redistogo) add-on. Starting in August onward, Redis To Go will shutdown, we will use [Upstash Redis](https://devcenter.heroku.com/articles/upstash-redis) instead.

