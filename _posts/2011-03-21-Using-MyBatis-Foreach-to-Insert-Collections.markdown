---
layout: post 
title: Using MyBatis to Insert Collections Into MySQL
published: true
---

# {{ page.title }}

<p class="postdate">21 March 2011</p>

I recently updated one of my Java projects from and old iBatis version to the latest and greatest MyBatis 3.0.4.  The conversion part was fairly straightforward so I decided to see what the new library could do to help with performance.

Since the application I'm writing is an ant task that process XML documents into list data that needs to be saved to MySQL, I wanted to see if MyBatis could help out with inserting Collections.

If you google around for 'mybatis insert list' you'll end up on [this page](http://groups.google.com/group/mybatis-user/browse_thread/thread/b81c654041037384/22c448fca4b1c860?pli=1) which shows the following (incorrect) XML snippet:

{% highlight xml %}
INSERT INTO tbl_name (A,B,C) VALUES 
<foreach collection="_parameter" item="parm" open="(" close=")" separator=",">
	#{parm.val1},#{parm.val2},#{parm.val3}
</foreach>;
{% endhighlight %}

Which will generate SQL:

{% highlight sql %}
INSERT INTO tbl_name(A, B, C) VALUES (1, 2, 3, 1, 2, 3, ...)
{% endhighlight %}

After downloading the source, finding the unit test that covered this scenario and adding an additional test for the case I was seeing I discovered that this is the snippet that generates correct MySQL SQL for inserting a list into a table:

{% highlight xml %}
<insert id="insertList" useGeneratedKeys="true" parameterType="java.util.List">
	INSERT INTO tbl_name (A,B,C) VALUES (
	<foreach collection="_parameter" item="parm" open="" close="" separator="),()">
		#{parm.val1},#{parm.val2},#{parm.val3}
	</foreach>
</insert>
)
{% endhighlight %}

Which will output SQL like:

{% highlight sql %}
INSERT INTO tbl_name(A, B, C) VALUES (1, 2, 3), (1, 2, 3), .....
{% endhighlight %}
