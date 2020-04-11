---
layout: post
title: Java 8
categories: Java
---

At the time of writing, version 13 of the Java JDK is currently available and at the current rate, 14 won't be too far away. If like me you spend time between multiple languages it can be hard to keep track of the useful (and not so useful) features of a language. Java 8 was perhaps the most profound update amongst all of these, offering many feature rich enhancements that Java programmers now take for granted.

This article aims to pin down, consolidate the most important features from Java 8 as a basis for moving onto other more recent offerings in Java.

### Functional Interfaces
Functional interfaces are interfaces which contain only one method. They support the use of lambdas in Java which in turn can help developers to write code with more functional paradigms.

Functional interfaces can be imposed at compile time by the <b>@FunctionalInterface</b> annotation on declaring an interface, however it is not essential to be able to define them.

{% highlight java %}
@FunctionalInterface
interface MyFirstFunctionalInterface {
    public void iHadJustOneJob();

    default void myDefaultFunc() {
        System.out.println("Default func");
    }
}
{% endhighlight %}

While I am not a fan of some of the extra features provided, Java 8 also brings the <b>default</b> keyword which allows you to define a default implementation for a method and we are now also allowed to create static methods as part of the interface. The first of these effectively enables multiple class inheritance through interfaces. In my view, default implementations should be left to abstract classes or regular base level classes for cleaner intent but it's useful feature to know of. It should be noted that default functions do not count towards the one function limit either in a Functional Interface.

### Lambdas
Perhaps the most significant addition to Java 8 is the introduction of lambda functions. If you unfamiliar with lambdas, put simply lambdas are functions without a name. Similar to anonymous functions they are defined inline and often defined and passed directly into other functions as parameters. This very feature gives way to functional paradigms and designs not previously available in native Java. Functions no longer need to be tied to objects and some of the many benefits of functional programming follow this, including the ability to run functions lazily (on-demand) or being able to treat functions as first class citizens rather than objects. 

A function argument that takes a Functional Interface or a single function Interface can use lambdas in place of anonymous functions or object instances.
The Java <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/Runnable.html">Runnable</a> is a good example of this, as you can see a Runnable could be defined simply with an inline lambda:

{% highlight java %}
// anonymous function
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("My old anonymous function");
    }
});
// lambda - Runnable has one method and can be considered as a functional interface
new Thread(() -> { System.out.println("My nicer looking lambda function."); });
{% endhighlight %}

The empty brackets define the arguments to my function, which in this case is empty as the only function in Runnable takes none. The type of these arguments is implied based on the Functional Interface and therefore only the parameter names are required. As this is a single line statement you can also remove the need for curly braces entirely. 

If I wanted to, in some cases I can make the method static so I can re-use this in other places with the function reference notation. By using the class name followed by two colons this will enable me to take the static create function as a reference. In this case it's not all that useful but you can imagine some use cases where it could be.

{% highlight java %}
public class DefaultRunnableFactory {
    public static void create() {
        System.out.println("My Default Runnable");
    }
}

new Thread(DefaultRunnableFactory::create);

{% endhighlight %}

Method references or the double colon notationare often used a shorthand when using streams which are described in the next section.

### Streams
A stream is a new type that enables developers to transform existing data structures into new projections while leaving the underlying data untouched. This is performed through a pipeline of data transformations on the original data source. These are called <b>intermediate operations</b> and each transformation will return a new stream for any subsequent operation. Intermediate operations are lazily evaluated and will often reduce streams to smaller ones with filter, map and removal functions. This can be very handy for either writing shorthands or when you need to break down large data sets. Essentially the idea behind streams is to compute only the values we need through a series of functions. Once we are done with the stream we can return the resultant with a terminal operator such as <b>findFirst</b>, <b>collect</b>, <b>toArray</b>, <b>match</b> or many others that are available as part of the Stream API.

There are multiple ways to create streams. For collections, the easiest way to is to simply call <b>stream()</b> on the collection. There are many different computations and terminations you can perform that you will be able to find <a href="https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html">here</a>. This page is simply aimed as an overview of the feature. The Java API mentions many ways to create specific types of streams, but mostly you will be using <b>Stream.of(...)</b> or <b>stream()</b> on existing collections.

Here in this example code, we take a list of available users as a stream by invoking <b>stream()</b> before running a <b>filter</b> on all non-null users that contain the string "jack". With this result set we then re-format the username by surname first, forename last using the <b>map</b> function. Lastly we then filter out any invalid usernames based on the previous map function and then use the <b>ordered</b> function to sort users alphabetically.
{% highlight java %}
List<String> users = new ArrayList<>();
users.add("jack,smith");
users.add("jack,smith");
users.add("jake,smith");
users.stream()
    .filter(u -> u!=null && u.contains("jack"))
    .map(u -> {
        String[] names = u.split(',');
        if (u.length<2)
            return "";
        return String.format("%s, %s", u[1], u[0]);
    )
    .filter(String::isEmpty)
    .ordered()
    .collect(Collectors.toList());
{% endhighlight %}

There are ways to parallelise streams to make use of the default <a href="https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html">Fork-Join</a> thread pool in Java. To do this, you can call <b>parallelStream()</b> on collections or <b>parallel()</b> on existing streams before executing other intermediary functions. This does however potentially clog the thread pool across your application from doing more useful work. My recommendation is to avoid parallel streams and instead submit lambdas against separate custom thread pools to avoid unnecessary contention.

### forEach operator and new collection operators
The iterable interface now has a forEach function which you can use in place of iterating over collections. All is needed is to pass a single argument <a href="https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html">Consumer</a>. This can be very convenient and clean compared to writing multiple for statements.

{% highlight java %}
List<String> names = new ArrayList<String>();
names.add("Mark");
names.add("Dan");
names.forEach(name -> System.out.println(name));
{% endhighlight %}

In addition to the forEach operator, some useful operators have been added to the
<a href="https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html">collection</a> interface and
<a href="https://docs.oracle.com/javase/8/docs/api/java/util/Map.html">map</a> interface.
A few that could be of interest include removeIf and removeAll in collection, and compute in the map interface.

### Optionals
An <a href="https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html">Optional</a> is described in the Java Docs as "A container object which may or may not contain a non-null value". They act as wrappers around objects where we are unsure of the presence of a value.
They can be useful for handling functions that may return null, a sample use-case could be when fetching data through a REST call or an IO operation. Optionals provide an alternative to traditional exception handling and null checks, hoping to alleviate some of the user error from handling NullPointerExceptions. 

An Optional can be defined by <b>Optional.of()</b> or <b>Optional.ofNullable()</b>. Using the <b>ifPresent</b> function we can execute a function that is conditional on the presence of a value or if we suspect there is none then the <b>orElseGet/orElse</b> functions can return a default value. <b>ifPresent</b> will take a <a href="https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html">Consumer</a> as an argument and if the Optional has a non-null value then the Consumer's code block will be executed. Conversely, <b>orElseGet</b> similarly takes a <a href="https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html">Supplier</a> as an argument and will assign a value to the Optional only when the original value in the Optional is empty.

Yes, you still have to remember to use an Optional instead of an if-null check and I would admit this is one of the
less useful features in my view. Although there is still some power that comes from using Optionals in a similar fashion to streams and
by providing some guarantees that null values will be handled appropriately.

Here is one example of using Optionals to do some logging after a failed service call and return 'Call failed' where null
was returned from the fetching call. As you can see this chaining of functions can be quite succinct and expressive compared to if/else statements:

{% highlight java %}

private abstract String fetchData(String id);

private String handleFailedServiceCall() {
    System.out.println("Failed to call service. No results returned.");
    return "Call failed.";
}

public String getCustomerDetails(String id) {
     return Optional.ofNullable(fetchData(id))
        .orElseGet(this::handleFailedServiceCall);
}
{% endhighlight %}

As mentioned, there are actually two functions for returning a default value to an Optional <b>orElse()</b> and <b>orElseGet()</b>. There are a few differences to be aware of. The first is that orElse takes a value as an argument whereas orElseGet takes a <a href="https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html">Supplier</a> as an argument. The second difference is that orElseGet() will run lazily, so as you would expect this will only run when there is not a value present on the Optional. orElse()
will run regardless each time upfront on each call whether there is a value or not. This means when calling <b>getCustomerDetails</b> in the above example,
the function <b>handleEmptyServiceCall</b> will always be invoked if an orElse() is used, but by using orElseGet() <b>handleFailedServiceCall</b>
will only get invoked when the original object is not present. I cannot see a legitimate reason for preferring to use orElse instead of orElseGet given the potential impact on performance unless the default object has already been created up front.

Similar to streams we can use filter/map/flatMap and a vast array of other functions on Optionals. Here is a code
example trying to fetch customer details by name and return "Empty Customer" where the customer id has no valid prefix
or where the customer is null.

{% highlight java %}
private abstract Customer fetchData(String customerName);

private String handleNullCustomer() {
    return "Empty Customer.";
}

private boolean hasCustomerPrefix(String name) {
    return name.startsWith("CustID_");
}

public String getCustomerDetails(String name) {
    Optional<Customer> customerDetails = Optional.ofNullable(fetchData(name));

     return customerDetails
        .flat(Customer::getID)
        .filter(customerName -> this.hasCustomerPrefix(customerName))
        .orElseGet(this::handleNullCustomer);
}
{% endhighlight %}

### LocalDate/LocalTime
Java 8 brought <a href="https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html">LocalDate</a> and <a href="https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html">LocalTime</a> as a substitute for java.util.Date.
This was due to the old Date/Time classes being inherently problematic due to lack of multi-threading capabilities and some
unintuitive conventions. Previously in Java handling timezone differences was more difficult, this has improved with some new
additions to the language such as <a href="https://docs.oracle.com/javase/8/docs/api/java/time/OffsetDateTime.html">OffsetDateTime</a> and <a href="https://docs.oracle.com/javase/8/docs/api/java/time/ZonedDateTime.html">ZonedDateTime</a>. Java 8 also introduced <a href="https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html">Instant</a> and <a href="https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html">Duration</a> which allows developers to
capture time and durations easily with nanosecond accuracy.

<a href="https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html">LocalDate</a> represents Date, without time or time-zone information in ISO-8601 format:
{% highlight java %}
LocalDate local = LocalDate.now();
LocalDate localExplicit = LocalDate.parse("2019-12-31");
{% endhighlight %}
<a href="https://docs.oracle.com/javase/8/docs/api/java/time/LocalDate.html">LocalTime</a> represents Time without Date or time-zone information in ISO-8601 format:
{% highlight java %}
LocalTime local = LocalTime.now();
LocalTime localExplicit = LocalTime.parse("12:00");
{% endhighlight %}
<a href="https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html">LocalDateTime</a> represents both Date and Time a combination of the prior and still without time-zone information:
{% highlight java %}
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
LocalDateTime localExplicit = LocalDateTime.parse("2019-12-31 12:00", formatter);
{% endhighlight %}
<a href="https://docs.oracle.com/javase/8/docs/api/java/time/OffsetTime.html">OffsetTime</a>, <a href="https://docs.oracle.com/javase/8/docs/api/java/time/OffsetDateTime.html">OffsetDateTime</a> and <a href="https://docs.oracle.com/javase/8/docs/api/java/time/ZonedDateTime.html">ZonedDateTime</a> represent Dates and Times with zone information:
{% highlight java %}
// OffsetDateTime stores time as an offset of +1:00 hours from UTC/GMT
OffsetDateTime utcPlusOneHour = OffsetDateTime.now(ZoneOffset.of("+01:00"));
// ZonedDateTime stores time with time zone information. This is the current time in Los Angeles
ZoneId zoneId = ZoneId.of("America/Los_Angeles");
ZonedDateTime timeInLA = ZonedDateTime.now(zoneId);
{% endhighlight %}

Dates and times can now easily be compared with <b>isBefore</b> and <b>isAfter</b> functions on LocalDateTime, LocalTime, LocalDate
and ZonedDateTime. Making time and date comparisons much easier:
{% highlight java %}
LocalDate currDate = LocalDate.now();
boolean haveIPassedYearEnd = currDate.isAfter(LocalDate.parse("2019-12-31"));
boolean amIStillIn2019 = currDate.isAfter(LocalDate.parse("2018-12-31")) && currDate.isBefore(LocalDate.parse("2020-01-01"));
{% endhighlight %}

<a href="https://docs.oracle.com/javase/8/docs/api/java/time/Instant.html">Instant</a> represents a timestamp in Java 8
and Instant.now() will find the current system UTC timestamp.
Classes like <a href="https://docs.oracle.com/javase/8/docs/api/java/time/Duration.html">Duration</a> can be used to
add or subtract Instants as required.

As ever there are varying levels of detail that can be delved into here. Look through the classes mentioned and the API
to uncover details where you come across a need.