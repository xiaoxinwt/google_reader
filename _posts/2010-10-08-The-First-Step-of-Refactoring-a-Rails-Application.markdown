---
layout: post
title:  "The First Step of Refactoring a Rails Application"
date:   2010-10-08 09:06:31
author: Eric Davis
categories: program
---

## The First Step of Refactoring a Rails Application
### by Eric Davis
### at 2010-10-08 09:06:31
### original <http://feedproxy.google.com/~r/RailsInside/~3/RL6AfxNdnKE/460-the-first-step-of-refactoring-a-rails-application.html>

<p><img src="http://www.railsinside.com/wp-content/uploads/2009/10/tickbox.png" width="117" height="127" alt="tickbox.png" style="float:left;margin-right:12px;margin-bottom:12px">Refactoring is an important step in software development.  It helps to make sure that existing code is kept updated and clean, even after a herd of people trample over it.</p>
<p>Recently I&#39;ve been asked a lot as to which tools I use to refactor Rails apps. I don&#39;t use any one tool to refactor, but instead have a toolbox of several tools.  Having several tools at your disposal gives you more flexibility to find what code <em>needs</em> to be refactored.</p>
<h3>Intuition</h3>
<p>The first and primary tool I use is intuition.  If you know your code really well, you will already know which areas cause problems and need to be refactored.</p>
<p>If you are new to a code base though, you won&#39;t have this intuition and need to build it up before you can use it.  Luckily there are several tools to find problem code that is just waiting for you to refactor them.</p>
<h3>wc - the Unix word counter</h3>
<p>The first tool is a simple Unix tool called <code>wc</code>.  <code>wc</code> is the word count command and it&#39;s installed on almost all Unixes by default.  It does more than count words though, it also counts the number of lines in a file.  In my experience, classes and files with the most lines tend to need the most refactoring.  If you think about it, more lines means more code which means a greater likelihood of bad code.</p>
<p>Using <code>wc -l</code> you can easily get line counts for all of the major areas of your Rails project.  I typically just look in the controllers and models since that&#39;s where most development takes place but you might also want to check your lib and vendor directories.  What you are looking for are large and complex classes.</p>
<p>If you have a lot of classes, like <a href="http://www.redmine.org">Redmine</a> does, it can be useful to pipe the output to <code>sort -n</code> which sorts it numerically.</p>
<pre>
$ wc -l app/controllers/*.rb | sort -n
    25 app/controllers/auto_completes_controller.rb
    25 app/controllers/ldap_auth_sources_controller.rb
    26 app/controllers/project_enumerations_controller.rb
    ... snip ...
   248 app/controllers/wiki_controller.rb
   271 app/controllers/projects_controller.rb
   272 app/controllers/account_controller.rb
   322 app/controllers/repositories_controller.rb
   324 app/controllers/timelog_controller.rb
   326 app/controllers/issues_controller.rb
   412 app/controllers/application_controller.rb
  5381 total
</pre>
<pre>
$ wc -l app/models/*.rb | sort -n
    22 app/models/document_observer.rb
    22 app/models/group_custom_field.rb
    22 app/models/issue_observer.rb
    ... snip ...
   200 app/models/changeset.rb
   202 app/models/wiki_page.rb
   215 app/models/repository.rb
   234 app/models/version.rb
   336 app/models/mail_handler.rb
   416 app/models/user.rb
   442 app/models/mailer.rb
   643 app/models/query.rb
   774 app/models/project.rb
   863 app/models/issue.rb
  7412 total
</pre>
<h3>Reviewing the results from wc</h3>
<p>You want to look for a few things with raw line counts:</p>
<ul>
<li>is there a class with a disproportionate number of lines compared to the others?</li>
<li>are there more lines in the controllers than the models?</li>
</ul>
<p>In general, a well factored Rails application will have a majority of its code in the models and most classes will be the same size.  There might be a few major classes that are larger than the rest, which are the primary domain objects.  Using Redmine as an example, you would expect any Project, Issue, and User classes to be large since it&#39;s a project management and bug tracking application.  Looking at the line counts from <code>wc</code>, the Timelog controller and Query model have a considerable number of lines even though they aren&#39;t the primary domain objects. In fact, because of my intuition from working with Redmine for years, I know both of these classes need some major refactoring.</p>
<h3>Reading the code to find refactorings</h3>
<p>After you see which classes are the largest, you will want to read through the class itself.  Look for how &quot;thick&quot; the class is and see what you can learn about it&#39;s structure.</p>
<ul>
<li><strong>Are there a few large method that contain most of the code for the class?</strong> Might be able to use extract method and extract class to shrink their size.</li>
<li><strong>Are there a lot of short methods?</strong>  Maybe the code was over-factored or the class is trying to do too much and needs to be split.</li>
<li><strong>Are there a lot of comments?</strong> Since <code>wc</code> counts comments as lines too, <code>wc</code> might report that a class is large but there might not be that much actual code there.</li>
</ul>
<p>After reading through some classes you will start to get a feel for what areas to refactor and if there are any common problems.  You should also be able to start working on a few refactorings as you go.</p>
<p>Next time I&#39;ll talk about using some tools that understand Ruby and what makes Ruby code complex.</p>
<p style="padding:8px;background-color:#eee"><img src="http://gravatar.com/avatar/ae959c6836366656b895069d75ad09bf?size=64" alt="Eric Davis" style="float:left;margin-right:12px;margin-bottom:12px"><strong>This is a guest post by Eric Davis.</strong>  Eric is a member of the Redmine team and has written an ebook <a href="http://www.refactoringredmine.com/">Refactoring Redmine</a> to show Rails developers what refactoring real Rails code looks like.  He writes about the different refactorings done to Redmine every day at <a href="http://theadmin.org">http://theadmin.org</a>.</p>
<p align="left"><a href="http://twitter.com/home/?status=The+First+Step+of+Refactoring+a+Rails+Application+http://www.railsinside.com/?p=460" title="Post to Twitter"><img src="http://www.railsinside.com/wp-content/plugins/tweet-this/icons/tt-twitter.png" alt="Post to Twitter"></a> <a href="http://twitter.com/home/?status=The+First+Step+of+Refactoring+a+Rails+Application+http://www.railsinside.com/?p=460" title="Post to Twitter">Tweet This Post</a></p><div>
<a href="http://feeds.feedburner.com/~ff/RailsInside?a=RL6AfxNdnKE:sMwErDmgzVI:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/RailsInside?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RailsInside?a=RL6AfxNdnKE:sMwErDmgzVI:D7DqB2pKExk"><img src="http://feeds.feedburner.com/~ff/RailsInside?i=RL6AfxNdnKE:sMwErDmgzVI:D7DqB2pKExk" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RailsInside?a=RL6AfxNdnKE:sMwErDmgzVI:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/RailsInside?i=RL6AfxNdnKE:sMwErDmgzVI:F7zBnMyn0Lo" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RailsInside?a=RL6AfxNdnKE:sMwErDmgzVI:gIN9vFwOqvQ"><img src="http://feeds.feedburner.com/~ff/RailsInside?i=RL6AfxNdnKE:sMwErDmgzVI:gIN9vFwOqvQ" border="0"></a> <a href="http://feeds.feedburner.com/~ff/RailsInside?a=RL6AfxNdnKE:sMwErDmgzVI:3H-1DwQop_U"><img src="http://feeds.feedburner.com/~ff/RailsInside?i=RL6AfxNdnKE:sMwErDmgzVI:3H-1DwQop_U" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/RailsInside/~4/RL6AfxNdnKE" height="1" width="1">