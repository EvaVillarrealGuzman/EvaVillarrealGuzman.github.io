---
title: Web application concepts
layout: post
subtitle: In this post we will see basic concepts about web systems. We will see how a browser and a server interact, what is an API and a web services and how a client server architecture works.
categories: architecture
tags: [architecture, client-server, web, api rest, soap, web service]
---


The development of the World Wide Web has had a profound effect on all of our lives. Initially, the Web was primarily a universally accessible information store and it had little effect on software systems. These systems ran on local computers and were only accessible from within an organization. Around 2000, the Web started to evolve and more and more functionality was added to browsers. This meant that web-based systems could be developed where, instead of a special-purpose user interface, these systems could be accessed using a web browser. This led to the development of a vast range of new system products that delivered innovative services, accessed over the Web. These are often funded by adverts that are displayed on the user’s screen and do not involve direct payment from users.

As well as these system products, the development of web browsers that could run small programs and do some local processing led to an evolution in business and
organizational software. Instead of writing software and deploying it on users’ PCs, the software was deployed on a web server. This made it much cheaper to change and upgrade the software, as there was no need to install the software on every PC. It also reduced costs, as user interface development is particularly expensive. Consequently, wherever it has been possible to do so, many businesses have moved to web-based interaction with company software systems.

The next stage in the development of web-based systems was the notion of web services. Web services are software components that deliver specific, useful functionality and which are accessed over the Web. Applications are constructed by integrating these web services, which may be provided by different companies. In principle, this linking can be dynamic so that an application may use different web services each time that it is executed.

In the last few years, the notion of *software as a service* has been developed. It has been proposed that software will not normally run on local computers but will run on *computing clouds* that are accessed over the Internet. If you use a service such as web-based mail, you are using a cloud-based system. A computing cloud is a huge number of linked computer systems that is shared by many users. Users do not buy software but pay according to how much the software is used or are given free access in return for watching adverts that are displayed on their screen. The advent of the web, therefore, has led to a significant change in the way that business software is organized. Before the web, business applications were mostly monolithic, single programs running on single computers or computer clusters. Communications were local, within an organization. Now, software is highly distributed, sometimes across the world. Business applications are not programmed from scratch but involve extensive reuse of components and programs.

## Browser and server interaction flow

When a browser requests for a web page, the following set of events takes place:

1- A user enters the URL of the website or file in the browser. The browser then requests the DNS(Domain Name System) server  
2- The DNS server looks up the IP address of the web server and once it finds it, sends it back to the browser  
3- The browser then sends over an HTTP/HTTPS request to the web server’s IP (provided by the DNS server in the previous step)  
4- The server responds with the necessary information or data  
5- The browser renders the data and the website is displayed  

## URL's elements

An URL (*Uniform Resource Locator*) is a unique web address. Represents the location of a specific resource on the Internet. The term URL refers to a subsets of URIs (*Uniform Resource Indicators*) that, in addition to identifying the web resource, also provide the means to locate it.

Every web page are *online* resources and every web page have a URL. But not all URLs point to a web page, but there are other resources on the Internet such as files, images, services, web applications, etc. Each web page is made up of a number of resources such as HTML, hyperlinks to other pages, CSS, javascript, images, files, etc. Each of them have their own URL.

Depending on the URL, it can contain the following elements:

![](https://zvelo.com/wp-content/uploads/2018/08/full-path-url-structure.jpg)

- **Top Level Domain**: TLD it is one of the domains at the highest level of the Domain Name System hierarchy. It is the last tagged part of a fully qualified domain name. Administration and responsibility for TLDs are delegated to organizations by The Internet Corporation for Assigned Names and Numbers ([ICANN](https://www.icann.org/)) and The Internet Assigned Numbers Authority ([IANA](https://www.iana.org)), which maintains the [root zone of the DNS](https://www.iana.org/domains/root/files). Some examples are .com, .net, .org, .edu, .ar, .us and [more](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains).The TLD is always present.
- **Domain name**: A domain name is the registered identification “string” (or word/phrase) used by the [Domain Name System](https://www.verisign.com/en_US/website-presence/online/domain-name-system/index.xhtml)  to define a specific area of control and autonomy (also known as the location of a website). A domain name is one level below the TLD. A single TLD can contain hundreds of thousands or even millions of individual "second level" domains. The domain can also be called a second level domain. Some examples are google.com, apple.com, amazon.com. The domain name is always present.
- **Subdomain**: Subdomains express relative dependency and represent part of a top-level domain. In this way, subdomains are commonly used to divide domains into smaller segments for communication purposes, content type, internationalization, or for other reasons. The subdomain is always present, although not always visible. Some of the most common examples of subdomains (in bold) are:
  - **www**.example.com
  - **blog**.example.com
  - **support**.example.com
  - **mail**.example.com
- **Protocol**: The protocol in a URL determines how data is transferred between the *host* and a web browser (or client). HTTP and HTTPS are two of the most common protocols that you will find in most URLs, although there are other Internet protocols such as FTP, DNS, DHCP, IMAP, SMTP, etc. The communication protocol appears before any subdomain in a URL. The protocol is followed by a colon and two forward slashes that separate it from the *hostname*. For example: **https**://google.com, where HTTPS is the protocol.
- **Path**: The path refers to the exact location of a page, post, file or other asset. It is often analogous to the underlying file structure of the website. The path resides after the *hostname* and is separated by "/" (forward slash). The path also consists of any file extensions, such as images (.jpg or .png, etc.), documents (.pdf or .docx), and more. But not all URLs will show a path. For example, when you visit a site's home page, you may not see a path or file name. This is because many modern websites may rewrite URLs (such as the home page) for simplicity and elegance, such as omitting the typical index.html. The path is always present, although not always visible.
- **HTML anchors**: HTML anchors are used on websites to implement *bookmarks* and internal page navigation elements. They can be used to provide links to specific locations within a page. Anchors will be placed immediately after the file/path of a URL (when present).
- **Parameters**: The parameters are found at the end of the URL or within the path, depending on the implementation. URL parameters are represented in key/value pairs, beginning with a "?" And separated by an "&". They can also be set dynamically in the path as values separated by slashes and other characters (depending on which system is used and how it is implemented). Parameters are commonly used for tracking and analytics, as well as encoding specific information for use on websites and apps. For example, a URL with parameters:
  - https://www.example.com/solutions?user=123&color=blue
  - https://www.example.com/solutions/user/123/color/blue

> NOTE: TLD, domain name and subdomain make up the *hostname*

> NOTE: A URL may exclude or omit the sending domain and the *hostname* may not include the subdomain. This is known as a *naked domain*. For example, our website https://google.com does not show a subdomain.

## About API and Web Services

### API

API stands for **Application Programming Interface**.

It is a software interface that allows two distinct services/applications to interact without one having to know the implementation details of the other. It is what we call, protocol agnostic.


### Web service

A web service is a medium that lets two machines communicate with each other over a network.

In other words, it allows communication between the client and the server applications using the XML, JSON, and other plain text open standards over the World Wide Web (WWW).

#### Types

**SOAP web services** – SOAP which stands for **Simple Object Access Protocol**, is a specification that enables applications to communicate with other applications. It uses an XML-based protocol to access web services over HTTP.

**RESTful web services** – REST which stands for **Representational State Transfer**, is a service developed on the REST architecture and is often called a RESTful service. They are lightweight, maintainable, and scalable in nature. The underlying protocol for REST is HTTP.

### API vs web services

| API           | Web Services     |
|--------------|-----------|
| Not all APIs are web services| All web services are APIs     |
| APIs can use any style for communication      | A web service can use SOAP, REST, and XML-RPC for communication  |
| APIs can also be used to invoke functions within a software, so it does not always need a network for its operation| A web service will always need a network for its operation     |

## Introduction to HTTP

HTTP (Hypertext Transfer Protocol) is a client-server protocol that enables a client and server to communicate over a network using request/response. It’s an application layer protocol that relies on TCP/IP for its services.

HTTPS is an extension of the Hypertext Transfer Protocol. It is used for secure communication over a computer network. In HTTPS, the communication protocol is encrypted using a TLS-encrypted TCP connection.

### The architecture of HTTP

**Client:** the client sends a request to the server via the HTTP protocol

**Server:** here, the server processes the request and sends the response for the requested information to the client

### The basic aspects of HTTP

- HTTP is **connectionless**: each client request is treated as a unique and independent connection.
- HTTP is **stateless but not sessionless**. By stateless we mean that, HTTP does not keep any information about the client. Therefore, there is no relation between any requests that are being carried out consecutively on the same connection.
- HTTP is **extensible/customized**. It can be integrated with any new functionality just by setting up or providing an agreement between a client and a server.

### HTTP Requests and HTTP Response

**HTTP requests** are the messages sent by the client to initiate an action on a resource identified by a given URL over the network. There are various HTTP request methods for specific purposes.

The components of an HTTP request are as below:

- The method to be applied to the resource – an HTTP method (like GET, PUT, POST HEAD or OPTIONS), that describes the action to be performed. We have already discussed the various HTTP methods in the previous lesson.  
- The resource identifier – resource identified by a given request URL.  
- Headers (optional) – headers are the information sent to the server in the form of key/value pairs, which contains the details of what the browser wants and will accept from the server.  
- Params (optional) – used for sending additional data to the server. For example, a query parameter.  
- Message body (optional) – These are additional information required by the server to process current requests properly. For example, a file type of JSON or XML sent in the case of a POST or PUT method request.  

After receiving and processing an HTTP request message, a server responds with an **HTTP response** message.

### Methods

#### GET Method

The GET method is used by the client to fetch the data from the server.

#### POST Method

The HTTP POST method sends data to the server to create a new resource.

#### PUT Method

The HTTP PUT method will update an existing resource or create a new resource on the server, depending on the request payload.

PUT is idempotent.: This means that an outcome will remain the same even if it’s called several times successively. For instance, calling the POST several times with the same request data may create a new resource each time, therefore, it is not idempotent.

#### DELETE Method

The HTTP DELETE method deletes the specified resource on the server.

DELETE is idempotent, which means that an outcome will remain the same even if it’s called several times successively with the same request data.

### HTTP Status Codes

In a server’s HTTP response, status codes indicate whether a specific HTTP request has been successfully completed or an error has occurred.

A HTTP status code can indicate the client or server error as well.

The HTTP response status codes are divided into 5 categories as shown in the table below:

![](https://evavillarrealguzman.github.io/img/client-server-arquitecture/status-code.png)

### HTTP Headers

The client and the server can pass some additional information using HTTP headers through a request or a response.

An HTTP header is basically a key-value, where its key is a case-insensitive string and value is a single or array of string.

#### Types of HTTP headers

These are grouped into different categories below, based on their context:

- **General headers:** this is used for both request and response and there is no relation to the message data.  
- **Request headers:** these are client headers, sent during the HTTP request and they contain information about the resource to be fetched.  
- **Response headers:** these are server headers and are part of the HTTP response. They give more details about the server resource context.  
- **Entity headers:** these headers give meta-information about the content of the body of the message like MIME type, Content-length, etc.  

### HTTP Parameters

When a client uses the HTTP GET method to request a certain resource from the web server, the client may also send certain parameters through the requested URL in the form of a query parameter, path parameter, or a form parameter.

#### Query parameter

The query parameter is a name-value pair and is the part of a requested GET URL. Have a look at the example below:

For the GET call, the client sends a query parameter in the form of a name-value pair ( page=2 ) and requests the web server for all the users on the ‘2’ listing page.

```
curl -X GET https://reqres.in/api/users?page=2
```

#### Path parameter

The path parameter is part of a requested GET URL path. Have a look at the example below:

For this GET call, the client sends a path parameter and requests the web server for details of the user whose id is ‘2’.

```
curl -X GET https://reqres.in/api/users/2
```

#### Form parameter

In an HTTP POST request, a certain piece of information is sent as a form parameter in the form of a name-value pair.

The client sends the form parameters in the requested body in the format that the Content-Type header field specifies. For example:

- Content-Type: application/x-www-form-urlencoded  
- Content-Type: multipart/form-data – if you use file uploads  
- But the content type could be anything, ranging from text/plain, application/json, etc  

## Web service


### Introduction to REST API

REST, or Representational State Transfer, is an architectural design that defines a set of rules for creating web services that interact between systems. Web services that follow this architecture are termed, ‘RESTful web services’.

At a very high level, the RESTful system consists of two major components:

- A server that hosts the resources
- A client that connects to the server to fetch the resources

REST uses HTTP (Hypertext Transfer Protocol) or HTTPS (Hypertext Transfer Protocol Secure) to exchange data between client and server using HTTP methods – GET, POST, UPDATE, DELETE, HEAD, PATCH, etc.

REST is more flexible architecture and loose guidelines than SOAP. As a result, it’s lightweight and better performing.

#### Benefits of using REST

By following the principles of REST, we can benefit from the loose coupling between the server and the client, reliability, scalability, and better performance of the application. Few of the benefits of REST are listed below:

- **Simplicity** – REST web services are easy and simple to develop compared to SOAP web services  
- **Light-weight** – REST advocates simple communication with the server over HTTP that supports plain XML, JSON formats in comparison to SOAP which supports only XML  
- **Architecture is similar to Web** --the architecture of REST is very similar to how the web is designed. So, developers who understand the web can easily understand and develop RESTful web services  
- **Scalability** – the REST architecture advocates refraining from the conversational state that allows us to easily add multiple instances of the components or application behind load balancers  

#### Guiding principles of REST

A RESTful service needs to comply with the following 6 guiding constraints:

**Client–Server** – the main principle behind this is the separation of concerns, i.e, separating the user interface concern from the data storage concerns. By following this principle, we can improve the:  
    - portability of the user interface across platforms  
    - scalability by simplifying the server component   
**Stateless** – each request from the client must be sent to the server with all the necessary information to understand the request and the request cannot take advantage of any stored context on the server. The session state is therefore maintained entirely on the client.  
**Cacheable** – cache constraints require that the data within a response to a request be implicitly or explicitly denoted as cacheable or non-cacheable to prevent clients from giving stale information. If a response is cacheable, then a client cache is given the right to reuse that response data for later, equivalent requests. This allows us to minimize the network calls made to the server.  
**Uniform Interface** – this is the fundamental principle of the RESTful system. It simplifies and decouples the architecture of the system, allows us to independently scale the components, and improve the interactions between components and other systems. The four architectural constraints that RESTful system should follow in order to a uniform interface are:  
    - resource identification  
    - manipulation of resources through representations  
    - self-descriptive messages  
    - hypermedia as the engine of application state (HATEOS)  
**Layered System** – the layered system style allows an architecture to be divided into a number of hierarchical layers or tiers by constraining each of the layer’s behavior such that each layer cannot access beyond the immediate layer with which they are interacting with. This allows us to independently scale the layers.  
**Code on-demand** – this principle is an optional one. REST allows client functionality to be extended by downloading executable scripts that can be executed on client-side like Java applets or Javascript.

### Introduction to SOAP

SOAP (Simple Object Access Protocol) is an XML-based protocol for accessing web services over HTTP and few other protocols. It has some specifications which can be used across all applications and platforms.

SOAP has been a really popular messaging protocol for a long time. It comes with strict rules which make features such as the ACID (Atomicity, Consistency, Isolation, Durability) properties and authorization possible. However, SOAP generally requires a lot of bandwidth and resources, which results in slower operations.

#### Advantages

- SOAP communicates over HTTP, HTTPS, TCP, SMTP, FTP, XMPP, and more, unlike REST which communicates over HTTP and HTTPS only.  
- SOAP can have its own implementation of security SOAP WS-Security that allows it to encrypt messages and have authentication data in the message header.  
- SOAP is ACID-compliant (Atomicity, Consistency, Isolation, and Durability).  

#### SOAP Messages

The SOAP message is an XML document which has the following components:

![](https://evavillarrealguzman.github.io/img/client-server-arquitecture/soap-message.png)

- An **Envelope element** that encapsulates the SOAP header and SOAP body, which form the SOAP message. This is the root of the SOAP message. It needs to have at least one SOAP body.
- A **SOAP Header** element that contains metadata about the message such as credentials that can be used for authentication. It consists of two attributes:
  - Actor – defines the list of services or nodes that should receive the SOAP message in the chain the message propagation defined by SOAP
  - MustUnderstand – labels that tell whether the header is mandatory or optional. When this attribute is set, the service node that receives the SOAP message will process and validate its semantics

A SOAP Header can also have complex types that can be used in the SOAP body as mentioned like so:

```xml
<?xml version="1.0"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:soap="http://educative.io/soap-automation">
   <soapenv:Header>
       <soap:requestId soapenv:mustUnderstand="true">dc7e2763-6495</soap:requestId>
   </soapenv:Header>
   <soapenv:Body>
       ..............
    </soapenv:Body>
</soapenv:Envelope> 
```

- A **SOAP Body** contains the information about the request and response. The complexType described in the SOAP Header will be used here.

```xml
<?xml version="1.0"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:soap="http://educative.io/soap-automation">
   <soapenv:Header>
       <soap:requestId soapenv:mustUnderstand="true">dc7e2763-6495</soap:requestId>
   </soapenv:Header>
   <soapenv:Body>
       <soap:getStudentsRequest>
           <soap:firstName>John</soap:firstName>
           <soap:lastName>Doe</soap:lastName>
           <soap:gender>Male</soap:gender>
       </soap:getStudentsRequest>
    </soapenv:Body>
</soapenv:Envelope>
```

- Fault (optional) – holds data about any errors that could be thrown by the web service. This is part of SOAP body.

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
   <soapenv:Header/>
   <soapenv:Body>
      <soapenv:Fault>
         <faultcode>soapenv:Server</faultcode>
         <faultstring xml:lang="en">student with id '103' not found</faultstring>
      </soapenv:Fault>
   </soapenv:Body>
</soapenv:Envelope>
```

#### Web Services Description Language (WSDL)

WSDL is an XML document that describes a web service and its components. It consists of the location of the web service, the methods contained in the web service, and its data types. All these information are described using the following elements:

- **types** – defines data types used by the web service  
- **message** – defines data elements for each operation  
- **portType** – operations that can be performed and the messages involved  
- **binding** – defines the protocol and data format for each port type  
- **service** – defines where the web service is hosted  

A sample WSDL XML document is given below:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<wsdl:definitions xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" xmlns:sch="http://educative.io/soap-automation" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:tns="http://educative.io/soap-automation" targetNamespace="http://educative.io/soap-automation">
    <wsdl:types>
        <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified" targetNamespace="http://educative.io/soap-automation">
            <xs:element name="getStudentsRequest">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="id" nillable="true" type="xs:int" />
                        <xs:element name="firstName" nillable="true" type="xs:string" />
                        <xs:element name="lastName" nillable="true" type="xs:string" />
                        <xs:element name="gender" nillable="true" type="xs:string" />
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
            <xs:element name="getStudentsResponse">
                <xs:complexType>
                    <xs:sequence maxOccurs="unbounded">
                        <xs:element name="students" type="tns:student" />
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
            <xs:complexType name="student">
                <xs:sequence>
                    <xs:element name="id" type="xs:int" />
                    <xs:element name="firstName" type="xs:string" />
                    <xs:element name="lastName" type="xs:string" />
                    <xs:element name="gender" type="xs:string" />
                </xs:sequence>
            </xs:complexType>
        </xs:schema>
    </wsdl:types>
    <wsdl:message name="getStudentsResponse">
        <wsdl:part element="tns:getStudentsResponse" name="getStudentsResponse">
        </wsdl:part>
    </wsdl:message>
    <wsdl:message name="getStudentsRequest">
        <wsdl:part element="tns:getStudentsRequest" name="getStudentsRequest">
        </wsdl:part>
    </wsdl:message>
    <wsdl:portType name="StudentsPort">
        <wsdl:operation name="getStudents">
            <wsdl:input message="tns:getStudentsRequest" name="getStudentsRequest">
            </wsdl:input>
            <wsdl:output message="tns:getStudentsResponse" name="getStudentsResponse">
            </wsdl:output>
        </wsdl:operation>
    </wsdl:portType>
    <wsdl:binding name="StudentsPortSoap11" type="tns:StudentsPort">
        <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http" />
        <wsdl:operation name="getStudents">
            <soap:operation soapAction="" />
            <wsdl:input name="getStudentsRequest">
                <soap:body use="literal" />
            </wsdl:input>
            <wsdl:output name="getStudentsResponse">
                <soap:body use="literal" />
            </wsdl:output>
        </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="StudentsPortService">
        <wsdl:port binding="tns:StudentsPortSoap11" name="StudentsPortSoap11">
            <soap:address location="http://ezifyautomationlabs.com:6566/educative-soap/ws" />
        </wsdl:port>
    </wsdl:service>
</wsdl:definitions>
```

#### SOAP vs REST

|            | SOAP           | REST     |
|--------------|--------------|-----------|
| Approach           |Data available as services: getValue | Data available as resources: “value”    |
| Performance           |Requires more bandwidth and compute power | Uses fewer resources    |
| Message Format           |Only XML | XML, HTML, JSON etc.    |
| Transfer Protocols           |SMTP, HTTP, UDP, etc. | HTTP only    |


# Sources

- [https://zvelo.com/anatomy-of-full-path-url-hostname-protocol-path-more/](https://zvelo.com/anatomy-of-full-path-url-hostname-protocol-path-more/)
- [Base Domain URL vs. Full Path URL. What’s the Difference?](https://zvelo.com/base-domain-url-vs-full-path-url-whats-the-difference/)
- [REST API vs. SOAP](https://www.educative.io/edpresso/rest-api-vs-soap)
- [Learn REST and SOAP API Test Automation in Java](https://www.educative.io/courses/learn-rest-soap-api-test-automation-java)
- Software Engineering, ninth edition - Ian Sommerville