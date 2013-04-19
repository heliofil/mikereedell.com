---
layout: post 
title: RubySunrise Gem
published: true
sharing: true
comments: true
---

As a follow-up to the Java sunrise/sunset calculator I released early last year, I'm releasing a ruby gem that does essentially the same thing.  Just in A LOT less code, but with the same attention to test goodness.

To install and use the RubySunrise gem just:
{% highlight bash %}
$ gem install RubySunrise
{% endhighlight %}

or download from [RubyGems](http://rubygems.org/gems/RubySunrise).

The current version as of today is 0.2, which requires the [tzinfo](http://tzinfo.rubyforge.org/) gem to perform the timezone offset lookups.

If you want/need the lastest, just grab it from the github repo:
{% highlight bash %}
$ git clone git://github.com/mikereedell/sunrisesunset-ruby
$ gem build RubySunrise
{% endhighlight %}

At this time the RubySunrise gem covers astronomical, nautical, civil, and official sunrise/sunset calculations for every location that experiences a sunrise and sunset every day (Sorry Alaska!).  I'm currently working on the strangeness that is causing my Alaskan test suite to fail with known-good data.

Example time:
{% highlight ruby %}
require 'solareventcalculator'

date = Date.parse('2008-11-01')
calc = SolarEventCalculator.new(date, BigDecimal.new("39.9537"), BigDecimal.new("-75.7850"))

utcOfficialSunrise = calc.compute_utc_official_sunrise
localOfficialSunrise = calc.compute_official_sunrise('America/New_York')

puts "utcOfficialSunrise #{utcOfficialSunrise}"
puts "localOfficialSunrise #{localOfficialSunrise}"
{% endhighlight %}

Which yields:
{% highlight bash %}
utcOfficialSunrise 2008-11-01T11:33:00+00:00
localOfficialSunrise 2008-11-01T07:33:00-04:00
{% endhighlight %}

The source is available under the Apache License, Version 2.0 at [Github](http://github.com/mikereedell/sunrisesunset-ruby)
