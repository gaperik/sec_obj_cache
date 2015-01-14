No Working Group

Göran AP Eriksson
Stefan Håkansson

January 14th, 2015

#Content delivery from secure origins and caching of secure objects

**Note: Half-baked brainstorming coming up- this is far from ready.**


Status of document

This document gathers thoughts on requirements from a service provider perspective on delivering protected objects to a web application assumed to be implemented in a browser using the Internet and using caches, either controlled by the service provider or outsourced to a 3rd party, with whom the service provider is assumed to have an agreement.


##Terminology

Client   			
An end user device with a W3C compliant web runtime and a web application supplied by a service provider.

Authenticated client
https://tools.ietf.org/html/draft-thomson-tls-care-00 https://tools.ietf.org/html/draft-thomson-httpbis-cant-01 https://tools.ietf.org/html/draft-thomson-httpbis-catch-00

Origin 				
Representation of the server side functionality of a service provider servers, one of which contains the web page and associated content to be delivered to the user device.

Cache proxy  		
An intermediate application level server or set of servers providing functionality for caching content from the origin.

Channel security
Transport level secure channels between hosts, e.g. using TLS.

Application level security		
Security mechanisms applied by web application to secure application data. JOSE is an example.

Secure origin   	
A secure origin is an origin to which there is a secure communication of data to and from the client. It is secure from an authentication, integrity and confidentiality perspective. The level of security varies depending on mechanisms applied including encryption algorithm, name resolution mechanisms, etc. Refer to https://w3c.github.io/webappsec/specs/mixedcontent/#terms

Trusted origin		
A trusted origin is an origin associated to which there is a service provider with whom a user has a trust relationship concerning how the service provider treats the users data or data derived by the service provider concerning about the user. The trust relationship includes the users data- or knowledge obtained by the SP having access to the data- is protected and not redistributed without the consent of the user and/or in accordance with regulations applicable to the users and/or service providers legal home.



##Introduction

Delivering content to a user using the Web platform and the Internet poses security challenges to the service provider and the user. Various MITMs may intercept the communication, interfering with or learning about the service, the service provider or the user, neither desirable effects.

Additionally, in the light of revelations of massive, pervasive monitoring and, in parts of the world, increased attention to privacy concerns as well as regulations for handling customers and employees personal data, various forms of means to protect data transfer from an origin to a client are applied and new technologies developed. The concept of 'secure origin' has been brought up and are worked on in applicable mailling lists such as webappsec, webcrypto, TAG, etc..

At the same time, in some situations intermediate servers between user device and service provider web server may improve the service delivery, either the end user experience (e.g. give better responsiveness thanks to lower delay), or lowering production costs, or both.

To further our understanding, this document attempts to explore the requirements on the solution from a service provider perspective as well as drafting potential solutions to the problems that are identified covering client, origin and caching proxies. These solutions should describe solutions using existing and emerging browser mechanisms and protocols but in case needed, identify needs for enhancements bordering on being beyond ongoing community discussions. This applies to both client and server side of the solution.

In doing so, the scenarios where the service provider is operating the caching proxies himself or outsourcing it from a 3rd party- sometimes referred to as a CDN- are discussed. The intent to have both cases covered are that both are valid and to minimize the difference between an service provider controlled caching infrastructure and one sourced from an external party, facilitating smooth changes in the content delivery set-up.

In this context, both passive and active caching are considered, active caching meaning pushing content to a cache including device caches before the content having been requested by a user or rather the web application. Passive caching is the case where content having been delivered to user A is cached and later used when same content is requested by user B (or user A again).


##Problem statement

###General 

Consider a web service service provider with a client, an origin server and intermediate, reverse proxies (forward proxies not considered a desirable option). The content ingestion is assumed to happen in the back-end servers of the origin server site. The content delivery process stretches from the origin server via intermediate caches proxies to the web client or directly origin- client or a combination thereof. 

The service provider (SP from now on) have the following set of considerations:

1. Control of the user experience, to the extent the device and transport capabilities allow for it, meaning that the actual experience of the user should match the intended experience as is/was assumed in the composition of the Web application.

2. Parts of the experience may come from content to which Digital Rights are associated, rights that need to be protected.

3. Fulfilling the commitments to the user as stipulated in the privacy policy.

4. Compliance to local regulations (on security and privacy). 

5. Delivering on the promise of a UI 'secure origin'.

6. Efficient use of transport and device resources.

7. Production cost minimized...(certificates, storage, computing, networking, etc.).


###Problem example

Do we need examples?

###Motivations for caching

Do we need to motivate caching for fixed and mobile 'last mile'?


##Drafting a solution

###From the perspective of the SP

Assume the SP wants to take considerations 1 to 7 into account, in particular 3- providing a 'trusted origin' feeling (yeah, deliberately picked a subjective term to trigger a discussion).

One approach is to use HTTPS connection end-to-end between the client and the origin. That one however may incur considerable delays or high transport costs over mobile broadband (and in some Internet situations). A typical solution today is that the SP is using a cache proxy closer to the client.  But using HTTPS requires the SP to provide the certificate to the proxy where the HTTPS will be terminated. In case the proxy is deployed remotely from the origin server, further actions are required, such as having the proxy setting up a secure transport towards the origin servers, for instance a new HTTPS connection.

This may however not be enough in cases where the cache is either provided by a 3rd party or deployed in a computing context where the control of the cache by the SP is not sufficient, e.g. the certificate can be obtained by the local IAAS provider, potentially creating a risk for the content delivery to be compromised.

For these reasons- and others- the SP may also need to apply application level security on the content passing the proxy using an end-to-end security association making the risk vector for the data passing or being cached in the proxy smaller.

The above means the SP has a set of tools available for delivering content from its origin for an individual session:

* E2E HTTPS connection(s).
* Multi-layer caching enabled secure transfer- a combination of application level encryption of data and hop-by-hop HTTPS connections between client-proxy-origin.

And combinations of the above.

To further clarify the need of application level security, consider the case where HTTPS only is used and a 3rd party cache proxy shall be used; This scenario requires the certificate of the HTTPS connection between the client and origin domain to be given to the cache proxy provider.  But at the same time, the implied expectation in 'secure origin' and possibly a lock icon in the UI is that the is a secure connection between client and origin, an expectation that is not reflected in the actual situation. One could argue that this indirectly violates the expectations associated with 'TLS', similar to if TLS protocol would be designed to allow intermediate, transparent MITM's.

### Application level security per content type and different types of web app data

Placeholder for going through web app data and various content types and how to secure these.

##Example use case

Placeholder as input to first discussions:

1. User initiates web app.
2. Web app connects to origin using HTTPS bootstrap connection(s). 
3. Origin authenticates Client.
4. Client and Origin server negotiates use of intermediate cache proxies for hop-by-hop connections. (Content can also be delivered e2e from origin server to client but in this example, all content is assumed to be delivered via a caching infrastructure.)
5. Hop-by-hop connections to intermedia caching proxies established. This includes download to client via bootstrap HTTPS connection of keys and other info needed to encryping content (pieces) on application level, etc.
6. From then on, in selected cases, content delivered using push or pull approach.


To be continued....


##Acknowledgements

Never in the history of the Web platform development have so few had so many to thank for so little.

##About the authors

GE
SH
...
...


