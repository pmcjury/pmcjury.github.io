---
layout: post
title:  "Chef: Why won't my data bag work?"
date:   2013-12-31 13:26:11
categories: chef ruby
---

I've come across this problem a few times. I have my data bag uploaded. I can see the data bag json I'm using via knife

{% highlight bash %}
$~ knife node data bag show cool_guys pmcjury
is_cool : true
is_awesome : mostly
can_get_on_it : always
{% endhighlight %}

But in my recipe the variable is never set when accessing the data bag item.

{% highlight ruby %}
pmcjury = Chef::EncryptedDataBagItem.load(:cool_guys, :pmcjury, secret_key)
pmcjury[:is_cool] # will be nil
{% endhighlight %}

I don't know if it's me or what, but I often switch from ruby to php to js to scala to php to java to staring at the screen... Any how enough excuses, switching syntaxes sometimes gets me into trouble. I love symbols in ruby and often like to use them instead of strings inside [Chef](http://www.getchef.com/chef/) recipes to access atrributes.

{% highlight ruby %}
node[:pmcjury][:is_cool] = true
{% endhighlight %}

VS

{% highlight ruby %}
node['pmcjury']['is_cool'] = true
{% endhighlight %}

If you use [foodcritic](http://acrmp.github.io/foodcritic/) ( should also be using [chefspec](https://github.com/sethvargo/chefspec) ), the linter will yell at you if you mix and match string and symbols when accessing attributes. 

However, when accessing data bags you **CAN NOT** use symbols to access the items. I haven't looked at the internals of it, but it's probably something to do with not checking if ```is_a? Symbol``` or conversion of a symbol in a string during interpolation. I could figure it out, but it's beyond the scope of this article.

In conclusion, **USE STRING TO ACCESS DATA BAG ITEMS**. You **cannot** use symbols

{% highlight ruby %}
pmcjury = Chef::EncryptedDataBagItem.load('cool_guys', 'pmcjury', secret_key)
pmcjury['is_cool'] # true... so, so true
{% endhighlight %}

## Happy New Year on the last day of 2013