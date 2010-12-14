---
layout: post 
title: Licensing Your Code the Ruby Way
published: true
---

# {{ page.title }}

<p class="postdate">31 January 2009</p>

Do you have an existing project you want to open-source?  Does the thought of manually adding the license comment block to EVERY. SINGLE. SOURCE. FILE. sound not-so-exciting?  Here's some help, ruby style:

{% highlight ruby %}
class Licenser

    def licenseFiles(dir)
          filenames = Dir[dir.chop + "**/*.java"]
	  for filename in filenames do
              contents = ''
              File.open(filename, 'r') { |file| contents = file.read }
              contents = HEADER + contents
              File.open(filename, 'w') { |file| file.write(contents) }
          end
    end
end

HEADER = &lt;&lt;HEAD
/*
 * Copyright 2008-2009 Mike Reedell / LuckyCatLabs.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

HEAD

Licenser.new.licenseFiles(ARGV[0])
{% endhighlight %}

This script was inspired by the the [check_license_headers.rb](http://svn.apache.org/viewvc/incubator/ode/trunk/tasks/check_license_headers.rb?revision=524802&view=markup&pathrev=524802) script in the Apache SVN repo.  Couldn't get it to work on my setup, so I rewrote the parts I needed.

This can be found in the [GitHub scripts project](http://github.com/mikereedell/scripts/tree/master).  Look for updates to handle more source types and to check for existing license headers.