Web::Cache [![Build Status](https://travis-ci.org/scmorrison/perl6-Web-Cache.svg?branch=master)](https://travis-ci.org/scmorrison/perl6-Web-Cache)
===

Web::Cache is a Perl 6 web framework independant LRU caching module.

**note:** this is very much a work in progress. Ideas, issues, and pull-requests welcome.

Usage
=====

```perl6
# Bailador example
use v6;
use Bailador;
use Web::Cache;

Bailador::import; # for the template to work

# Configure cache backend
constant memory = cache-start size    => 2048,
                              backend => 'memory';

# TODO: Create multiple cache stores using different
#       backends. This will enable caching certain 
#       content to different stores.

# Cached templates!
get / ^ '/template/' (.+) $ / => sub ($x) {
    my $template = 'tmpl.tt';
     
    # Any code passed as the `content` parameter
    # will be run on initial cache insert only.
    # Once cache expiration is supported, this code
    # will re-run again when the key expires.
    webcache backend => memory,
             key     => [$template, $x].join('-'),
             content => { template($template, %{ name => $x }) };
}

baile;

# Test with ab:
#
# ab -n 1000 -c 10 http://0.0.0.0:3000/template/bailador 
#
# with Web::Cache:
#  Requests per second:    165.66 [#/sec] (mean)
# without Web::Cache:
#  Requests per second:    23.88 [#/sec] (mean)
```

Config
======

Currently only memory caching is supported. 

```perl6
# Configure cache backend
constant memory = cache-start size    => 2048,
                              backend => 'memory';
```

## Todo:

```perl6
constant memory    = cache-start size    => 2048,
                                 expires => 3600, # add expires parameter
                                 backend => 'memory';

constant disk      = cache-start path    => '/tmp/webcache/',
                                 expires => 3600,
                                 backend => 'disk';

constant memcached = cache-start servers => ["127.0.0.1:11211"],
                                 expires => 3600,
                                 backend => 'memcached';

constant redis     = cache-start host    => "127.0.0.1",
                                 port    => 6379,
                                 expires => 3600,
                                 backend => 'redis';
```


Installation
============

## zef
```
zef install Web::Config
```

## Manual

```
git clone https://github.com/scmorrison/perl6-Web-Cache.git
cd perl6-Web-Cache/
zef install .
```

Todo
====

* Support additional backends (disk, Memcached, Redis)
* Cache key generator
* Partial / fragment support
* Tests

Requirements
============

* [Perl 6](http://perl6.org/)

AUTHORS
=======

* Sam Morrison

Copyright and license
=====================

Copyright 2017 Sam Morrison

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.
