---
layout: post
title:  "multi-server continuous deployment with fleet"
date:   2012-05-03 02:00:00
author: Isaac Schlueter
categories: program
---

## multi-server continuous deployment with fleet
### by Isaac Schlueter
### at 2012-05-03 02:00:00
### original <http://blog.nodejs.org/2012/05/02/multi-server-continuous-deployment-with-fleet/>

<p><img style="float:right;margin-left:1.2em" alt="substack" src="http://substack.net/images/substackistan.png"><i>This is a guest post by James “SubStack” Halliday, originally posted <a href="http://substack.net/posts/16a9d8/multi-server-continuous-deployment-with-fleet">on his blog</a>, and reposted here with permission.</i></p>
<p>Writing applications as a sequence of tiny services that all talk to each other over the network has many upsides, but it can be annoyingly tedious to get all the subsystems up and running. </p>
<p>Running a <a href="http://substack.net/posts/7a1c42">seaport</a> can help with getting all the services to talk to each other, but running the processes is another matter, especially when you have new code to push into production. </p>
<p><a href="http://github.com/substack/fleet">fleet</a> aims to make it really easy for anyone on your team to push new code from git to an armada of servers and manage all the processes in your stack. </p>
<p>To start using fleet, just install the fleet command with <a href="http://npmjs.org">npm</a>: </p>
<pre style="font-size:1.2em">$ npm install -g fleet </pre>
<p>Then on one of your servers, start a fleet hub. From a fresh directory, give it a passphrase and a port to listen on: </p>
<pre style="font-size:1.2em">$ fleet hub --port=7000 --secret=beepboop </pre>
<p>Now fleet is listening on :7000 for commands and has started a git server on :7001 over http. There’s no ssh keys or post commit hooks to configure, just run that command and you’re ready to go! </p>
<p>Next set up some worker drones to run your processes. You can have as many workers as you like on a single server but each worker should be run from a separate directory. Just do: </p>
<pre style="font-size:1.2em">$ fleet drone --hub=x.x.x.x:7000 --secret=beepboop </pre>
<p>where <span>x.x.x.x</span> is the address where the fleet hub is running. Spin up a few of these drones. </p>
<p>Now navigate to the directory of the app you want to deploy. First set a remote so you don’t need to type <span>–hub</span> and <span>–secret</span> all the time. </p>
<pre style="font-size:1.2em">$ fleet remote add default --hub=x.x.x.x:7000 --secret=beepboop </pre>
<p>Fleet just created a <span>fleet.json</span> file for you to save your settings. </p>
<p>From the same app directory, to deploy your code just do: </p>
<pre style="font-size:1.2em">$ fleet deploy </pre>
<p>The deploy command does a <span>git push</span> to the fleet hub’s git http server and then the hub instructs all the drones to pull from it. Your code gets checked out into a new directory on all the fleet drones every time you deploy. </p>
<p>Because fleet is designed specifically for managing applications with lots of tiny services, the deploy command isn’t tied to running any processes. Starting processes is up to the programmer but it’s super simple. Just use the <span>fleet spawn</span> command: </p>
<pre style="font-size:1.2em">$ fleet spawn -- node server.js 8080 </pre>
<p>By default fleet picks a drone at random to run the process on. You can specify which drone you want to run a particular process on with the <span>–drone</span> switch if it matters. </p>
<p>Start a few processes across all your worker drones and then show what is running with the <span>fleet ps</span> command: </p>
<pre style="font-size:1.2em">$ fleet ps
drone#3dfe17b8
├─┬ pid#1e99f4
│ ├── status:   running
│ ├── commit:   webapp/1b8050fcaf8f1b02b9175fcb422644cb67dc8cc5
│ └── command:  node server.js 8888
└─┬ pid#d7048a
  ├── status:   running
  ├── commit:   webapp/1b8050fcaf8f1b02b9175fcb422644cb67dc8cc5
  └── command:  node server.js 8889</pre>
<p>Now suppose that you have new code to push out into production. By default, fleet lets you spin up new services without disturbing your existing services. If you <span>fleet deploy</span> again after checking in some new changes to git, the next time you <span>fleet spawn</span> a new process, that process will be spun up in a completely new directory based on the git commit hash. To stop a process, just use <span>fleet stop</span>. </p>
<p>This approach lets you verify that the new services work before bringing down the old services. You can even start experimenting with heterogeneous and incremental deployment by hooking into a custom <a href="http://substack.net/posts/5bd18d">http proxy</a>! </p>
<p>Even better, if you use a service registry like  <a href="http://substack.net/posts/7a1c42">seaport</a> for managing the host/port tables, you can spin up new ad-hoc staging clusters all the time without disrupting the normal operation of your site before rolling out new code to users. </p>
<p>Fleet has many more commands that you can learn about with its git-style manpage-based help system! Just do <span>fleet help</span> to get a list of all the commands you can run. </p>
<pre style="font-size:1.2em">$ fleet help
Usage: fleet &lt;command&gt; [&lt;args&gt;]

The commands are:
  deploy   Push code to drones.
  drone    Connect to a hub as a worker.
  exec     Run commands on drones.
  hub      Create a hub for drones to connect.
  monitor  Show service events system-wide.
  ps       List the running processes on the drones.
  remote   Manage the set of remote hubs.
  spawn    Run services on drones.
  stop     Stop processes running on drones.

For help about a command, try `fleet help `.</pre>
<p><span>npm install -g fleet</span> and <a href="https://github.com/substack/fleet">check out the code on github</a>! </p>
<p><img src="http://substack.net/images/fleet.png" width="849" height="568"> </p>
<br>  <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gocomments/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/comments/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godelicious/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/delicious/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gofacebook/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/facebook/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gotwitter/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/twitter/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gostumble/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/stumble/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godigg/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/digg/nodeblog.wordpress.com/510/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/goreddit/nodeblog.wordpress.com/510/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/reddit/nodeblog.wordpress.com/510/"></a> <img alt="" border="0" src="http://stats.wordpress.com/b.gif?host=blog.nodejs.org&amp;blog=21260551&amp;post=510&amp;subd=nodeblog&amp;ref=&amp;feed=1" width="1" height="1">