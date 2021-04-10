# HTTP - Hypertext Transfer Protocol

## Old

* Used to fetch resources, such as HTML documents
  * A document is reconstructed from the different element fetched \(text, css, media, scripts, etc\)
* Essential for any data exchange on the Web
* Characteristic :
  * Client-Server : the communication is initiated by the client \(usually a browser\)
    * They communicate via messages, called **request \(**from the client\) & **response** \(from the server\)
  * [Stateless](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols) \(The usage of cookies let us use it as a stateful protocol\)
  * Human readable \(before HTTP/2\)
  * Used over TCP \(however in reality it only needs its transport protocol to be reliable, not necessarily connection-oriented\)
    * Experiments are made to develop a transport protocol better suited for HTTP
  * Extensible
* There's 3 versions of this protocol \(HTTP/1.0, HTTP/1.1 & HTTP/2\)
  * The main difference between HTTP/1.0 & HTTP/1.1 is that the new version can use a connection for more than one request/response pair
  *  HTTP messages, as defined in HTTP/1.1 and earlier, are human-readable. In HTTP/2, these messages are embedded into a binary structure, a _frame_, allowing optimizations like compression of headers and multiplexing. Even if only part of the original HTTP message is sent in this version of HTTP, the semantics of each message is unchanged and the client reconstitutes \(virtually\) the original HTTP/1.1 request. It is therefore useful to comprehend HTTP/2 messages in the HTTP/1.1 format.

### Request

Format :

```text
<VERB | NOUN> <path> <version>
[Optional Headers ...]

[Optionnal body]
```

Example :

```text
GET /api/stats/overview HTTP/1.1
Host: www.hackthebox.eu
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://www.hackthebox.eu/
Cookie:  hackthebox_session=eyJpdiI6IkJBTlJ0bzNRVGowUDVrOUZNejl2K3c9PSIsInZhbHVlIjoiNlNUOUlUTlkwXC9GWEhpWXhpQzhmSUtHczFvWDlLYTYyMk5Tc0RNTDBFcm8rdjA2YmJwdDF5blwvSkl5OFB6emxVIiwibWFjIjoiMTY1NDNjMDI0NzhkOTI4ZDVlNzE2Y2MwOTYwZGE5NzZhOTIxNzQ3NTQ3ODk3YjNkNmJmMjA2ZWZhMjdmZjQ5ZCJ9
```

* HTTP Verbs & Nouns :
  * `GET` Ask for a resource by adding `key=value` parameters after an `?` in a link
  * `POST` Submit form data \(they're in the message body\)
  * `HEAD` Ask for the the headers that would be given if the request was a GET
  * `PUT` Upload a file to the server
  * `DELETE` Remove a file from the server
  * `OPTIONS` Query the server for accepted Verbs
  * `TRACE`
* Path
* Protocol Version
* `Host` Domain of the requested ressource
  * The behavior of the serveur might change depending on that field even if it points to the same ressource \(for example, using the ip address instead of the hostname\)
* `User-Agent` Client software issuing the request
* `Accept` Expected format of the response
* `Accept-Language` Expected \(human\) language of the response
* `Accept-Encoding` Expected encoding \(aka compression method\)
* `Connection` What to do with it, with `keep-alive` it will be re-use next time \(i think\)
* `Referer` Last page visited

### Response

```text
HTTP/1.1 200 OK
Date: Mon, 23 Mar 2020 18:35:27 GMT
Content-Type: application/json
Connection: close
Vary: Accept-Encoding
Cache-Control: no-cache, private
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Strict-Transport-Security: max-age=0; includeSubDomains
Server: cloudflare
CF-RAY: 578a4096ab09c795-AMS
Content-Length: 48

{"users":"286k","machines":154,"challenges":111}
```

* Protocol version
* Status
  * 1xx : Information
  * 2xx : Successful
    * 200 Ok
  * 3xx : Redirection
    * 301 Moved Permanently
    * 302 Found \(temporarily moved\)
  * 4xx : Client Error
    * 400 Bad Request
    * 401 Unauthorized
    * 403 Forbidden
    * 404 Not Found
    * 405 Method Not Allowed
    * 408 Request Time-Out
  * 5xx : Server Error
    * 500 Internal Server Error
    * 501 Method Not Implemented
* `Date`
* `Cache-Control` Cache policy
* `Content-type` Describe the format
* `Content-Encoding` Describe the encoding \(the compression method\)
* `Server` \(Optional\) Header of the server generating the response
* `Content-Length` Length of the message body in bytes

### HTTPS

* Stands for HTTP Secure, it's runs over SSL/TLS

## Draft

* HTTP communication usually takes place over TCP/IP connections. The default port is TCP 80 \[19\], but other ports can be used. This does not preclude HTTP from being implemented on top of any other protocol on the Internet, or on other networks. HTTP only presumes a reliable transport; any protocol that provides such guarantees can be used;
* Semantics
  * HTTP/1.1 defines the sequence CR LF as the end-of-line marker for all protocol elements except the entity-body \(see appendix 19.3 for tolerant applications\). The end-of-line marker within an entity-body is defined by its associated media type, as described in section 3.7.
  * HTTP/1.1 header field values can be folded onto multiple lines if the continuation line begins with a space or horizontal tab. All linear white space, including folding, has the same semantics as SP. A recipient MAY replace any linear white space with a single SP before interpreting the field value or forwarding the message downstream.
* HTTP uses a "." numbering scheme to indicate versions of the protocol
  * HTTP-Version   = "HTTP" "/" 1\*DIGIT "." 1\*DIGIT
* As far as HTTP is concerned, Uniform Resource Identifiers are simply formatted strings which identify--via name, location, or any other characteristic--a resource.
  * For definitive information on URL syntax and semantics, see "Uniform Resource Identifiers \(URI\): Generic Syntax and Semantics," RFC 2396 \[42\] \(which replaces RFCs 1738 \[4\] and RFC 1808 \[11\]\).
  * URIs in HTTP can be represented in absolute form or relative to some known base URI \[11\], depending upon the context of their use
    * The two forms are differentiated by the fact that absolute URIs always begin       with a scheme name followed by a colon.
  *  http\_URL = "http:" "//" host \[ ":" port \] \[ abs\_path \[ "?" query \]\]
    *  If the port is empty or not given, port 80 is assumed
  * Uri comparison
    * A port that is empty or not given is equivalent to the default      port for that URI-reference;
    * Comparisons of host names MUST be case-insensitive;
    * Comparisons of scheme names MUST be case-insensitive;
*  HTTP character sets are identified by case-insensitive tokens. The  complete set of tokens is defined by the IANA Character Set registry  \[19\].
  * Although HTTP allows an arbitrary token to be used as a charset value, any token that has a predefined value within the IANA Character Set registry \[19\] MUST represent the character set defined by that registry
  * Some HTTP/1.0 software has interpreted a Content-Type header without

       charset parameter incorrectly to mean "recipient should guess." \(ND : it's not the goal\)

  * Unfortunately, some older HTTP/1.0 clients did not deal properly with an explicit charset parameter. HTTP/1.1 recipients MUST respect the charset label provided by the sender; and those user agents that have a provision to "guess" a charset MUST use the charset from the

    content-type field if they support that charset, rather than the recipient's preference, when initially displaying a document

  * When no explicit charset

       parameter is provided by the sender, media subtypes of the "text"

       type are defined to have a default charset value of "ISO-8859-1" when

       received via HTTP
*  Content coding values indicate an encoding transformation that has

     been or can be applied to an entity. Content codings are primarily

     used to allow a document to be compressed

  *  The Internet Assigned Numbers Authority \(IANA\) acts as a registry for

       content-coding value tokens

* Transfer-coding values are used to indicate an encoding

     transformation that has been, can be, or may need to be applied to an

     entity-body in order to ensure "safe transport" through the network.

     This differs from a content coding in that the transfer-coding is a

     property of the message, not of the original entity.

  *   ```text


           transfer-coding         = "chunked" | transfer-extension
           transfer-extension      = token *( ";" parameter )

       Parameters are in  the form of attribute/value pairs.

           parameter               = attribute "=" value
           attribute               = token
           value                   = token | quoted-string
    ```

  * Whenever a transfer-coding is applied to a message-body, the set of

       transfer-codings MUST include "chunked", unless the message is

       terminated by closing the connection. When the "chunked" transfer-

       coding is used, it MUST be the last transfer-coding applied to the

       message-body.

  *  The Internet Assigned Numbers Authority \(IANA\) acts as a registry for

       transfer-coding value tokens.

  * A server which receives an entity-body with a transfer-coding it does

       not understand SHOULD return 501 \(Unimplemented\), and close the

       connection. A server MUST NOT send transfer-codings to an HTTP/1.0

       client.

  * 3.6.1 Chunked Transfer Coding \(if i want to develop\)

*   ```text
  media-type     = type "/" subtype *( ";" parameter )
         type           = token
         subtype        = token
  ```

  * Parameters MAY follow the type/subtype in the form of attribute/value

       pairs

  * Note that some older HTTP applications do not recognize media type

       parameters

  * Media-type values are registered with the Internet Assigned Number

       Authority \(IANA \[19\]\).

* When in canonical form, media subtypes of the "text" type use CRLF as

     the text line break. HTTP relaxes this requirement and allows the

     transport of text media with plain CR or LF alone representing a line

     break when it is done consistently for an entire entity-body. HTTP

     applications MUST accept CRLF, bare CR, and bare LF as being

     representative of a line break in text media received via HTTP. In

     addition, if the text is represented in a character set that does not

     use octets 13 and 10 for CR and LF respectively, as is the case for

     some multi-byte character sets, HTTP allows the use of whatever octet

     sequences are defined by that character set to represent the

     equivalent of CR and LF for line breaks. This flexibility regarding

     line breaks applies only to text media in the entity-body; a bare CR

     or LF MUST NOT be substituted for CRLF within any of the HTTP control

     structures \(such as header fields and multipart boundaries\).

* HTTP messages consist of requests from client to server and responses

     from server to client.

  * Both types of message consist of a start-line, zero

       or more header fields \(also known as "headers"\), an empty line \(i.e.,

       a line with nothing preceding the CRLF\) indicating the end of the

       header fields, and possibly a message-body.

  *  Each header field consists     of a name followed by a colon \(":"\) and the field value
    * Field names are case-insensitive.
    * Header fields can be

         extended over multiple lines by preceding each extra line with at

         least one SP or HT.
  * The order in which header fields with differing field names are

       received is not significant. However, it is "good practice" to send

       general-header fields first, followed by request-header or response-

       header fields, and ending with the entity-header fields.

  * It MUST be possible to combine the multiple header fields into one

       "field-name: field-value" pair, without changing the semantics of the

       message, by appending each subsequent field-value to the first, each

       separated by a comma. The order in which header fields with the same

       field-name are received is therefore significant to the

       interpretation of the combined field value, and thus a proxy MUST NOT

       change the order of these field values when a message is forwarded

* The message-body \(if any\) of an HTTP message is used to carry the

     entity-body associated with the request or response. The message-body

     differs from the entity-body only when a transfer-coding has been

     applied, as indicated by the Transfer-Encoding header field

  * Transfer-Encoding is a property of the message, not of the





       entity, and thus MAY be added or removed by any application along the

       request/response chain

  * The presence of a message-body in a request is signaled by the

       inclusion of a Content-Length or Transfer-Encoding header field in

       the request's message-headers

  *  For response messages, whether or not a message-body is included with

       a message is dependent on both the request method and the response

       status code 

    *  All responses to the HEAD request method

         MUST NOT include a message-body, even though the presence of entity-

         header fields might lead one to believe they do

    * All 1xx

         \(informational\), 204 \(no content\), and 304 \(not modified\) responses

         MUST NOT include a message-body. All other responses do include a

         message-body, although it MAY be of zero length.

* The transfer-length of a message is the length of the message-body as

     it appears in the message; that is, after any transfer-codings have

     been applied

  * The way in which the length is determined is describe, but it's really detailed

* general-header = Cache-Control            ; Section 14.9

                        \| Connection               ; Section 14.10

                        \| Date                     ; Section 14.18

                        \| Pragma                   ; Section 14.32

                        \| Trailer                  ; Section 14.40

                        \| Transfer-Encoding        ; Section 14.41

                        \| Upgrade                  ; Section 14.42

                        \| Via                      ; Section 14.45

                        \| Warning 

* The Request-Line begins with a method token, followed by the

     Request-URI and the protocol version, and ending with CRLF

* The Method  token indicates the method to be performed on the

     resource identified by the Request-URI. The method is case-sensitive

  * The methods GET

       and HEAD MUST be supported by all general-purpose servers. All other

       methods are OPTIONAL;

  * The list of methods allowed by a resource can be specified in an

       Allow header field

* The request-header fields allow the client to pass additional

     information about the request, and about the client itself, to the

     server

* request-header = Accept                   ; Section 14.1

                        \| Accept-Charset           ; Section 14.2

                        \| Accept-Encoding          ; Section 14.3

                        \| Accept-Language          ; Section 14.4

                        \| Authorization            ; Section 14.8

                        \| Expect                   ; Section 14.20

                        \| From                     ; Section 14.22

                        \| Host                     ; Section 14.23

                        \| If-Match                 ; Section 14.24













                        \| If-Modified-Since        ; Section 14.25

                        \| If-None-Match            ; Section 14.26

                        \| If-Range                 ; Section 14.27

                        \| If-Unmodified-Since      ; Section 14.28

                        \| Max-Forwards             ; Section 14.31

                        \| Proxy-Authorization      ; Section 14.34

                        \| Range                    ; Section 14.35

                        \| Referer                  ; Section 14.36

                        \| TE                       ; Section 14.39

                        \| User-Agent               ; Section 14.43

* After receiving and interpreting a request message, a server responds

     with an HTTP response message.

  * The first line of a Response message is the Status-Line, consisting

       of the protocol version followed by a numeric status code and its

       associated textual phrase, with each element separated by SP

       characters

*   ```text
     - 1xx: Informational - Request received, continuing process

        - 2xx: Success - The action was successfully received,
          understood, and accepted

        - 3xx: Redirection - Further action must be taken in order to
          complete the request

        - 4xx: Client Error - The request contains bad syntax or cannot
          be fulfilled

        - 5xx: Server Error - The server failed to fulfill an apparently
          valid request
  ```

*   ```text
  Status-Code    =
              "100"  ; Section 10.1.1: Continue
            | "101"  ; Section 10.1.2: Switching Protocols
            | "200"  ; Section 10.2.1: OK
            | "201"  ; Section 10.2.2: Created
            | "202"  ; Section 10.2.3: Accepted
            | "203"  ; Section 10.2.4: Non-Authoritative Information
            | "204"  ; Section 10.2.5: No Content
            | "205"  ; Section 10.2.6: Reset Content
            | "206"  ; Section 10.2.7: Partial Content
            | "300"  ; Section 10.3.1: Multiple Choices
            | "301"  ; Section 10.3.2: Moved Permanently
            | "302"  ; Section 10.3.3: Found
            | "303"  ; Section 10.3.4: See Other
            | "304"  ; Section 10.3.5: Not Modified
            | "305"  ; Section 10.3.6: Use Proxy
            | "307"  ; Section 10.3.8: Temporary Redirect
            | "400"  ; Section 10.4.1: Bad Request
            | "401"  ; Section 10.4.2: Unauthorized
            | "402"  ; Section 10.4.3: Payment Required
            | "403"  ; Section 10.4.4: Forbidden
            | "404"  ; Section 10.4.5: Not Found
            | "405"  ; Section 10.4.6: Method Not Allowed
            | "406"  ; Section 10.4.7: Not Acceptable
            | "407"  ; Section 10.4.8: Proxy Authentication Required
            | "408"  ; Section 10.4.9: Request Time-out
            | "409"  ; Section 10.4.10: Conflict
            | "410"  ; Section 10.4.11: Gone
            | "411"  ; Section 10.4.12: Length Required
            | "412"  ; Section 10.4.13: Precondition Failed
            | "413"  ; Section 10.4.14: Request Entity Too Large
            | "414"  ; Section 10.4.15: Request-URI Too Large
            | "415"  ; Section 10.4.16: Unsupported Media Type
            | "416"  ; Section 10.4.17: Requested range not satisfiable
            | "417"  ; Section 10.4.18: Expectation Failed
            | "500"  ; Section 10.5.1: Internal Server Error
            | "501"  ; Section 10.5.2: Not Implemented
            | "502"  ; Section 10.5.3: Bad Gateway
            | "503"  ; Section 10.5.4: Service Unavailable
            | "504"  ; Section 10.5.5: Gateway Time-out
            | "505"  ; Section 10.5.6: HTTP Version not supported
  ```

* HTTP status codes are extensible. HTTP applications are not required

     to understand the meaning of all registered status codes, though such

     understanding is obviously desirable. However, applications MUST

     understand the class of any status code, as indicated by the first

     digit, and treat any unrecognized response as being equivalent to the

     x00 status code of that class, with the exception that an

     unrecognized response MUST NOT be cached

*   ```text
  The response-header fields allow the server to pass additional
     information about the response which cannot be placed in the Status-
     Line. These header fields give information about the server and about
     further access to the resource identified by the Request-URI.

         response-header = Accept-Ranges           ; Section 14.5
                         | Age                     ; Section 14.6
                         | ETag                    ; Section 14.19
                         | Location                ; Section 14.30
                         | Proxy-Authenticate      ; Section 14.33
                         | Retry-After             ; Section 14.37
                         | Server                  ; Section 14.38
                         | Vary                    ; Section 14.44
                         | WWW-Authenticate        ; Section 14.47
  ```

*  Request and Response messages MAY transfer an entity if not otherwise

     restricted by the request method or response status code. An entity

     consists of entity-header fields and an entity-body, although some

     responses will only include the entity-headers

  * Entity-header fields define metainformation about the entity-body or,

       if no body is present, about the resource identified by the request.

       Some of this metainformation is OPTIONAL; some might be REQUIRED by

       portions of this specification.

  * entity-header  = Allow                    ; Section 14.7

                          \| Content-Encoding         ; Section 14.11

                          \| Content-Language         ; Section 14.12

                          \| Content-Length           ; Section 14.13

                          \| Content-Location         ; Section 14.14

                          \| Content-MD5              ; Section 14.15

                          \| Content-Range            ; Section 14.16

                          \| Content-Type             ; Section 14.17

                          \| Expires                  ; Section 14.21

                          \| Last-Modified            ; Section 14.29

                          \| extension-header

  * The extension-header mechanism allows additional entity-header fields

       to be defined without changing the protocol, but these fields cannot

       be assumed to be recognizable by the recipient. Unrecognized header

       fields SHOULD be ignored by the recipient and MUST be forwarded by

       transparent proxies.

  * An entity-body is only present in a message when a message-body is

       present, as described in section 4.3. The entity-body is obtained

       from the message-body by decoding any Transfer-Encoding that might

       have been applied to ensure safe and proper transfer of the message.

* Content-Type specifies the media type of the underlying data.

     Content-Encoding may be used to indicate any additional content

     codings applied to the data, usually for the purpose of data

     compression

  * Any HTTP/1.1 message containing an entity-body SHOULD include a

       Content-Type header field defining the media type of that body. If

       and only if the media type is not given by a Content-Type field, the

       recipient MAY attempt to guess the media type via inspection of its

       content and/or the name extension\(s\) of the URI used to identify the

       resource. If the media type remains unknown, the recipient SHOULD

       treat it as type "application/octet-stream".

*   The entity-length of a message is the length of the message-body

     before any transfer-codings have been applied

* Persistent HTTP connections have a number of advantages:

  * By opening and closing fewer TCP connections, CPU time is saved

            in routers and hosts \(clients, servers, proxies, gateways,

            tunnels, or caches\), and memory used for TCP protocol control

            blocks can be saved in hosts.



  *  HTTP requests and responses can be pipelined on a connection.

            Pipelining allows a client to make multiple requests without

            waiting for each response, allowing a single TCP connection to

            be used much more efficiently, with much lower elapsed time.

  *  Network congestion is reduced by reducing the number of packets

          caused by TCP opens, and by allowing TCP sufficient time to

          determine the congestion state of the network.



  * Latency on subsequent requests is reduced since there is no time

            spent in TCP's connection opening handshake.



  *  HTTP can evolve more gracefully, since errors can be reported

            without the penalty of closing the TCP connection. Clients using

            future versions of HTTP might optimistically try a new feature,

            but if communicating with an older server, retry with old

            semantics after an error is reported. 

  * HTTP implementations SHOULD implement persistent connections.

* A significant difference between HTTP/1.1 and earlier versions of

     HTTP is that persistent connections are the default behavior of any

     HTTP connection. That is, unless otherwise indicated, the client

     SHOULD assume that the server will maintain a persistent connection,

     even after error responses from the server.

  * Persistent connections provide a mechanism by which a client and a

       server can signal the close of a TCP connection. This signaling takes

       place using the Connection header field \(section 14.10\). Once a close

       has been signaled, the client MUST NOT send any more requests on that

       connection.

  * An HTTP/1.1 server MAY assume that a HTTP/1.1 client intends to

       maintain a persistent connection unless a Connection header including

       the connection-token "close" was sent in the request.

  * An HTTP/1.1 client MAY expect a connection to remain open, but would

       decide to keep it open based on whether the response from a server

       contains a Connection header with the connection-token close. In case

       the client does not want to maintain a connection for more than that

       request, it SHOULD send a Connection header including the

       connection-token close.

  * If either the client or the server sends the close token in the

       Connection header, that request becomes the last one for the

       connection.

  * Clients and servers SHOULD NOT assume that a persistent connection is

       maintained for HTTP versions less than 1.1 unless it is explicitly

       signaled

  * 
       A client that supports persistent connections MAY "pipeline" its

       requests \(i.e., send multiple requests without waiting for each

       response\). A server MUST send its responses to those requests in the

       same order that the requests were received.

* This means that clients, servers, and proxies MUST be able to recover

     from asynchronous close events.

* The purpose of the 100 \(Continue\) status \(see section 10.1.1\) is to

     allow a client that is sending a request message with a request body

     to determine if the origin server is willing to accept the request

     \(based on the request headers\) before the client sends the request

     body.

  * Requirements for HTTP/1.1 clients:



          - If a client will wait for a 100 \(Continue\) response before

            sending the request body, it MUST send an Expect request-header

            field \(section 14.20\) with the "100-continue" expectation.



          - A client MUST NOT send an Expect request-header field \(section

            14.20\) with the "100-continue" expectation if it does not intend

            to send a request body.



## From book

* The `Content-Type` header indicate the media type of the content
  * The media-type determines how a browser will render the content
  * Browser can ignore this header and instead use MIME sniffing
    * Application can block this behavior with the `X-Content-Type-Options: nosniff`
* When a 3xx response is received, the browser should make a new HTTP request to the URL defined in the `Location` header
* The CONNECT method is reserved for use with a proxy
* OPTIONS doesn't say if HEAD & TRACE request are permitted
* HTTP request are stateless
* A site uses basic authorization if there's an HTTP header like this : `Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW11`
* Check `samesite`Cookie
* You can identify a site that uses basic authorization when the request includes an http header of the form `Authorization: BasicQwxhZGRpb...`

## Sources

* RFC
* _Real-World Bug Hunting: A Field Guide to Web Hacking_ by [Peter Yaworski](https://www.google.com/search?safe=strict&sxsrf=ALeKk02u5fJoFUD8va7qx4wcDgWmD8cPVg:1616713092379&q=peter+yaworski&stick=H4sIAAAAAAAAAOPgE-LVT9c3NEyrqixPMyhKUoJyDaqMjTLKk7RkspOt9JPy87P1y4syS0pS8-LL84uyrRJLSzLyixax8hWklqQWKVQmAkWLszN3sDICAA97YYBSAAAA&sa=X&ved=2ahUKEwj7oszMxczvAhXGyYUKHRtMBLkQmxMoATAdegQIFhAD)

