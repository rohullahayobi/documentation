# Inserting into Elasticsearch

**Authoriship**

|Version|Date|Modified by|Summary of changes|
|-------|----|-----------|------------------|
|  0.1  | 2017-07-24 | Paul | initial version |
|  0.2  | 2017-07-30 | Andres | Proofread |


This section covers our consumer component, which is responsible for taking data items from the queue and inserting them into the main database. We will discuss the requirements and the tasks it fulfills.


## Requirements

In our architectural design, data importers push their output to a queue, in our case *Apache Kafka*. From the queue, the data items must be inserted into Elasticsearch. Besides simply taking data from the queue there are additional validation requirements that this component fulfills.


## Validation

The output generated by data importers (JSON documents)  that are prepared and processed must be validated prior to insertion in the database. That task is logically linked to the data storage component and not the importing process itself since:

* the ETL framework should not validate its own outcome (as it or its creator are biased)
* the database should ensure it is not importing corrupted datapoints that will break queries, etc.

The logical place to do validation was then the component that first processes the data items after the importers have written them to the queue.


### Ability to scale and adjust the configuration according to Kafka's configuration

Kafka can be configured in many ways. This mainly is about distribution (how many brokers), partitioning and the topics (channels) it uses. The consumer should work and be reconfigurable, if our Kafka configuration chances

### Allow parallelism for reading from Kafka

Depending on the configuration there can be several options, how parallel reading from Kafka can be accomplished (consumer groups, many topics and consumers, etc.). The consumer should be able to work for all of them or at least the ones we decide for.

### Achieve high performance to not slow down the importing pipeline

As our importers reached very high speeds in producing data items (> 1 million in few seconds) and many of them can be run in parallel the consumer itself shell also deliver a good performance. While of course overall we want to tackle every possible bottleneck, for the consumer itself it was at first most important that it is not the bottleneck itself.

### Allow concurency for processing data read from Kafka

A naive strategy of serially reading from the queue, processing the data and the pushing it to Elasticsearch will most likely be way to slow. Therefore we must go for a more sophisticated method, that uses asynchronisity and concurrency to manage a non-blocking higher performant process.


## Implementation of the Requirements
Because of the named reasons we decided to build one component to validate the output and push it &mdash;if valid&mdash; to Elasticsearch. There would have been the possibility to do this in separate steps with another queue in between as well. This would have meant a lot higher configuration effort and more resources required so that it seemed good to join both tasks in one component.

### Bulk insertion

The most important decision was on how to handle the insertion to Elasticsearch. These requests require an HTTP connection. If we open and close one HTTP connection per item inserted, the overhead would be enormous. Therefore we wanted to use Elasticsearch's bulk insertion feature. The HTTP requests should as well be non-blocking and be processed in the background to not halt the execution of the other important tasks, like listening to the queue and validation the data.

### Achieve high performance and allow validating

In order to fulfill the requirements we went with *Go* as a programming language. The main reasons for that was the outstanding performance and the build in concurrency features, which directly tackled the main requirements. Parallelism, configuration and scalability was achieved independent from the programming language and the consumer itself by deploying it the right way. This will be described in the paragraphs after this about Go-specific advantages.

Although parallelism can be achieved by simply spawning more consumers (see later) it was still very important to maintain a very fast performance within one importer. While reading from a queue, validating and inserting seemed like a typically task for a scripting language, the performance shortfalls of interpreted languages are too high to use one for this time-sensitive task. Needless to say we also discovered time issues with some test-consumers written e.g. in *ruby*.

For validating the JSON items there is the need for a library that handles validation of the used *JSON Schema* format. There is at least one library present for the most relevant programming languages, so this did not limit us in chosing a programming language.

### Allow concurrency for processing data read from Kafka
This was another main reason to chose *Go* as a programming language as concurrency is very well supported by the built-in Go routines. With this we could achieve a procedure that works like this:

* Continuously listen to the Kafka queue for new item on the topic.
* Directly validating them with a preloaded schema that is already in memory and therefore takes nearly no time
* Asynchronously passing the validated JSON to another Go routine, that aggregates the items to bulks until
	* The bulk limit is reached or
	* A timeout is triggered (from outside the routine, needed if importing is very slow, or especially for the last items released by the importer)
* the aggregated JSON is again asynchronously sent to another Go routine, that handles the HTTP connection to Elasticsearch in the background.


### Allow parallelism, scaling, and configuration

Scaling and configuring the consumer would have been possible in any language: scaling is a job solved by the deployment on a Kubernetes cluster and configuring it can be done with environment variables and the same deployment component setting them accordingly. Therefore the parallelism required was also not a requirement directly to the consumer itself.

There are two possibilities to allow parallelized reading from Kafka for a data source:

1. Using Kafka's Consumer Groups to allow several consumers reading from one topic
2. Using a topic per importer instance and not per data source (e.g. when several importers run for several days for a data source.)

Both of them have been tested by us and both of them have their advantages. As Elasticsearch was the limiting component in an end-to-end importing pipeline by not being able to insert > 5000 records/second with our configuration, we have not been able to do a proper benchmark on how consumer groups behave performance-wise and if it can reach the same performance than using a consumer per importing instance.

We could not achieve to run an even bigger configuration of Elasticsearch. The consumer therefore supplied a speed that reaches Elasticsearches limits as soon as two of them are run in parallel, which can be evaluated as a success and reduces the need of a detailed benchmark, which options would be more effective.

The consumer is able to join a consumer group, which can be configured by environment variables and therefore by the deployment component. If a data source needs several consumers for one importer (e.g. having lots of data for an importing interval &mdash;which is typically a day&mdash; that is produced faster than a consumer can utilize) several of them can be spawned inside one consumer group for that topic. If one importing interval can be processed by one importer but there are several of them run in parallel we can create a topic per importing instance and run a consumer for each. So the consumer in its current state is capable for both options.
