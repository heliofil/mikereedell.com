---
layout: post 
title: Using Ruby to Help Maven
published: true
sharing: true
comments: true
---

Maven projects can become a wasteland of neglected, old dependencies: huge, unmanageable .pom files  and a local repository that's catalog of every Apache project in the past 10 years.  Who knows if your project still requires that old version of Xerces?

This script takes the name of a dependency and removes it from every pom.xml in any sub-directory from where it's called, removing the dependency element for the given jar file from each one.  The script also saves the dependency tree in the file $artifactname.xml in case you need to put it back.

{% highlight ruby %}
#/usr/bin/env ruby
require 'rexml/document.rb'

if(ARGV[0] == nil)
  puts 'Must provide the name of the artifact to remove.'
  exit
end

artifact = ARGV[0].chomp
poms = Dir[Dir.pwd() + "/**/pom.xml"]

for pom in poms do
  pomXML = REXML::Document.new(File.new(pom))
  element = pomXML.get_elements("//dependency[artifactId='" + artifact + "']")[0]

  if(element != nil)
    temp = File.new(artifact + ".xml", "w+")
    formatter = REXML::Formatters::Default.new()
    formatter.write(element, temp)
    puts "Removing the dependency: " + artifact + "\nfrom " + pom
    element.parent.delete(element)
    pomXML.write(File.new(pom, "w+"))
  else
    puts "No artifacts with the name:'" + artifact + "' were found."
  end
end
{% endhighlight %}
