---
layout: post 
title: POST'ing Form Data to CSRF-protected Forms With Cocoa
published: true
---

# {{ page.title }}

<p class="postdate">21 December 2010</p>

While writing [MacGist](http://luckycatlabs.com/macgist.html), I had to engineer my way around [GitHub's](http://github.com) lack of API support for gists. The [gist API](http://develop.github.com/p/gist.html) doesn't allow for much, just simple GET's around the gists metadata and content, for public gists only. The only way around these limitations that I saw was to programmatically access the web interface of [gist.github.com](http://gist.github.com) and POST the data in the HTTP headers.

Sounds easy enough.

And it was, but my lovingly crafted HTTP POST's didn't actually do anything. Some quick Firebug'ing uncovered that Github uses [CSRF](http://en.wikipedia.org/wiki/CSRF) form fields to protect themselves from, well, CSRF attacks. Without this uniquely generated key present in the POST data, the requests weren't going to do anything.

Here's the solution I came up with:
1. Login to github using standard username/password authentication (BASIC auth over https).
1. Construct the URL of the form you want to POST to, in my case the URL has the form https://gist.github.com/gists/$GIST_ID/edit.
1. GET the form URL.
1. Get the "authenticity_token" hidden form field from the HTML content.
1. Use the value of the "authenticity_token" in the POST data.
1. POST the form.

Getting the authenticity_token from the HTML form data is pretty straight-forward:

{% highlight objectivec %}
+ (NSString *) getAuthenticityTokenForUrl:(NSString *) url {
    ASIHTTPRequest *request = [ASIHTTPRequest requestWithURL:[NSURL URLWithString:url]];
    [request startSynchronous];

    NSError *error = [request error];
    if(error) {
        NSLog(@"Error getting authenticity_token from github for url: %@ with error: %@",
            url, [error localizedDescription]);
    }

    NSString *textData = [request responseString];
    NSRange location = [textData rangeOfString:@"authenticity_token" options:NSLiteralSearch];

    if(location.location != NSNotFound) {
        location.location = location.location + 41;
        location.length = 40;
        NSString *formData = [textData substringWithRange:location];
        return formData;
    }
    return nil;
}
{% endhighlight %}

In this solution, we're essentially grep'ing the HTML for the string "authenticity_token" then returning the next 40 characters. There's more than one hammer for this nail: [NSScanner](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSScanner_Class/Reference/Reference.html), regular expressions, parsing the XHTML and walking the DOM, but for something quick and easy, this one works just as well.