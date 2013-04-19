---
layout: post 
title: Maven with Ruby Part II
published: true
sharing: true
comments: true
---

Want to add something to your projects Maven builds (like code coverage...) but don't want to CTL-C/CTL-V your way to boredom?  Use the below script to lend a hand.  Substitute the XML in the SNIPPET variable to the artifact/plugin/dependency to be injected (leave the 'inject' tag in place) and use the command:

{% highlight bash %}
$>ruby mavenInjector.rb /xpath/to/element
{% endhighlight %}

Here's the code.  Check the [git repository](http://github.com/mikereedell/scripts/tree/master) for the latest.

In the beginning of the 'for' loop in the inject method I remove the read-only bit from the file.  This is for those of us afflicted with ClearCase and can be removed for those fortunate enough to use a sane SCM.

{% highlight ruby %}
#/usr/bin/env ruby
require 'rexml/document.rb'
require 'fileutils.rb'

class MavenInjector

  def inject(path)
    if(path == nil)
      puts 'Must provide the root path of the node to inject under.'
      exit
    end

    poms = Dir[Dir.pwd() + "/**/pom.xml"]

    for pom in poms do
      if(!File.writable?(pom))
        FileUtils.chmod 0644, pom
      end

      pomXML = REXML::Document.new(File.new(pom))
      element = pomXML.get_elements(path)[0]

      if(element == nil)
        parentElement = pomXML.get_elements("/project/build")[0]
        element = REXML::Element.new("plugins", parentElement)
      end

      for plugin in getElementsToInject() do
        element.add_element(plugin)
      end

      formatter = REXML::Formatters::Default.new(4)
      formatter.write(pomXML, File.new(pom, "w+"))
    end
  end

  def getElementsToInject()
    elementXML = REXML::Document.new(SNIPPET)
    return elementXML.get_elements("/inject/plugin")
  end
end

SNIPPET = <<head
<inject>
  <plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>cobertura-maven-plugin</artifactId>
    <configuration>
        <formats>
          <format>html</format>
        </formats>
    </configuration>
  </plugin>
</inject>
HEAD

MavenInjector.new.inject(ARGV[0])
{% endhighlight %}

Any questions/problems/enhancements are welcome in the comments.
