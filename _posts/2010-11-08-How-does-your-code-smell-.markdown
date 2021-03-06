---
layout: post
title:  "How does your code smell?"
date:   2010-11-08 21:22:08
author: Kevin Rutherford
categories: program
---

## How does your code smell?
### by Kevin Rutherford
### at 2010-11-08 21:22:08
### original <http://feedproxy.google.com/~r/LearningRubyBlog/~3/rhieerp_F6k/>

<p></p>
<div>
<h3>How does your code smell?</h3>
<p>This guest post is by <strong>Dr. Kevin Rutherford</strong>, a UK-based agile/XP coach, developer and project leader, with over 25 years experience in software development. He is also the founder of AgileNorth and XP-Manchester. Contact him via <a href="http://www.kevinrutherford.co.uk/">http://www.kevinrutherford.co.uk</a>.</p>
<p><img style="float:right;padding:4px;margin:0 0 2px 7px" src="http://rubylearning.com/images/kr-bw-125x125.jpg" alt="Dr. Kevin Rutherford"> <span>I</span> expect you’re a conscientious modern developer. You write your code using test-driven or behaviour-driven development, and you never write a line of production code unless it is needed in order to make a test pass. But there’s more to TDD than RED-GREEN-RED-GREEN; you have to REFACTOR each time the tests go green. Specifically, we write a test and see it fail; then we write code in the most straightforward way we possibly can to make it pass; then we refactor to clean up any mess we just made. But in that third step, just what should we refactor, and why? And if we need to refactor a pile of code that wasn’t developed in a test-driven manner, how do we get a handle on where to begin?</p>
<p>Refactoring is defined as improving software without changing what it does. The intention is to take bad code and replace it with something better. The next question must then be: What is “good code” and what is “bad code”? How can we tell them apart and recognize which is which? And when we can recognize bad code, what should we do to eliminate it? The best guiding principle here is the four rules of <a href="http://c2.com/xp/XpSimplicityRules.html">Simple Design</a>, first described by Kent Beck in the early descriptions of Extreme Programming. As <a href="http://www.jbrains.ca/permalink/the-four-elements-of-simple-design">re-expressed by J.B.Rainsberger</a>, the design of a piece of code is “simple” when it:</p>
<ol>
<li> Passes its tests</li>
<li> Minimizes duplication</li>
<li> Maximizes clarity</li>
<li> Has fewer elements</li>
</ol>
<p>So that’s it: First ensure that your code passes all its tests, then remove all duplication, then make sure the design intent is clear, and then get rid of anything that doesn’t need to be there. Easy! But “duplication” and “clarity” are non-trivial concepts. For example, it is usually easy to see when a chunk of code has been copied and pasted, but far less easy to see when a piece of knowledge, or a responsibility, exists in more than one place. Textual duplication can be detected by tools, but the more subtle kinds cannot. Similarly, the use of clear names can appear subjective; maybe a name I believe to be clear confuses you, and vice versa. Our goal is Simple Design, but the path to simplicity may be very unclear.</p>
<p>To help with this difficulty, the agile community has developed informal names for the most common kinds of “bad” code. These are anti-patterns, and are often called “code smells,” after Kent Beck’s analogy between code and babies: “If it stinks, change it”. Reflecting the rules of Simple Design, code smells come in two major varieties: they either make the code harder to understand, or something in the code is duplicated (which more than doubles the risk involved in making future changes). However, until recently there has been no catalog of the kinds of smell you might find in Ruby code, and Bill Wake and I set about remedying that problem with our book <a href="http://www.refactoringinruby.info/">Refactoring in Ruby</a>. Here’s a list of the major types of code smell we identified, grouped approximately according to their impact on the rules of Simple Design:</p>
<table style="border:1px solid gray;background-color:#eee">
<tbody>
<tr>
<td><strong>Duplication</strong></td>
<td><strong>Names</strong></td>
</tr>
<tr>
<td>Alternative Modules with Different Interfaces</td>
<td>Comments</td>
</tr>
<tr>
<td>Combinatorial Explosion</td>
<td>Complicated Boolean Expression</td>
</tr>
<tr>
<td>Control Coupling</td>
<td>Divergent Change</td>
</tr>
<tr>
<td>Data Class</td>
<td>Dynamic Code Creation</td>
</tr>
<tr>
<td>Data Clump</td>
<td>Global Variable</td>
</tr>
<tr>
<td>Derived Value</td>
<td>Implementation Inheritance</td>
</tr>
<tr>
<td>Duplicated Code</td>
<td>Inappropriate Intimacy</td>
</tr>
<tr>
<td>Feature Envy</td>
<td>Incomplete Library Module</td>
</tr>
<tr>
<td>Greedy Method</td>
<td>Inconsistent Names</td>
</tr>
<tr>
<td>Greedy Module</td>
<td>Long Parameter List</td>
</tr>
<tr>
<td>Large Module</td>
<td>Nil Check</td>
</tr>
<tr>
<td>Lazy Class</td>
<td>Runaway Dependencies</td>
</tr>
<tr>
<td>Long Method</td>
<td>Shotgun Surgery</td>
</tr>
<tr>
<td>Message Chain</td>
<td>Simulated Polymorphism</td>
</tr>
<tr>
<td>Middle Man</td>
<td>Special Case</td>
</tr>
<tr>
<td>Open Secret</td>
<td>Speculative Generality</td>
</tr>
<tr>
<td>Parallel Inheritance Hierarchies</td>
<td>Temporary Field</td>
</tr>
<tr>
<td>Procedural Code</td>
<td>Type Embedded in Name</td>
</tr>
<tr>
<td>Refused Bequest</td>
<td>Uncommunicative Name</td>
</tr>
<tr>
<td>Reinvented Wheel</td>
<td>Utility Function</td>
</tr>
<tr>
<td>Repeated Value</td>
<td></td>
</tr>
</tbody>
</table>
<p>For each different code smell identified, we give a set of tell-tale symptoms so you can spot it easily, an indication of what refactorings you can do to remove the smell, and an indication of what other smells you might reveal as you do so. For example, here’s a brief summary of one code smell from the catalog:</p>
<div style="border:1px solid gray;background-color:#eee">
<p><strong>Feature Envy</strong><br>
<em>What To Look For:</em></p>
<ul>
<li> Code references another object more than it references itself, or</li>
<li> Several clients manipulate a particular type of object in similar ways.</li>
</ul>
<p><em>What To Do:</em></p>
<ul>
<li> Extract Method to isolate the envious code</li>
<li> Move Method to put it with the referenced object</li>
</ul>
<p><em>What To Look For Next:</em></p>
<ul>
<li> Duplication: Look for further duplication around the clients</li>
<li> Communication: Review names for the receiving class for consistency</li>
</ul>
</div>
<p><a href="http://www.refactoringinruby.info/">Refactoring in Ruby</a> also provides hundreds of challenges, exercises that will help you practice detecting and removing smells from working Ruby code. For example, take a look at the following code; which smells can you see?</p>
<pre># Creates a report showing machine activity
class Report
  def report(machines)
    statuses = machines.map { |machine| status(machine) }
    "FACTORY REPORT\n#{statuses.join("\n")}\n"
  end

  def status(machine)
    activity = machine.bin == nil ? '' : " bin=#{machine.bin}"
    "Machine #{machine.name}#{activity}\n"
  end
end</pre>
<p>As a companion to the book I have also created a very naive code smell detector tool called <a href="https://github.com/kevinrutherford/reek/wiki">Reek</a>. Reek cannot perform enough static analysis to detect every code smell listed, but it does a reasonable job of detecting some forms of Class Variable, Control Couple, Data Clump, Duplication, Irresponsible Module, Large Class, Long Method, Long Parameter List, Feature Envy, Utility Function, Nested Iterators, Simulated Polymorphism and Uncommunicative Name. For example, consider the little source file above; Reek reports the following code smells:</p>
<pre>$ reek simple_report.rb
simple_report.rb -- 3 warnings:
  Report#status calls machine.bin twice (Duplication)
  Report#status doesn't depend on instance state (LowCohesion)
  Report#status refers to machine more than self (LowCohesion)</pre>
<p>Reek can also produce a YAML report giving extra details such as line numbers:</p>
<pre>$ reek --yaml simple_report.rb
---
- !ruby/object:Reek::SmellWarning
  location:
    lines:
    - 9
    - 9
    context: Report#status
    source: simple_report.rb
  smell:
    class: Duplication
    occurrences: 2
    subclass: DuplicateMethodCall
    call: machine.bin
    message: calls machine.bin twice
  status:
    is_active: true
- !ruby/object:Reek::SmellWarning
  location:
    lines:
    - 8
    context: Report#status
    source: simple_report.rb
  smell:
    class: LowCohesion
    subclass: UtilityFunction
    message: doesn't depend on instance state
  status:
    is_active: true
- !ruby/object:Reek::SmellWarning
  location:
    lines:
    - 8
    context: Report#status
    source: simple_report.rb
  smell:
    class: LowCohesion
    subclass: FeatureEnvy
    receiver: machine
    message: refers to machine more than self
    references: 3
  status:
    is_active: true</pre>
<p>As you can see, this report is a little rough around the edges, and there are still many more code smells that Reek could detect. But even in its current draft state many people find Reek helpful, either as a part of their build process (the Reek gem includes an easy-to-use Rake task) or on an <em>ad hoc</em> basis. If you try it please <a href="http://groups.google.com/group/ruby-reek">let me know</a> how well it works for you.</p>
<p><em>I hope you found this article valuable. Feel free to ask questions and give feedback in the comments section of this post. Thanks!</em></p>
<p><strong><em>Do also read</em> these awesome Guest Posts:</strong></p>
<ul>
<li><a href="http://rubylearning.com/blog/2010/11/08/do-you-know-resque/">Do YOU know Resque?</a></li>
<li><a href="http://rubylearning.com/blog/2010/11/03/do-you-understand-rubys-objects-messages-and-blocks/">Do You Understand Ruby’s Objects, Messages and Blocks?</a></li>
<li><a href="http://rubylearning.com/blog/2010/11/02/how-does-one-use-design-patterns-in-ruby/">How Does One Use Design Patterns In Ruby?</a></li>
<li><a href="http://rubylearning.com/blog/2010/10/26/do-you-know-whats-new-in-ruby-1-9/">Do you know what’s new in Ruby 1.9?</a></li>
<li><a href="http://rubylearning.com/blog/2010/10/25/the-value-of-a-personal-bug-log/">The value of a personal bug log</a></li>
<li><a href="http://rubylearning.com/blog/2010/10/18/do-you-enjoy-your-code-quality/">Do You Enjoy Your Code Quality?</a></li>
</ul>
</div>
<p>Technorati Tags: <a href="http://technorati.com/tag/Reek" rel="tag">Reek</a>, <a href="http://technorati.com/tag/Programming" rel="tag">Programming</a>, <a href="http://technorati.com/tag/Ruby+programming" rel="tag">Ruby programming</a></p>

<div>
<a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:yIl2AUoC8zA"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?d=yIl2AUoC8zA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:MclgosYkiZA"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=rhieerp_F6k:i3FwJQue01k:MclgosYkiZA" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:F7zBnMyn0Lo"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=rhieerp_F6k:i3FwJQue01k:F7zBnMyn0Lo" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:qj6IDK7rITs"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?d=qj6IDK7rITs" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:gIN9vFwOqvQ"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=rhieerp_F6k:i3FwJQue01k:gIN9vFwOqvQ" border="0"></a> <a href="http://feeds.feedburner.com/~ff/LearningRubyBlog?a=rhieerp_F6k:i3FwJQue01k:-BTjWOF_DHI"><img src="http://feeds.feedburner.com/~ff/LearningRubyBlog?i=rhieerp_F6k:i3FwJQue01k:-BTjWOF_DHI" border="0"></a>
</div><img src="http://feeds.feedburner.com/~r/LearningRubyBlog/~4/rhieerp_F6k" height="1" width="1">