---
layout: post 
title: Sunrise-WS - A Super Simple Sinatra Wrapper for RubySunrise
published: true
---

# {{ page.title }}

<p class="postdate">11 August 2010</p>

Just did a push to github of [sunrise-ws](http://github.com/mikereedell/Sunrise-WS), a super simple [Sinatra](http://www.sinatrarb.com/) wrapper for the RubySunrise gem. The concept is simple: call a web-service to get the sunrise/sunset data for a given location in a given timezone. Since the requirements were so simple and there is no database or security, it made sense to use Sinatra instead of rails. First off, Sinatra is easy. I know I'm not breaking new ground here, but as a 10-year professional java developer, getting ANYTHING done in that little code is next to impossible. I certainly appreciated the ease of setup of the Sinatra app and how easy it was to test it using RSpec.

Like the title says, this app is SIMPLE. It only exposes three web-services: get the sunrise/sunset data for today, a specified date, or for the entire year. That's it. And instead of using XML as the response, everything is in JSON.

For more information on how to use it, go to the project's  [README](http://github.com/mikereedell/Sunrise-WS/blob/master/README.md) on github.

If you'd like to try it out without having to run it locally, it's been deployed to [Heroku](http://heroku.com). Hit the following links to try it out:
* [http://fierce-fire-51.heroku.com/sunrise/today/39.9537/-75.8301537/America/New_York](http://fierce-fire-51.heroku.com/sunrise/today/39.9537/-75.8301537/America/New_York)
* [http://fierce-fire-51.heroku.com/sunrise/2004-01-31/39.9537/-75.8301537/America/New_York](http://fierce-fire-51.heroku.com/sunrise/2004-01-31/39.9537/-75.8301537/America/New_York)
* [http://fierce-fire-51.heroku.com/sunrise/year/39.9537/-75.8301537/America/New_York](http://fierce-fire-51.heroku.com/sunrise/year/39.9537/-75.8301537/America/New_York)

To get a better visualization of the JSON output, install the JSON bundle for TextMate or use this [JSON Visualization](http://chris.photobooks.com/json/default.htm) page.