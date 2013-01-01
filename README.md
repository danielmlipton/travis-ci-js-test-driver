[![Build Status](https://travis-ci.org/danielmlipton/travis-ci-js-test-driver.png?branch=master)](https://travis-ci.org/danielmlipton/travis-ci-js-test-driver)

travis-ci-js-test-driver
========================

## Get The Code 

Note that this repository uses two required submodules.  To get all of the code
you need, please use the following commands:

```
git clone git@github.com:danielmlipton/travis-ci-js-test-driver.git
git submodule init
git submodule update
```

## Dependencies

 * [node.js](http://nodejs.org/)

With [Homebrew](http://mxcl.github.com/homebrew/) on a Mac:

```
brew update && brew install node
```

 * [PhantomJS](http://phantomjs.org/)

```
npm install phantomjs
```

or

```
brew install phantomjs
```

*TODO: Document how to install phantom with the included package.json*

This repository does not contain the JsTestDriver jar, but the included Travis
CI config will download the file on the build machine and place it in the 
appropriate directory.  If you'd like to run the tests locally, you'll need to
download the following file:

```
curl -o resources/JsTestDriver-1.3.3d.jar \
        http://js-test-driver.googlecode.com/files/JsTestDriver-1.3.3d.jar
```

== Testing Your Setup

To ensure that your installation is working with the tests included in the
submodule js-test-driver-demo, you'll need to start the JsTestDriver server:

```
java -jar resources/js-test-driver/JsTestDriver-1.3.3d.jar \
     --basePath resources/js-test-driver                   \
     --config resources/js-test-driver/jsTestDriver.conf   \
     --port 9876 
```

Then you'll need to start an instance of PhantomJS with the submodule
js-test-driver-phantomjs:

```
phantomjs resources/js-test-driver-phantomjs/phantomjs-jstd.js &
```

Finally, you'll need to execute all of the tests:

```
java -jar resources/js-test-driver/JsTestDriver-1.3.3d.jar \
     --basePath resources/js-test-driver --tests all --reset
```

You should see results like:

    Safari: Reset
    Safari: Reset
    .
    .
    .
    .
    .
    ..
    .
    ...
    Total 11 tests (Passed: 11; Fails: 0; Errors: 0) (3053.00 ms)
    Safari 534.34 Mac OS: Run 11 tests (Passed: 11; Fails: 0; Errors 0) (3053.00 ms)
    ConsoleTestCase.testLogging passed (1.00 ms)
          [LOG] This is log message.
          [DEBUG] Printing out the value  2
          [INFO] Array content is:  ["one","two",3,"four"]  
          [WARN] The value is undefined:  undefined
          [ERROR] 1 2 3 4 5 6 7 8 9 10

## Merging With Your Repository

Before moving any of these files into your own repository, you should note that
there is no src or lib directories included with this example.  The test code
was unceremoniously borrowed from Mária Jurčovičová's [very thorough post]
(http://meri-stuff.blogspot.com/2012/01/javascript-testing-with-jstestdriver.html)
about testing with JsTestDriver.  It's only included as an example of tests
written for JsTestDriver.  You probably don't want to use them in your code.

Second, you probably don't want to include a 4 Mb binary file in your repository.
In order to do this, please add the following to your .gitignore:

    JsTestDriver-1.3.3d.jar

Third, you might already have a .travis.yml in your repository.  You will need
to copy at least the before_script section of the included .travis.yml.

Lastly, you probably do want to add js-test-driver-phantomjs to your repository
as a submodole.  You can accomplish this with the following command, just change
the destination path:

```
git submodule add git://github.com/larrymyers/js-test-driver-phantomjs.git \
    path/to/js-test-driver-phantomjs
```

== Tips

PhantomJS uses [a very old version of WebKit]
(http://code.google.com/p/phantomjs/issues/detail?id=522) that doesn't support
[bind()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Function/bind)
and likely other new features in [JavaScript 1.8.5]
(https://developer.mozilla.org/en-US/docs/JavaScript/New_in_JavaScript/1.8.5).
If you use bind() in any of the code you are testing, you'll need to include
a [polyfill](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Function/bind#Compatibility).
Additional polyfills can be found at [Modernizr's excellent collection]
(https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills) or
at Mozilla Developer Network's [JavaScript Reference]
(https://developer.mozilla.org/en-US/docs/JavaScript/Reference).

The most recent versions of JsTestDriver have problems with relative paths.
According to [this thread](http://code.google.com/p/js-test-driver/issues/detail?id=223)
on the JsTestDriver site, you'll need to use JsTestDriver-1.3.3d in order to
use it on Travis CI because you don't control the path there.  Sure, you could
monkey around with bash or other scripting tool to set it if you absolutely need
to, but there was no compelling feature in the newer versions for me.

## The Story Of This Repository

When I was writing the tests for [pubsub](https://github.com/danielmlipton/pubsub),
I became curious about code coverage.  After a brief tangle with a variety of
different options, I chose to use [JsTestDriver](http://code.google.com/p/js-test-driver/)
with the coverage plugin.  This had the following benefits:

1.  A IDE and CI friendly tool for running unit tests.
2.  Code coverage
3.  The project has (relatively) lengthy history, even if does not seem to be in very active development.
4.  Reasonable documentation, if scattered across many different blogs.

In writing tests for [clock](https://github.com/danielmlipton/clock), I used
[PhantomJS](http://phantomjs.org/) and [Zombie.js](http://zombie.labnotes.org/)
with [QUnit](http://qunitjs.com/) and [cucumber-js](https://github.com/cucumber/cucumber-js).
QUnit comes wit [runner.js](https://github.com/jquery/qunit/tree/master/addons/phantomjs).  
Cucmber-js utilizes Zombie.js.  While these tools work well and were easily
integrated into [Travis CI](https://travis-ci.org), adding code coverage to the
process proved troublesome.

After writing a majority of my tests in QUnit, I discovered [Sinon.js](http://sinonjs.org/),
which plays nicely with QUnit.  After integrating the two testing frameworks, I
began in earnest trying to get the both running with JsTestDriver.  This was a
lot more difficult than it should have been given a number of bugs in the various
tools I chose to use.  See the tips section for a lengthier explanation of two
of the bugs that a relevant to this repository.

The effort took some pretty serious snooping and brute force attempts to solve
problems I had little insight into.  However, with JsTestDriver was working on my
local development environment I began the process of searching for others
who had successfully set up [JsTestDriver and Travis CI]
(https://groups.google.com/forum/?fromgroups=#!topic/js-test-driver/teDkckYql2w).
With no real leads, I began to blindly poke around Travis CI and eventually
stumbled into a solution that worked.  After some refactoring, I felt that I had
a reasonable approach that I thought I'd share here for others to learn from.

In the end, JsTestDriver's code coverage plugin doesn't even work on Travis CI.
In fact, it just reports [0.0% code coverage]
(https://travis-ci.org/danielmlipton/pubsub/builds/3890560).
