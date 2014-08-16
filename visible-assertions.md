---
layout: plain
title: Visible Assertions
categories: major
---

# Visible Assertions

TestPackage includes `org.testpackage.VisibleAssertions`, which is a lightweight re-implementation of `org.junit.Assert`. Unlike `Assert`, `VisibleAssertions` augments the assertion errors it raises with a running commentary on standard out. Some of the API of `VisibleAssertions` is summarised below.

Please note that TestPackage will generally suppress standard out unless a test fails, so you'd not normally expect to see this output on the command line for passing tests.

## assertTrue(*String message, boolean value*)

Aimed at simple boolean condition evaluation, although often `assertEquals` and `assertThat` will yield more informative output. For example:

    assertTrue("some boolean condition is as expected", myObject.isSomethingTrue());
    assertTrue("another boolean condition is as expected", myBadObject.isSomethingTrue());

Yields:

<pre><code><span class="green">&#10004; some boolean condition is as expected</span>
<span class="red">&#10008; another boolean condition is as expected</span></code></pre>

## assertEquals(*String message, Object expected, Object actual*)
Tests two objects for equality, and describes the difference (as a toString()) if they are not equal. For example:

    assertEquals("some thing is as expected", myExpectedObject, theActualObjectWhichIsEquals);
    assertEquals("some other thing is as expected", "my expected String", "the actual String");

Yields:

<pre><code><span class="green">&#10004; some thing is as expected</span>
<span class="red">&#10008; some other thing is as expected</span>
<span class="yellow">        'the actual String' does not equal expected 'my expected String'</span></code></pre>


##assertThat(*String whatTheObjectIs, T actual, Matcher<? super T> matcher*)

Brings the power of Hamcrest matchers to bear, and additionally uses the matchers to help construct an informative commentary. 
Rather than describing a condition, the first parameter can just be a noun describing the object which is being tested. For example:

    assertThat("the thing", "a String", is(equalTo("a String")));
    assertThat("the thing", "my actual String", is(equalTo("the expected String")));

Yields:

<pre><code><span class="green">&#10004; the thing is "a String"</span>
<span class="red">&#10008; assertion on the thing failed</span>
<span class="yellow">        asserted that it is "the expected String" but was "my actual String"</span></code></pre>

## Other methods

Other visible assertions methods follow similar patterns of usage and presentation style:

 * assertNull(String message, Object o)
 * assertNotNull(String message, Object o)
 * assertSame(String message, Object expected, Object actual)
 * fail(String message)
   
