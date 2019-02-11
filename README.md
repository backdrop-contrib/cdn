CDN
======================

This module provide easy Content Delivery Network integration for Backdrop CMS.
It alters file URLs, so that files are downloaded from a CDN instead of your
web server.

It provides two modes: "Origin Pull" and "File Conveyor".

In "Origin Pull" mode, only "Origin Pull" CDNs are supported (hence the name).
These are CDNs that only require you to replace the domain name with another
domain name. The CDN will then automatically fetch (pull) the files from your
server (the origin).

In "File Conveyor" mode, this module integrates with the File Conveyor [1]
daemon. This allows for much more advanced setups: files can be processed
(e.g. optimize images like smush.it [2], minify CSS with YUI Compressor [3],
minify JS with YUI compressor or Google Closure Compiler [4], and it's easy to
add your own!), before they are synced and your CDN doesn't *have* to support
Origin Pull, any push method is fine (supported transfer protocols: FTP,
Amazon S3, Rackspace CloudFiles). File Conveyor is flexible enough to be used
with *any* CDN, thus it enables you to avoid vendor lock-in.

If you're not sure which mode to use, use "Origin Pull". It's easier and more
reliable. Every single common CDN today (2015) supports Origin Pull.

**Note:** It is essential that you understand the key properties of a CDN, most
importantly the differences between an Origin Pull CDN and a Push CDN. A good
(and compact!) reference is the "Key Properties of a CDN" article [5].

The CDN module aims to do only one thing and do it well: altering URLs to
point to files on CDNs.
However, in later versions, it does as much as possible to make CDN
integration frictionless:
    • Any sort of CDN mapping
    • optimal Far Future expiration (http://drupal.org/node/974350)
        - CORS (http://drupal.org/node/982188)
        - signed URLs prevent abuse
        - disabled by default, automatically disabled when in maintenance mode
        - *requires* a CDN or reverse proxy, not Apache/nginx/lighttpd/…!
    • Advanced Help integration to guide you (http://drupal.org/node/1413162)
    • DNS prefetching (http://drupal.org/node/982188)
    • CSS aggregation (http://drupal.org/node/1428530)
    • auto-balance files over multiple CDNs (http://drupal.org/node/1452092)
    • … and many more details that are taken care of automatically

But in some cases, simply altering the URL is not enough, that's where the
AdvAgg module comes in:

    If you've ever had any issues with CSS or JS files not behaving as
    desired, check out AdvAgg. The "Advanced CSS/JS Aggregation" module solves
    all issues that arise from having CSS/JS served from a CDN. Keeping track
    of changes to CSS/JS files, smart aggregate names, 404 protection,
    on-demand generation, works with private file system, Google CDN
    integration, CSS/JS compression, GZIP compression, caching, and smart
    bundling are some of the things AdvAgg does. It's also faster then core's
    file aggregation.

[1] http://fileconveyor.org/
[2] http://smushit.com/
[3] http://developer.yahoo.com/yui/compressor/
[4] http://code.google.com/closure/compiler/
[5] http://wimleers.com/article/key-properties-of-a-cdn

### Supported CDNs

- Origin Pull mode: any Origin Pull CDN (or alternatively: domains that point
  to your main domain, by using so called "CNAME" DNS records).
- File Conveyor mode: any Origin Pull CDN and any push CDN that supports FTP.
  Support for other transfer protocols is welcomed and encouraged: your
  patches are welcome! Amazon S3, Amazon CloudFront and Rackspace CloudFiles
  are also supported.


Installation
------------

- Install this module using the official Backdrop CMS instructions at
  https://backdropcms.org/guide/modules.

- Visit the configuration page under Administration > Configuration >
  Development > CDN (admin/config/development/cdn) to learn about the various
  settings.

- Go to your CDN provider's control panel and set up a "CDN instance" (Amazon
  CloudFront calls this a "distribution"). There, you will have to specify
  the origin server (Amazon CloudFront calls this a "custom origin"), which
  is simply the domain name of your Backdrop site.
  The CDN will provide you with a "delivery address", this is the address
  that we'll use to download files from the CDN instead of the Backdrop server.
  Suppose this is `http://d85nwn7m5gl3y.cloudfront.net`.
  Be sure to forward query strings from the CDN to the origin! Otherwise image
  style derivatives will not work.
  (It acts like a globally distributed, super fast proxy server.)

  Relevant links:
  - Amazon CloudFront gotcha: https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/QueryStringParameters.html

- Optionally, you can create a CNAME alias to the delivery address on your
  DNS server. This way, it's not immediately obvious from the links in the
  HTMl that you're using an external service (that's why it's also called a
  vanity domain name).
  However, if you're going to use your CDN in HTTPS mode, then using vanity
  domains will break things (because SSL certificates are bound to domain
  names).

- Enter the domain name (`http://d85nwn7m5gl3y.cloudfront.net`, or the vanity
  domain/CNAME if you used that instead) at admin/settings/cdn/details. If
  you want to support HTTPS transparently, it is recommended to enter it as
  `//d85nwn7m5gl3y.cloudfront.net` instead — this is a protocol-relative URL.

- Visit the Status Report under Administration > Reports > Status
  (admin/reports/status). The CDN module will report its status here.

- Enable the display of statistics at "admin/config/development/cdn", browse
  your site with your root/admin (user id 1) account. The statistics will
  show which files are served from the CDN!

- If your site is behind a reverse proxy such as Varnish, so that your stack
  looks like: CDN <-> reverse proxy <-> web server, then you need to take extra
  measures if you want to prevent duplicate content showing up on the CDN. See
  https://www.drupal.org/node/2678374#comment-11278951 for details. It's
  possible in this situation to end up with redirect loops; for that reason
  the CDN module adds a debugging header to the 301 redirects it emits in order
  to facilitate troubleshooting.


Documentation
-------------

Additional documentation is located in the Wiki:
https://github.com/backdrop-contrib/cdn/wiki/Documentation.


Issues
------

Bugs and Feature requests should be reported in the Issue Queue:
https://github.com/backdrop-contrib/cdn/issues.


Current Maintainers
-------------------

- Jen Lampton (https://github.com/username).
- Co-Maintiners wanted


Credits
-------

- Ported to Backdrop CMS by jen Lampton (https://github.com/jenlampton).
- Port of Far Future expiration functionality to Drupal 7 [ONE Agency](http://www.one-agency.be).
- Originally written for Drupal 6 as part of the [bachelor thesis](http://wimleers.com/tags/bachelor-thesis) of [Wim Leers](http://wimleers.com) at [Hasselt University](http://uhasselt.be).


License
-------

This project is GPL v2 software. See the LICENSE.txt file in this directory for
complete text.
