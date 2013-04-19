---
layout: post 
title: Conditionally Ignoring Test Cases With JUnit Rules
published: true
sharing: true
comments: true
---

With JUnit 4.x, the developers introduced the concept of annotating tests with @Ignore so developers could mark tests that the test runner should ignore. What wasn't added was the ability to specify conditions for which the test should be ignored, for instance:

Ignore this test when running locally:

{% highlight java %}
@IgnoreWhen(env == Env.LOCAL)
public void testSomethingReallyCPUIntensive() {
	...
}
{% endhighlight %}

A bit of google'ing will yield [a lot](http://stackoverflow.com/questions/6096061/is-there-any-conditional-annotation-in-junit-to-mark-few-test-cases-to-be-skippe) [of pages](http://stackoverflow.com/questions/1689242/conditionally-ignoring-tests-in-junit-4) [that](http://programmaticallyspeaking.blogspot.com/2008/10/run-time-equivalent-to-junits-ignore.html) [purport](http://garygregory.wordpress.com/2011/03/12/conditionally-ignoring-tests-in-junit/) to have the solution to this problem or discuss ways to work around it.

While there's nothing wrong with any of these solutions technically, I didn't want to write a custom JUnit test runner nor have code that executed after the class- and method-level setup's were run. In other words, I wanted a conditional short-circuit that would exit the test as soon as possible without running any code the test-to-be-ignored depends on.

That leads us to using JUnit's [@Rule](http://www.junit.org/node/580) feature. By using Rules, we can introduce just the type of short-circuit mechanism mentioned above by creating a rule that applies to all test cases that will determine whether the test case should execute or not.

The case we had to solve was specifying the test data sets that certain tests could run against. If one test had test data available in data set QA01 but not in QA02, we didn't want that test to run, and fail, when the suite was run against QA02.

To solve this, we created a method-level annotation called DataSets:

{% highlight java %}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface DataSets {
	String[] value();
}
{% endhighlight %}

which we then used to annotate our test cases with the data sets that they should be executed on:

{% highlight java %}
@DataSets({"QA01"})
@Test
public void someDataIntensiveTest() {
	assertTrue(....);
}
{% endhighlight %}

To 'enforce' the DataSets annotation, we created a JUnit Rule that would inspect the annotation along with the JVM argument 'dataset' to see if the test should be executed.

{% highlight java %}
public class DataSetRule implements TestRule {

	@Override
	public Statement apply(Statement statement, Description description) {
		DataSets annotation = description.getAnnotation(DataSets.class);
		if(annotation == null) {
			return statement;
		}
		String testDataSet = System.getProperty("dataset");

		List<String> allowedDataSets = Arrays.asList(annotation.value());
		if (allowedDataSets.contains(testDataSet)) {
			return statement;
		}

		return new Statement() {

			@Override
			public void evaluate() throws Throwable {
				// Return an empty Statement object for those tests 
				// that shouldn't run on the specified dataset.
			}
		};
	}
}
{% endhighlight %}

This rule checks the value of the annotation, and if the 'dataset' JVM argument is contained in the list of allowed data sets (or the annotation isn't present at all), passes the test (Statement object) unaltered to the next step in the execution sequence. If the 'dataset' JVM argument is not in the list of allowed data sets, the rule creates a new, empty Statement object which then gets executed in place of the actual test case.

To put the DataSetRule into effect, we need to decorate our test classes with one more annotation:
{% highlight java %}
@Rule
public TestRule dataSetRule = new DataSetRule();
{% endhighlight %}

This snippet lets JUnit know that there's a rule that needs to be executed before each test case gets run. There's also a ClassRule interface that can be implemented to create JUnit rules that are executed before any @BeforeClass methods are run.

While this solution isn't perfect: it still shows the tests as being run and passing, albeit with an execution time approaching zero, it does allow JUnit tests to be decorated with custom annotations that control the execution of the test

For more information on JUit rules:
* [http://cwd.dhemery.com/2011/01/what-junit-rules-are-good-for/](http://cwd.dhemery.com/2011/01/what-junit-rules-are-good-for/)
* [http://cwd.dhemery.com/2010/12/junit-rules/](http://cwd.dhemery.com/2010/12/junit-rules/)
