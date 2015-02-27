Microservices is a lean way of implementing age old Service-Oriented-Architecture (SOA).

SOA is initially pushed by big vendors (IBM, Oracle, SUN, Microsoft) and as such has ulterior motive to sell more software/tooling around the concepts.  WebServices (WSDL, SOAP, WS*) and really never cared for developer-productivity. (Yeah there is eclipse tooling to generate wrappers and code, but its hardly any productive).

Unlike Enterprises, web companies doesn't have to buy all this BS and just straight went to adopt the SOA principles.  Just standardize on a serialization format (Avro, Thrift) that allows changes and migrations and go deliver the features as services in your language of choice.  

Every micro-service doesn't have to  be "enterprise-ready" meaning secured, interfaces defined, governance-ready, audit-enabled etc. and just implement the functionality and run it in a fire-wall protected environment.  It doesn't need an authentication of its own, when all it does is provide service to a service aggregator like front-end application.

Micro-Services in Enterprise
----------------------------
This is how micro services implementation will look in enterprise.

1. CRM becomes a `Customer Service`
2. HRIS becomes a `Employee Service`
3. OMS becomes a `Order Service`
4. VMS becomes a `Supplier Service`
5. MDM(Product Information Management) becomes a `Catalog Service`
6. All Encompassing `ERP` will become the aggregate service of all the above.

All the above systems will have their own database of choice (Eg. Catalog system may chose a Document Database, where as order management system may choose traditional RDBMS) and can only be used by the app and not shared with any other app.  All the communication happens through REST API/Messaging.  Every app publishes the state changes as `events` in logs and logs are aggreated by a centralized messaging service (Kafka) for various downstream consumers like Data warehouses, Analytics, Archival, Monitor and Alert systems etc. If the Apps are not built to be cooperative to publish the events, then a change-data-capture needs to be put in place for that data store.
