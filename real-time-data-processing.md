Real-time data processing
-------------------------

Real-time data processing is hot.  Its a capability to consume the `events` and do some processing (pricing, Risk and Fraud, Alert, Trivial aggregations etc.) in soft real-time constraints.

One may say real-time is noting but micro-batch processing (e.g processing last 5 min events) and this implementation works(pull).  Another implementation is as soon as an event is produced, there is a infrastructure to deliver run a subscriber (push) and produce the result.

This is also known as stream processing or complex event processing (CEP) in Enterprise and Web companies have come up with these Big-Data familty systems for similar purpose

1. Apache Storm (Developed by Backtype and acquired by Twitter) built for sentiment analysis of Tweets
2. Apache Samza (Developed by LinkedIn)
3. Apache Spark Streaming

Unlike batch-processing which is throughput-oriented, real-time processing systems are latency-oriented.  They have strict-timeouts and fault-tolerance with no accuracy guarantees.  Events may be dropped, sequence-altered, double counted etc. but in practice its taken care with batch Ids and compensations built-in.
