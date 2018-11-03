# Passport-HTTP

[![NPM version](https://img.shields.io/npm/v/@passport-next/passport-http.svg)](https://www.npmjs.com/package/@passport-next/passport-http)
[![Build Status](https://travis-ci.org/passport-next/passport-http.svg?branch=master)](https://travis-ci.org/passport-next/passport-http)
[![Coverage Status](https://coveralls.io/repos/github/passport-next/passport-http/badge.svg?branch=master)](https://coveralls.io/github/passport-next/passport-http?branch=master)
[![Maintainability](https://api.codeclimate.com/v1/badges/a5614edd33ca5e08e641/maintainability)](https://codeclimate.com/github/passport-next/passport-http/maintainability)
[![Dependencies](https://david-dm.org/passport-next/passport-http.png)](https://david-dm.org/passport-next/passport-http)
<!--[![SAST](https://gitlab.com/passport-next/passport-http/badges/master/build.svg)](https://gitlab.com/passport-next/passport-http/badges/master/build.svg)-->

HTTP Basic and Digest authentication strategies for [Passport](https://github.com/passport-next/passport).

This module lets you authenticate HTTP requests using the standard basic and
digest schemes in your Node.js applications.  By plugging into Passport, support
for these schemes can be easily and unobtrusively integrated into any
application or framework that supports [Connect](http://www.senchalabs.org/connect/)-style
middleware, including [Express](http://expressjs.com/).

## Install

    $ npm install @passport-next/passport-http

## Usage of HTTP Basic

#### Configure Strategy

The HTTP Basic authentication strategy authenticates users using a userid and
password.  The strategy requires a `verify` callback, which accepts these
credentials and calls `done` providing a user.

    passport.use(new BasicStrategy(
      function(userid, password, done) {
        User.findOne({ username: userid }, function (err, user) {
          if (err) { return done(err); }
          if (!user) { return done(null, false); }
          if (!user.verifyPassword(password)) { return done(null, false); }
          return done(null, user);
        });
      }
    ));

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'basic'` strategy, to
authenticate requests.  Requests containing an 'Authorization' header do not
require session support, so the `session` option can be set to `false`.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/private', 
      passport.authenticate('basic', { session: false }),
      function(req, res) {
        res.json(req.user);
      });

#### Examples

For a complete, working example, refer to the [Basic example](https://github.com/passport/express-3.x-http-basic-example).

## Usage of HTTP Digest

#### Configure Strategy

The HTTP Digest authentication strategy authenticates users using a username and
password (aka shared secret).  The strategy requires a `secret` callback, which
accepts a `username` and calls `done` providing a user and password known to the
server.  The password is used to compute a hash, and authentication fails if it
does not match that contained in the request.

The strategy also accepts an optional `validate` callback, which receives
nonce-related `params` that can be further inspected to determine if the request
is valid.

    passport.use(new DigestStrategy({ qop: 'auth' },
      function(username, done) {
        User.findOne({ username: username }, function (err, user) {
          if (err) { return done(err); }
          if (!user) { return done(null, false); }
          return done(null, user, user.password);
        });
      },
      function(params, done) {
        // validate nonces as necessary
        done(null, true)
      }
    ));

#### Authenticate Requests

Use `passport.authenticate()`, specifying the `'digest'` strategy, to
authenticate requests.  Requests containing an 'Authorization' header do not
require session support, so the `session` option can be set to `false`.

For example, as route middleware in an [Express](http://expressjs.com/)
application:

    app.get('/private', 
      passport.authenticate('digest', { session: false }),
      function(req, res) {
        res.json(req.user);
      });

#### Examples

For a complete, working example, refer to the [Digest example](https://github.com/passport/express-3.x-http-digest-example).
