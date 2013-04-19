---
layout: post 
title: Sunrise/Sunset Java Library
published: true
sharing: true
comments: true
---

I just wrote a Java library that computes the sunrise / sunset using GPS coordinates and a date.  To use, simply download the tar or zip file from the [GitHub page](http://mikereedell.github.com/sunrisesunsetlib-java/), or use git to pull the source:

{% highlight bash %}
$>git clone git://github.com/mikereedell/sunrisesunsetlib-java
{% endhighlight %}

Once you have the source, use the command:

{% highlight bash %}
$ ant all
{% endhighlight %}

To compile the source, build the bin, src, and test jars, execute the unit tests and generate the JavaDoc for the library.

This code snippet illustrates the main usage pattern:

{% highlight java %}
// Location of sunrise/set, as latitude/longitude.
Location location = new Location("39.9937", "-75.7850");

// Create calculator object with the location and time zone identifier.
SunriseSunsetCalculator calculator = new SunriseSunsetCalculator(location, "America/New_York");

Calendar date = Calendar.getInstance();
String dawn = calculator.getCivilSunriseForDate(date);
String dusk = calculator.getCivilSunsetForDate(date);
{% endhighlight %}

The list of supported time zone identifiers is given by:

{% highlight java %}
//Returns String[] of supported tz identifiers.
TimeZone.getAvailableIDs();
{% endhighlight %}

Any feedback? Comments? Features? Bugs? Comment below.

Released under the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).
