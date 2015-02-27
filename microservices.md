Microservices is a lean way of implementing age old Service-Oriented-Architecture (SOA).

SOA is initially pushed by big vendors (IBM, Oracle, SUN, Microsoft) and as such has ulterior motive to sell more software/tooling around the concepts.  WebServices (WSDL, SOAP, WS*) and really never cared for developer-productivity. (Yeah there is eclipse tooling to generate wrappers and code, but its hardly any productive).

Unlike Enterprises, web companies doesn't have to buy all this BS and just straight went to adopt the SOA principles.  Just standardize on a serialization format (Avro, Thrift) that allows changes and migrations and go deliver the features as services in your language of choice.  

Every micro-service doesn't have to  be "enterprise-ready" meaning secured, interfaces defined, governance-ready, audit-enabled etc. and just implement the functionality and run it in a fire-wall protected environment.  It doesn't need an authentication of its own, when all it does is provide service to a service aggregator like front-end application.

