>markdown
This article includes contributions from [Lee Reilly](http://www.leereilly.net). Lee is a toolsmith hacking on [GitHub Enterprise](https://enterprise.github.com).

Static sites are sites that don't contain any dynamic server-side functionality or application logic. Examples include brochure sites made up completely of HTML and CSS or even interactive client-side games built in Flash that don't interact with a server component. Though these sites only require a web-server to deliver their content to users it's still useful to use a platform like Heroku to quickly provision and deploy to such infrastructure.

Using the [Rack](http://rack.github.com/) framework, static sites can be quickly deployed to Heroku.

>callout
>Source for this article's sample application is [available on GitHub](https://github.com/leereilly/static-site-heroku-cedar-example).

## Prerequisites

This guide assumes you have the following:

* A Heroku account.  [Signup is free and instant.](https://signup.heroku.com/signup/dc)
* The [Heroku Toolbelt](https://toolbelt.heroku.com/) installed
* Ruby and the [Bundler gem](https://rubygems.org/gems/bundler) installed 

## Create directory structure

Run the following command to create the directory structure for Rack to serve static files:

```term
$ mkdir -p site/public/{images,js,css}
$ touch site/{config.ru,public/index.html}
$ cd site && bundle init
```

This will create the following directory structure:

    - site
      |- config.ru
      |- Gemfile
      |- public
        |- index.html
        |- images
        |- js
        |- css

## Specify dependencies

Many dynamic applications require dozens of third-party libraries and frameworks to run. Static sites, however, require very little support from other libraries. In this case only the `rack` gem is required. In the `Gemfile` file add the following:

```ruby
source 'https://rubygems.org'
gem 'rack'
```

and run the `bundle` command to download and resolve the rack dependency.

```term
$ bundle install
Using rack (1.4.1)
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
```

## File server configuration

Rack must be told which files it should serve as static assets. Do this by editing the `config.ru` file so it contains the following:

```ruby
use Rack::Static, 
  :urls => ["/images", "/js", "/css"],
  :root => "public"

run lambda { |env|
  [
    200, 
    {
      'Content-Type'  => 'text/html', 
      'Cache-Control' => 'public, max-age=86400' 
    },
    File.open('public/index.html', File::RDONLY)
  ]
}
```

This template assume that you place images, javascript files and css files in the `images`, `css`, and `js` directories, respectively, and use relative references to them in your HTML.

## Running locally

To run your site locally and view any modifications before deploying to Heroku run the following command in the `site` directory:

```term
$ rackup
>> Thin web server (v1.5.0 codename Knife)
>> Maximum connections set to 1024
>> Listening on 0.0.0.0:9292, CTRL+C to stop
```


Go to your browser and open [http://localhost:9292](http://localhost:9292) to see your static site loaded. As you make changes to the site you only need to reload your browser to view the changes -- you don't have to bounce the server.

## Deploy to Heroku

Before deploying your site to Heroku you will need to store your code in the [Git version control system](http://git-scm.com/) which was installed for you as part of the Heroku Toolbelt. When inside the `site` root directory execute the following commands:

```term
$ git init
$ heroku create
$ git add .
$ git commit -m "Initial static site template app"
```

Use git to deploy to Heroku as well.

```term
$ git push heroku master
Counting objects: 6, done.
Delta compression using up to 4 threads.
...

-----> Heroku receiving push
-----> Ruby app detected
...
-----> Launching... done, v1
       http://blazing-galaxy-997.herokuapp.com deployed to Heroku
```

At this point your site is deployed to Heroku and has been given an auto-generated URL. Open your site using the `heroku open` command:

```term
$ heroku open
Opening blazing-galaxy-997... done
```

## Updating

When making changes to your site, edit the necessary files locally, commit them to Git, and push to Heroku to see the changes deployed.

```term
$ git commit -m "Update landing page"
$ git push heroku master
```

## Related

* Check out the tool [Kent Fenwick's](https://twitter.com/kentf) built that helps you [generate a static site](http://herokustaticmagico.herokuapp.com/) based on this article.