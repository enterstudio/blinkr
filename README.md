# Blinkr

A broken page and link checker for websites. Optionally uses phantomjs to render pages
to check resource loading, links created by JS, and report any JS page load errors.

typhoeus, which can execute up to 200 parallel requests, and cache the results is used
to check links.

## Installation

Add this line to your application's Gemfile:

    gem 'blinkr'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install blinkr

If you wish to use phantomjs, install phantomjs for your platform 
http://phantomjs.org/download.html

## Usage

Blinkr determines which pages to load from your `sitemap.xml`. To run blinkr 
against your site checking every `a[href]` link on all your pages:

````
blinkr -u http://www.jboss.org
````

If you want to customize blinkr, create a config file `blinkr.yaml`. For example:


````
# Links and pages not to check (may be a regexp or a string)
skips:
  - !ruby/regexp /^\/video\/((?!91710755).)*\/$/
  - !ruby/regexp /^\/quickstarts\/((?!eap\/kitchensink).)*\/*$/
  - !ruby/regexp /^\/boms\/((?!eap\/jboss-javaee-6_0).)*\/*$/
  - !ruby/regexp /^\/archetypes\/((?!eap\/jboss-javaee6-webapp-archetype).)*\/*$/

# Errors to ignore when generating the output. Each ignore should be a hash
# containing a url (may be regexp or a string), an error code (integer) and a
# error message (may be a regexp or a string)
ignores:
  - url: http://www.acme.com/foo
    message: Not Found
  - url: !ruby/regexp /^https?:\/\/(www\.)?acme\.com\/bar\/
    code: 500

# The output file to write the report to
report: _tmp/blinkr.html

# The URL to check (often specificed on the command line)
base_url: http://www.jboss.org

# Specify the URL to the sitemap to use, rather than the default <base_url>/sitemap.xml
sitemap: http://www.jboss.org/my_sitemap.xml

# Specify the 'browser' used to load each page from the sitemap. By default 
# typhoeus is used, which will fetch the sources of each page in parallel 
# (fast). 
# Alternatively, you can use phantomjs, which will process the javascript and
# CSS. This allows any links generated by javascript as well as any resources
# loaded by the page/javascript to be checked. Additionally, any JS errors are
# reported. To use phantomjs, you must make sure the native binary is available
# on your path.
browser:phantomjs

# The number of times to try reloading a link, if the server doesn't respond or
# refuses the connection. If the retry limit is exceeded, it will be reported as
# 'Server timed out' in the report. By default 3.
max_retrys: 3

# The number times to try reloading a page. You may want to increase this if you
# find errors in the console that a page cannot be loaded
max_page_retrys: 3

# Allows blinkr to ignore fragments (#foo) which can reduce the number of URLs
# to check. By default false.
ignore_fragments: true

````

You can specify a custom config file on the command link:

````
blinkr -c my_blinkr.yaml
````

If you want to see more details about the URLs blinkr is checking, you can use
the `-v` option:

blinkr -u http://www.jboss.org -v

If you need to debug why a particular URL is being reported as bad using
blinkr, but works in your web browser, you can load a single URL using typhoeus:

````
blinkr -c my_blinkr.yaml -s http://www.acme.com/corp
````

Additionally, you can specify the `-w` option to tell libcurl to run in verbose
mode (this is very verbose, so normally used with `-s`):

````
blinkr -c my_blinkr.yaml -s http://www.acme.com/corp -v
````


## Contributing

1. Fork it ( http://github.com/pmuir/blinkr/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

