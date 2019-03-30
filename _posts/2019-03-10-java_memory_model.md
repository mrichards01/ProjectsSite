---
layout: post
title: The Java Memory Model
categories: Java
---

Often times, the Java Memory Model is a talking point with technical interviews. Knowing how Garbage Collection works within Java is important to understanding the impact of your code, improving the efficiency of your applications with scale and choosing the right garbage collector for the job. Here are some key points and considerations related to the JVM and Garbage Collection.

## Stack vs Heap Memory

Every <b>value type</b> in the JVM is stored on the thread's stack. For each method called, a new block in memory is allocated for primitive values within that scope and then de-allocated immediately after each call. Compared to Heap memory, the stack is for short lived memory. <b>Reference types</b> on the other hand are stored on the Java Heap for longer use and then de-allocated through Garbage Collection. When the collector detects an object is no longer referenced the corresponding block in memory is freed to be re-used. A possible area of confusion here is that for any allocated object, the reference itself is actually stored on the stack whereas the actual object it is pointing is stored on the heap.

## JVM Heap

We are often concerned with the JVM's Heap memory as running the garbage collector is an overhead. Running the garbage collector frequently and for long periods of time is going to have an impact on overall performance, as this is a 'Stop the World' operation. In other words, the running execution of all of your running threads will be paused until Garbage Collection has finished. For this reason there are a few strategies to deal with garbage collections more efficiently.

## Garbage Collection Strategies

Each GC strategy at the JVM 's disposal have their own advantages/disadvantages. A classic but fundamental strategy that you should know of is the Mark and Sweep method. This particular algorithm or strategy will keep track of all of the running GC roots in the program. When the Garbage collector runs it will traverse these roots and 'mark' any objects that are still in use. The 'sweeping' part of the algorithm then takes place and objects not marked are left to be re-allocated for future use. After this there is a final step to compact the memory to ensure memory remains de-fragmented and for contiguous blocks to be re-used. Some other GC strategies include the following:
<ul>
<li>Reference Counting - Store a count of the number of references to each object, when the count is zero then free the object from memory. This can be difficult to determine for circular dependencies. </li>
<li>Copying - Similar to the Mark and Sweep method but the sweep stage will move across objects in use to a different partition and then compact memory. </li>
<li>Generational - Similar to copying. Separate memory into sections a young and old. Keep new objects in the young generation before phasing them into an older generation dedicated to taking the burden of more intensive but less frequent GCs.</li>
</ul>

Java uses a combination of these GC methods but the concept of splitting memory into generations  is key.

## JVM Model

As seen below the JVM memory heap is split into different sections to serve different purposes. This does not represent the real ratio between the different generations and actually the size of both the young and old generation partitions seen can be configured in your JVM arguments. 

![placeholder]({{site.baseurl}}/images/java_memory_model/java_memory_model.png "JVM Memory Model")

## The Young Generation

At first all objects are created in the young generation in the Eden space within the JVM. When filled, a <b>Minor Garbage Collection</b> will take place and move objects to either one of the two survivor spaces. It will alternate between these two spaces for each minor GC and keep track of how many times an object has survived a garbage collection.

## The Old Generation

<b>Major GCs</b> are performed when the survivor space is full usually or if an object has survived a large number of GCs. This will move objects from the young generation into the old generation where as the name suggests, long lived objects reside. Running the garbage collector on the whole of the heap is inefficient process hence the design decision to separate parts of the JVM memory into generations. You may start to think of some different possibilities here, if an object in the old generation refers to another in the young generation for example there is a chance of collecting those during a minor GC unless the entire old generation is scanned. If we require scanning everything in this way, of course this would negate the design choice of having two different generations to begin with. To get around this the JVM handles this through a card table, storing the memory address ranges or buckets consisting of bits to mark where memory is dirty. When assigning a new object, the respective card entry in the table is marked. Later during a minor GC this is used to scan for through parts of the old generation rather than the whole generation.

## JVM Garbage Collector Settings

As mentioned, the JVM has a number of different collectors which a developer is free to choose depending on the best fit the purposes of their application. Before choosing any of these, you should thoroughly research your choice and any alternatives. If in doubt, always use the default:

<ul>
<li>
<b>Serial Garbage Collector</b>: The most basic of all types listed here. This collector freezes all threads when running and it is most suited for single threaded, 32 bit systems and most likely only a good choice for client machines.
</li>
<li>
<b>Parallel Garbage/Throughput Collector</b>: Uses multiple threads for scans and any compacting. This collector is an improvement from Serial but will still pause all threads. This option is much more customisable, we can choose the number of GC threads, maximum pause between GCs, maximum heap size and other throughput options. This is suitable for some applications with some level of optimisation but can still permit some small pauses within the app.
</li>
<li><b>CMS Collector (Concurrent Mark and Sweep)</b>: Like the Parallel Collector this uses multiple threads. This collector tries to limit the amount of time spent on stop the world operations but will run slower on average. Usually this collector requires both more cpu and heap memory in order to run steadily. This could be suitable for an always on server side environment where having no pauses are important to perceived performance.
</li>
<li><b>The G1 Collector (Garbage First)</b>: A more recent collector as of JDK 7 update 4, designed to replace the CMS collector and for machines with higher memory specifications. This collector will partition the heap into contiguous blocks in virtual memory. First a concurrent marking phase will take place, after which the collector will know which regions are mostly empty. These regions are collected first as they provide the best results to relinquishing memory and to fulfilling target pause times. As an intended improvement of the CMS Collector the use-case of this collector is similar.
</li>
</ul>

## Other Resources
<ul>
<li><a href="https://www.geeksforgeeks.org/mark-and-sweep-garbage-collection-algorithm/">GeeksForGeeks</a>
</li>
<li><a href="https://docs.oracle.com/javase/9/gctuning/introduction-garbage-collection-tuning.htm#JSGCT-GUID-326EB4CF-8C8C-4267-8355-21AB04F0D304">JavaDocs</a>
</li>
</ul>