---
layout: post
title:  "使用Google Authenticator实现SSH两步认证"
date:   2011-09-26 23:30:49
author: yuanyi
categories: program
---

## 使用Google Authenticator实现SSH两步认证
### by yuanyi
### at 2011-09-26 23:30:49
### original <http://item.feedsky.com/~feedsky/heikezhi/~8608072/569336371/6713895/1/item.html>

<p>最近，为了能够通过PCI（安全支付行业标准）的自我测评，我得到了几台服务器，其中的一个要求时需要通过两步认证来访问这些服务器，我询问了是否可以使用SSH Key加密码（Passphrase），但是没有得到一个明确的答复，于是我决定自己动手来实现SSH的两步认证，正好我也对这个很感兴趣。</p>
<p>经过一番调研之后，我发现我可以使用<a href="http://code.google.com/p/google-authenticator/">Google Authenticator</a>的<a href="http://code.google.com/p/google-authenticator/source/browse/#hg%2Flibpam">PAM认证模块</a>，但是它只支持SSH密码认证，而我使用的是SSH key认证。</p>
<p><strong>意想不到</strong></p>
<p>我想找一种最简单的实现方式，所以我决定先从SSH开始，看看有没什么特性可供利用，很快，我就发现SSH的authorized_keys文件有一个选项，可以为通过public key方式登录的用户设置一个钩子命令：</p>
<p>command="/usr/bin/my_script" ssh-dsa AAA...zzz me@example.com</p>
<p>command选项可以让你为每个key设置一条不同的命令，下面就让我们开始集成Google Authenticator吧！</p>
<p><strong>更新</strong>：根据Hacker news上的评论，你也可以在sshd_config中配置ForceCommand选项来设置一个全局钩子。</p>
<p><strong>简单实现</strong></p>
<p>我选择了ruby来编写我的第二步认证脚本，但理论上，你可以使用任何你喜欢的语言，下面的代码只是为了验证概念，要运行下面的代码，你需要安装<a href="http://rubygems.org/gems/rotp">rotp</a>，一个ruby的HOTP/TOTP协议实现：</p>
<p>gem install rotp</p>
<p>现在，将下面的代码保存到 /usr/bin/two_factor_ssh：</p>
<pre name="code">
#!/usr/bin/env ruby
require 'rubygems'
require 'rotp'

# we'll pass in a secret to this script from the authorized_keys file
abort unless secret=ARGV[0]

# prompt the user for their validation code

STDERR.write "Enter the validation code: "
until validation_code=STDIN.gets.strip
  sleep 1
end

# check the validation code is correct

abort "Invalid" unless validation_code==ROTP::TOTP.new(secret).now.to_s

# user has validated so we'll give them their shell

Kernel.exec ENV['SSH_ORIGINAL_COMMAND'] || ENV['SHELL']
</pre>
<p>这里的诀窍就是kernel.exec，如果通过了验证，它会继续调用用户原来正在执行的命令，或是给用户一个全新的shell，从而让整个认证过程更加顺畅。</p>
<p><strong>生成密钥</strong></p>
<p>下面，我们需要为Google Authenticator和服务器生成一个共享密钥，我写了一个简单的脚本：</p>
<pre name="code">
#!/usr/bin/env ruby
require &#39;rubygems&#39;
require &#39;rotp&#39;

secret=ROTP::Base32.random_base32
data=&quot;otpauth://totp/#{`hostname -s`.strip}?secret=#{secret}&quot;
url=&quot;https://chart.googleapis.com/chart?chs=200x200&amp;chld=M|0&amp;cht=qr&amp;chl=#{data}&quot;

puts &quot;Your secret key is: #{secret}&quot;
puts url
</pre>
<p>运行这个脚本得到：</p>
<p>Your secret key is: 4rr7kc47sc5a2fgt</p>
<p>https://chart.googleapis.com/chart?chs=200x200&amp;chld=M|0&amp;cht=qr&amp;chl=otpauth://totp/myserver?secret=4rr7kc47sc5a2fgt</p>
<p>现在，通过Google Authenticator扫描生成的二维码，然后更新authorized_keys:</p>
<p>command="/usr/bin/two_factor_ssh 4rr7kc47sc5a2fgt" ssh-dsa AAA...zzz me@example.com</p>
<p>大功告成！</p>
<p>更新： 感谢<a href="http://twitter.com/js4all">@js4all</a>提醒，OpenSSH的某个特定版本可能会打印出正在执行的命令（以及参数），这样我们的密钥就暴露了，解决办法就是在命令行只传递一个标识符，然后在脚本内再通过这个标识符去查找真正的密钥。</p>
<p><strong>试试看</strong></p>
<p>[richard@mbp ~]$ ssh moocode@myserver<br>
Enter the validation code: wrong<br>
Invalid<br>
Connection to myserver closed.<br>
[richard@mbp ~]$<br>
[richard@mbp ~]$ ssh moocode@myserver<br>
Enter the validation code: 410353<br>
moocode@myserver:~$</p>
<p>不错，和我预期的完全一样。</p>
<p><strong>再进一步</strong></p>
<p>这里有一个<a href="http://bit.ly/qWP2Ns">更完善的例子</a>可以支持根据IP地址自动保存登录信息一段时间，这样，如果你使用同一个IP登录，就不用每次都输入验证码了。</p>
<p>这个例子里面包含了一些简单的日志记录，但是我更愿意使用监控（audit）系统来做这件事（这也是PCI的一项要求），这样就可以记录某个key在什么时间登录过服务器，以及它的认证结果。</p>
<p>或许我们还应该增加一种备份机制（一个主key或是5个一次性的code，就像Google做的那样），以保证我们自己不会在某些意外情况下被挡在外面，如果你有任何好点子，欢迎留言，另外，下周我会再写一篇跟进文章，如果你感兴趣，欢迎通过Twitter <a href="https://twitter.com/moocode">follow我</a>。</p>
<p>-----------<br>
本文来自"<a href="https://moocode.com/posts/5-simple-two-factor-ssh-authentication">Simple Two-Factor SSH Authentication</a>"，作者：<a href="http://twitter.com/moomerman">Richard Taylor </a>，翻译：<a href="http://twitter.com/yuanyiz">@yuanyiz</a></p>
<p>想和我们一道传播黑客精神？<a href="http://heikezhi.com/join">快来加入吧！</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="16条技巧让你更高效使用SSH" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F08%2F26%2Fssh-productivity-tips%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F09%2F26%2Fsimple-two-factor-ssh-authentication-with-google-authenticator%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">16条技巧让你更高效使用SSH</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Gate One: 基于HTML5的Web SSH客户端" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F10%2F20%2Fgate-one-html5-based-web-terminal%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F09%2F26%2Fsimple-two-factor-ssh-authentication-with-google-authenticator%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/10/20/9500496.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Gate One: 基于HTML5的Web SSH客户端</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Google SPDY初探：HTTP 1.1之外的世界" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F11%2Flife-beyond-http-11-googles-spdy%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F09%2F26%2Fsimple-two-factor-ssh-authentication-with-google-authenticator%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/05/11/8400316.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Google SPDY初探：HTTP 1.1之外的世界</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="在 Google Chrome 中运行 Termkit" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F06%2F15%2Fplaying-with-termkit-with-chrome%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F09%2F26%2Fsimple-two-factor-ssh-authentication-with-google-authenticator%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/06/15/12395921.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">在 Google Chrome 中运行 Termkit</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="4" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/569336371/heikezhi/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/heikezhi/~8608072/569336371/6713895/1/item.html" border="0" height="0" width="0">