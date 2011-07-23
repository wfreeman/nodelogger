# nodelogger

## the problem

A heavily virtual-hosted server in apache's httpd creates some difficulties with logging, by itself. First, it's not practical to spit everything in the same log, so some solutions configure custom logs in the virtualhost directives. Second, you usually want to have some sort of log rotation, so log file sizes don't get out of hand. There are some solutions available to handle this--even one from apache called rotatelogs (http://httpd.apache.org/docs/2.2/programs/rotatelogs.html).

A big downside to rotatelogs: If you use it within virtualhost directives, you end up with a process for each virtualhost. That can get a bit annoying when you have a hundred virtualhosts and have 100 processes going.

One solution: I used http://n0rp.chemlab.org/vlogger/ for a while, which was straightforward and easy. I used some ideas from it while writing this nodelogger. 

The idea behind nodelogger is to provide a logging utility able to take the standard "piped" output from apache, and to drop that output into a mongodb database. It also has the side benefit of looking up IP address lat/lon, which I have plans for in my logviewer to come.

## installing

This guide assumes you have nodejs (tested with 0.4.9) and npm installed. You also need to have a mongodb server.

1. Install GeoIP: `npm install geoip`
2. Install MongoDB: `npm install mongodb --mongodb:native`
3. Put nodelogger.js in a convenient path somewhere, let's say /usr/local/node_modules/nodelogger/nodelogger.js

## configure your apache

nodelogger expects a particular format from apache. You'll want to set up your httpd.conf to look like this:

```apache
LogFormat "%v %h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
CustomLog "| /usr/local/bin/node /usr/local/node_modules/nodelogger/nodelogger.js" combined
```

## todo

* handle error logging.
* don't insert fields that have no value.
* create an npm package.