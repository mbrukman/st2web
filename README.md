StackStorm Web UI
=================

![st2web v0.9](https://cloud.githubusercontent.com/assets/1357357/7393040/010f78ac-eea5-11e4-82bf-8253674683e1.png)

Quick start
-----------

First of all, you need to make sure you have latest stable `node` and `npm` packages installed.

    $ node -v
    v0.10.32

    $ npm -v
    1.4.9

then you need to globally install `bower` and `gulp`

    $ npm install -g bower
    $ npm install -g gulp

then you need to install the requirements

    $ npm install
    $ bower install

and finally run build system to fetch the font, compile css and so on

    $ gulp

At that point you should be able to point your browser to http://localhost:3000/ and see the the page.

Build system
------------

While `gulp` runs the longest chain of tasks including style compiling, spining up dev server and watching for changes, you can run all of them directly using `gulp <task>`. Some common tasks are:
 - `gulp build` - just lint and compile all the stuff
 - `gulp test` - build the project and then run e2e tests
 - `gulp serve` - build the project and start serving it at 3000 port
 - `gulp production` - build production version of the project

You can see the whole list of tasks in `gulpfile.js`.

Connecting to st2 server
-------------------------
Configure the CORS on StackStorm server: on your st2 installation, edit the following lines to [[api] section](https://github.com/StackStorm/st2/blob/master/conf/st2.conf#L3-L9) in `/etc/st2/st2.conf`:

    [api]
    # Host and port to bind the API server.
    host = 0.0.0.0
    port = 9101
    logging = st2api/conf/logging.conf
    # List of allowed origins for CORS, use when deploying st2web client
    # The URL must match the one the browser uses to access the st2web
    allow_origin = http://st2web.example.com:3000

Configure st2web to point to the right server(s). By default, UI tries to get its data from the [devenv](https://www.github.com/StackStorm/devenv) vagrant box on 172.168.50.50.

To make it work with your st2 API, edit [`config.js`](./config.js) at the root of the project and update the `hosts` array with proper object of **name**, **url** and **auth**. URL should include scheme, domain and port in compliance with [rfc1808](http://tools.ietf.org/html/rfc1808.html). Auth might be either boolean (in which case the default schema and port is used) or a url of the auth server (see url requirements). For vagrant deployment of [st2express](https://github.com/StackStorm/st2express), it would be:

    hosts: [{
      name: 'Express Deployment',
      url: 'http://172.168.90.50:9101',
      auth: true
    }]

Multiple servers can be configured. Pick an desired server from the login screen and change the server by first disconnecting from the current one by picking 'Disconnect' from the drop down at the top right corner of the UI.


Production
----------
While `gulp serve` is ideal for development purposes and quick preview, it requires browser to make lots and lots of requests downloading every single project file separately thus wasting a lot of time on making a request and waiting for response. Production version minimizes the number of files by concatenating them together and minifies some of the most heavy files reducing their size up to 5 times. It also makes compiled version completely independent of the rest of code allowing you to deploy it everywhere: static apache\nginx server, AWS, Heroku, Github Pages.

To build production version, run

    $ gulp production

Then, you can find compiled version of the project in `build/` folder. You can just copy it to the public folder your webserver, create symlink or push it to another repository as a deployment strategy.


Testing
-------

For now, we don't have the UI to test, so we are using st2-docs application to make sure we are aware of any changes happening in StackStorm API. Take it as some sort of a live guide.

First of all, you need to make sure you have [Java Development Kit (JDK)][JDK] installed

    $ java -version
    java version "1.6.0_65"
    Java(TM) SE Runtime Environment (build 1.6.0_65-b14-462-11M4609)
    Java HotSpot(TM) 64-Bit Server VM (build 20.65-b04-462, mixed mode)

[JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html

Then, you would need to update and start a webdriver for Selenium tests to run on

    $ node_modules/.bin/webdriver-manager update
    $ node_modules/.bin/webdriver-manager start

Or if you want, you can setup Selenium to [start as a launchd service][selguide].
[selguide]: http://blog.richardknop.com/2013/06/installing-selenium-2-on-mac-os-as-service/

You should also make sure, StackStorm is running and in the stock state. For that, follow
[devenv](https://github.com/StackStorm/devenv) and [Stanley](https://github.com/StackStorm/st2) README files.

After that, `gulp test` should finish successfully.

Copyright, License, and Contributors Agreement
----------------------------------------------

Copyright 2015 StackStorm, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this work except in compliance with the License. You may obtain a copy of the License in the [LICENSE](LICENSE) file, or at:

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

By contributing you agree that these contributions are your own (or approved by your employer) and you grant a full, complete, irrevocable copyright license to all users and developers of the project, present and future, pursuant to the license of the project.
