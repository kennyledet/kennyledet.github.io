---
layout: post_page
title: "Web Scraping with Python and BeautifulSoup"
date: 2013-12-19 01:17
comments: true
categories: tutorials
---

In this short and to the point tutorial, we will use the infamous Python module *BeautifulSoup* to code a basic web scraping script that will get some useful project information from Kickstarter.com projects

### Motivation
Knowing how to scrape the web is an excellent skill to have. The web is huge; thousands of new websites are created every day so there is always new content to work with. By extension, this also means that there will always be tons of web scraping projects needing to be done *(this translates into money for us)*.

 And if you already know Python, you really have no excuse to not know how to utilize the language in this area.

<!--more-->

### Technical Requirements
* [Python 2.x+](http://python.org/)
* [BeautifulSoup4](http://www.crummy.com/software/BeautifulSoup/)

## Tutorial
#### Introduction
Simply put, Kickstarter is a *crowdfunding* site where people can get funding for their projects through the power of the internet.

I'll be using [this fairly interesting project](http://www.kickstarter.com/projects/sparkdevices/spark-core-wi-fi-for-everything-arduino-compatible) as a reference, but the techniques we use here will apply to any Kickstarter project page so choose any one that interests you.

First, import the required modules.
{% highlight python %}
# import required modules.
import urllib2
from bs4 import BeautifulSoup
{% endhighlight %}

#### Getting the HTML
We obviously need to get some *HTML* before we start using BeautifulSoup. How you go about getting the page's HTML is something BeautifulSoup leaves up to you. In this tutorial we use [*urllib2*](http://docs.python.org/2/howto/urllib2.html) as it is a standard Python module. 

We can retrieve HTML from a page by passing a *URL* into ```urllib2.urlopen()``` and calling the ```read()``` method on the *HTTP response object* it returns.

{% highlight python %}
url  = 'http://www.kickstarter.com/projects/sparkdevices/spark-core-wi-fi-for-everything-arduino-compatible'
res  = urllib2.urlopen(url)  # response object
html = res.read()
{% endhighlight %}

#### The Soup
In order to start using BeautifulSoup, we need to initialize a *BeautifulSoup object* by passing our HTML string into the _BeautifulSoup() constructor_.

{% highlight python %}
soup = BeautifulSoup(html)
{% endhighlight %}

Once we have this object initialized, we have access to various pieces of general page information right off the bat.

{% highlight python %}
print soup.title.text  # page title
# Spark Core: Wi-Fi for Everything (Arduino Compatible) by Spark Devices — Kickstarter
{% endhighlight %}

#### Getting More Information
This is where your browser's development tools come in *very* handy. I'm using Firefox (Nightly), but Chrome/Chromium and Safari also have a nice set of development tools.

In any case, by right clicking on a *page element* and selecting *Inspect Element* or similar, we can jump to where the element is in the page's *source HTML*.

Let's say we wanted our scraper to know how many backers the Kickstarter project has.


By inspecting one of the elements that contain this information (there are two on each project page), we find that it has an [*HTML attribute*](http://www.htmldog.com/guides/html/beginner/tags/) called ```itemprop``` with a value of ```"Project[backers_count]"``` 

We can use this *attribute/value mapping* to tell BeautifulSoup how to find the element

We can *see* the number of backers, but now let's find this element programmatically with our ```soup``` object by calling its ```find``` method. We'll pass the type of tag (```data``` here) as the first argument, followed by the attribute/value mappings we want to match. These mappings are passed in as keyword arguments.

{% highlight python %}
num_backers_element = soup.find('data', itemprop='Project[backers_count]')
# <data class="Project373368980" data-format="number" data-value="4201" itemprop="Project[backers_count]" value="4201">4,201</data>
{% endhighlight %}

This returns **one** *BeautifulSoup HTML element object*. We can do tons of shit with it, but most relevant right now is the fact that we can access all of its attribute/value mappings by using it as we would any normal Python dictionary and passing its attributes as keys.

Let's get the number of backers as we intended by getting the element's ```value``` attribute.

{% highlight python %}
num_backers = num_backers_element['value']
# '4201' <- yes, this is a string. cast it to an integer by calling int(num_backers)
{% endhighlight %}

So what if we wanted to find *multiple* elements that match our criteria? We use ```find_all```. Remember how the number of backers appears twice on the project page? Let's prove that here.

{% highlight python %}
print soup.find_all('data', itemprop='Project[backers_count]')
# should return a list of all elements with an itemprop of 'Project[backers_count]'
{% endhighlight %}




### Conclusion
If you enjoyed this tutorial, please note that I'll be posting good stuff like this *all the fucking time*. [Follow me on Twitter](http://www.twitter.com/kennyledet) to know when there's something new.