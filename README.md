![spandx logo](./spandx-logo.png)

# spandx

Develop locally, proxy to prod, browser-sync, and process ESI tags.

[![NPM][npm-img]][npm]
[![Build
Status][build-img]][build]

## Quick-start

The quickest way to get started is by installing spandx as a global package.

    npm install -g spandx

Generate a sample configuration file:

    spandx init > spandx.config.js

Launch!

    spandx

The `spandx` command automatically looks for a `spandx.config.js` file in the current directory.  If you prefer to use a different name, or keep the file in a different directory, you can pass in the config file path with the `-c` option, as in `spandx -c ../otherdir/spandx.conf.js`.  Also, if you prefer to store your config file in JSON format, that is also accepted.  It's js by default to allow for commenting your configuration choices.

*Note: spandx requires node.js version 6 or higher.*

## Customizing your configuration

Here are the configuration options accepted by the config file. 

  - **host** - the hostname spandx is running on, usually "localhost", but you can add custom hostnames (ie, `/etc/hosts` entries pointing to 127.0.0.1) if you, for example, need a certain domain suffix to satisfy CORS-enabled services (string)
  - **port** - the port for spandx to listen on (number)
  - **open** - whether to open a browser tab when spandx is launched (boolean)
  - **startPath** - what URL path to open, ex: `"/site"` (string)
  - **verbose** - display English summary of configuration settings and display browserSync logs, or not (boolean)
  - **routes** - define where to send requests for any number of URL paths, best explained by example in the following section (object)
  - **bs** - a [browserSync config object][bs-options], in case you need to further customize spandx's browserSync instance (object)
    

### Routes by example

Route all requests to palebluepixel.org (a perfect reverse proxy), *unless* the request falls under `/theme`, in which case look for files in `~/projects/pbp/theme`.

    routes: {
        '/theme' : '~/projects/pbp/theme/,
        '/'      : { host: 'https://palebluepixel.org/' },
    },

Here's how this configuration would play out.

  1. visit localhost:1337 and you see what looks like palebluepixel.org
  2. one exception, the page includes `/theme/site.css`
  3. because it falls under the `/theme` route, spandx fetches `~/projects/pbp/theme/site.css` instead of `palebluepixel.org/theme/site.css`

This effectively overlays a local directory of static files on top of a remote server.  Test in production!

In addition, because `~/projects/pbp/theme` is a local directory, changes to files inside it will trigger a browserSync refresh.

## Installing as a local package in your project

The quick-start has you install spandx as a global package for simplicity, but installing it locally per-project is a better approach in many ways.

Go to your project, install spandx as a dev dependency, and create a config file:

    cd $YOUR_PROJECT
    npm install --save-dev spandx
    node node_modules/.bin/spandx init > spandx.config.js

Modify `spandx.config.js` to reflect the needs of your application.

Then edit your `package.json` and add a `start` script which launches spandx.

    "scripts": {
        "start": "spandx"
    }

Now you and your fellow contributors can run `npm start` without having to install or even understand spandx!

## Miscellaneous commands

Get the current version.

    spandx --version
    spandx -v

## Known issues

### cURLing spandx

The URL-rewriting feature is powered by browserSync's rewriteRules.  However, browserSync will only perform the rewrites if `text/html` is present in the request's `Accept` header.  Web browsers include it by default, but if you're using [cURL][curl], you'll need to add that header in order for URLs to be rewritten.

For example:

    curl -H 'Accept: text/html' localhost:1337

All other spandx features work with or without `text/html` in the `Accept` header.
    

[curl]: https://curl.haxx.se/
[npm]: https://www.npmjs.com/package/spandx
[npm-img]: https://nodei.co/npm/spandx.png
[build-img]: https://travis-ci.org/redhataccess/spandx.png?branch=master
[build]: https://travis-ci.org/redhataccess/spandx
[bs-options]: https://browsersync.io/docs/options
