---
layout: post
title:  "SimpleForm 1.3: more HTML 5 goodness and new stuff"
date:   2010-12-09 03:49:22
author: Carlos Antônio
categories: program
---

## SimpleForm 1.3: more HTML 5 goodness and new stuff
### by Carlos Antônio
### at 2010-12-09 03:49:22
### original <http://feedproxy.google.com/~r/PlataformaBlog/~3/FZLpBU0Ud7k/>

<p>We have been working on SimpleForm for some time since the last release and have got a lot of contributions from community. Now it is time for a new release with more HTML 5 compatibility plus some new cool features. So, without further ado, lets take a ride on the new stuff.</p>
<h3>HTML 5</h3>
<p>One of the most useful features coming in HTML 5, in my opinion, is the placeholder option. This option allows us to configure a text to be shown inside the input when it is empty. This is really nice to help the user while filling out forms. SimpleForm now gives us the possibility to pass in a placeholder option in the same way we are used to do with use hints:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-weight:bold">&lt;%</span>= simple_form_for <span style="color:#0066ff;font-weight:bold">@user</span> <span style="color:#9966cc;font-weight:bold">do</span> <span style="color:#006600;font-weight:bold">|</span>f<span style="color:#006600;font-weight:bold">|</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:username</span>, <span style="color:#ff3333;font-weight:bold">:label</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#996600">'Your username please'</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:password</span>, <span style="color:#ff3333;font-weight:bold">:hint</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#996600">'No special characters.'</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:email</span>, <span style="color:#ff3333;font-weight:bold">:placeholder</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#996600">'user@domain.com'</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">button</span> <span style="color:#ff3333;font-weight:bold">:submit</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
<span style="color:#006600;font-weight:bold">&lt;%</span> <span style="color:#9966cc;font-weight:bold">end</span> <span style="color:#006600;font-weight:bold">%&gt;</span></pre></div></div>

<p>As you can see here, the placeholder is given as String, but it can also be fetched from I18n, as labels/hints does.</p>
<p>Another addition is the automatic lookup of min/max values from numericality validations, for <code>number</code> inputs. For instance:</p>

<div><div><pre style="font-family:monospace"><span style="color:#9966cc;font-weight:bold">class</span> User
  validates_numericality_of <span style="color:#ff3333;font-weight:bold">:age</span>, <span style="color:#ff3333;font-weight:bold">:greater_than_or_equal_to</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#006666">18</span>,
    <span style="color:#ff3333;font-weight:bold">:less_than_or_equal_to</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#006666">99</span>, <span style="color:#ff3333;font-weight:bold">:only_integer</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#0000ff;font-weight:bold">true</span>
<span style="color:#9966cc;font-weight:bold">end</span></pre></div></div>


<div><div><pre style="font-family:monospace"><span style="color:#006600;font-weight:bold">&lt;%</span>= simple_form_for <span style="color:#0066ff;font-weight:bold">@user</span> <span style="color:#9966cc;font-weight:bold">do</span> <span style="color:#006600;font-weight:bold">|</span>f<span style="color:#006600;font-weight:bold">|</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:age</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
<span style="color:#006600;font-weight:bold">&lt;%</span> <span style="color:#9966cc;font-weight:bold">end</span> <span style="color:#006600;font-weight:bold">%&gt;</span></pre></div></div>

<p>Would generate an input with type number, and the min/max attributes configured with 18 and 99, respectively.</p>
<p>Besides that SimpleForm also adds:</p>
<ul>
<li>the <code>:required</code> html attribute for required inputs (it is retrieved automatically from your presence validations);</li>
<li>the <code>:search</code> and <code>:tel</code> input types, with <code>:tel</code> mapping automatically for attributes matching <code>/phone/</code>.</li>
</ul>
<h3>Collections</h3>
<p>From now on, radio and check box collections will wrap the input element inside the label, making it pretty straightforward to associate both elements. Besides that, SimpleForm now comes with two new configurations:</p>
<ul>
<li><code>collection_wrapper_tag</code> wraps the entire collection in the configured tag;</li>
<li><code>item_wrapper_tag</code> wraps each item in the collection using the configured tag.</li>
</ul>
<p>An example:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-weight:bold">&lt;%</span>= simple_form_for <span style="color:#0066ff;font-weight:bold">@user</span> <span style="color:#9966cc;font-weight:bold">do</span> <span style="color:#006600;font-weight:bold">|</span>f<span style="color:#006600;font-weight:bold">|</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">association</span> <span style="color:#ff3333;font-weight:bold">:roles</span>, <span style="color:#ff3333;font-weight:bold">:as</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#ff3333;font-weight:bold">:check_boxes</span>, 
    <span style="color:#ff3333;font-weight:bold">:collection_wrapper_tag</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#ff3333;font-weight:bold">:ul</span>, <span style="color:#ff3333;font-weight:bold">:item_wrapper_tag</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#ff3333;font-weight:bold">:li</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
<span style="color:#006600;font-weight:bold">&lt;%</span> <span style="color:#9966cc;font-weight:bold">end</span> <span style="color:#006600;font-weight:bold">%&gt;</span></pre></div></div>

<p>This should be kind of self explanatory =).</p>
<h3>New input options</h3>
<p>It’s now possible to give the <code>:disabled</code> option straight to the input, which will also add the <code>disabled</code> css class to both input and wrapper elements:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-weight:bold">&lt;%</span>= simple_form_for <span style="color:#0066ff;font-weight:bold">@user</span> <span style="color:#9966cc;font-weight:bold">do</span> <span style="color:#006600;font-weight:bold">|</span>f<span style="color:#006600;font-weight:bold">|</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:email</span>, <span style="color:#ff3333;font-weight:bold">:disabled</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#0000ff;font-weight:bold">true</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
<span style="color:#006600;font-weight:bold">&lt;%</span> <span style="color:#9966cc;font-weight:bold">end</span> <span style="color:#006600;font-weight:bold">%&gt;</span></pre></div></div>

<p>And also the <code>:components</code> option, which will only render the given components in the given order:</p>

<div><div><pre style="font-family:monospace"><span style="color:#006600;font-weight:bold">&lt;%</span>= simple_form_for <span style="color:#0066ff;font-weight:bold">@user</span> <span style="color:#9966cc;font-weight:bold">do</span> <span style="color:#006600;font-weight:bold">|</span>f<span style="color:#006600;font-weight:bold">|</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
  # Generates the label after the input, and ignores errors/hints/placeholders
  <span style="color:#006600;font-weight:bold">&lt;%</span>= f.<span style="color:#9900cc">input</span> <span style="color:#ff3333;font-weight:bold">:email</span>, <span style="color:#ff3333;font-weight:bold">:components</span> <span style="color:#006600;font-weight:bold">=&gt;</span> <span style="color:#006600;font-weight:bold">[</span><span style="color:#ff3333;font-weight:bold">:input</span>, <span style="color:#ff3333;font-weight:bold">:label</span><span style="color:#006600;font-weight:bold">]</span> <span style="color:#006600;font-weight:bold">%&gt;</span>
<span style="color:#006600;font-weight:bold">&lt;%</span> <span style="color:#9966cc;font-weight:bold">end</span> <span style="color:#006600;font-weight:bold">%&gt;</span></pre></div></div>

<h3>New configuration options</h3>
<p>If you are not using any label / hint / placeholder with I18n, you can now completely disable the translation lookup of these components by setting the <code>config.translate</code> to <code>false</code> in your SimpleForm initializer. This should improve performance a bit in these cases.</p>
<p>Another nice improvement is the ability to add custom input mappings to SimpleForm. If you ever needed to map a specific attribute to a default input, now you can:</p>

<div><div><pre style="font-family:monospace">  config.<span style="color:#9900cc">input_mappings</span> = <span style="color:#006600;font-weight:bold">{</span> <span style="color:#006600;font-weight:bold">/</span>_count$<span style="color:#006600;font-weight:bold">/</span> <span style="color:#006600;font-weight:bold">=&gt;</span> :<span style="color:#cc0066;font-weight:bold">integer</span> <span style="color:#006600;font-weight:bold">}</span></pre></div></div>

<p>This configuration expects a hash containing a regexp to match as key, and the input type that will be used when the field name matches the regexp as value. In this example we match all attributes ending with <code>_count</code>, such as <code>comments_count</code>, to be rendered as integer input by SimpleForm.</p>
<h3>New docs and mailing list</h3>
<p>SimpleForm now has its own <a href="http://groups.google.com/group/plataformatec-simpleform" title="SimpleForm Google Group">google group</a> where you can ask questions, search for already answered questions and also help others. Besides that, you can also navigate and search the entire <a href="http://rubydoc.info/github/plataformatec/simple_form/master/frames" title="SimpleForm RDoc">RDoc</a>.</p>
<h3>Wrapping up</h3>
<p>As you can see, there are plenty of new and cool stuff in this release. We encourage you to take a look at the <a href="https://github.com/plataformatec/simple_form/blob/master/CHANGELOG.rdoc" title="SimpleForm Changelog">CHANGELOG</a> and also review the <a href="https://github.com/plataformatec/simple_form/blob/master/README.rdoc" title="SimpleForm Readme">README</a> to see what else is available and some more examples.</p>
<p>And please, check out <a href="https://github.com/plataformatec/simple_form/contributors" title="SimpleForm contributors">SimpleForm contributors</a>, we want to thank everyone who is helping us to improve SimpleForm.</p>
<p>What about you? Do you want any cool feature in SimpleForm? Help us improve it by forking and sending us a pull request, we will be really glad to apply it. We hope to see your name in the contributors page soon!</p>
<p>Finally, in your opinion, what is the coolest feature SimpleForm has? And what idea you have you might want to be added to SimpleForm? Feel free to comment <img src="http://blog.plataformatec.com.br/wp-includes/images/smilies/icon_biggrin.gif" alt=":D"> </p>
<img src="http://feeds.feedburner.com/~r/PlataformaBlog/~4/FZLpBU0Ud7k" height="1" width="1">