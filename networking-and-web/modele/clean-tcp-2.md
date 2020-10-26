# Clean TCP 2

## Generalities

* More complex and reliable than UDP but slower
* It's base functionalities are :
  * **Data Transfer** : Transfer a continuous stream of bytes in each     direction between its users
  * **Reliability** : Recovery from damaged, lost, duplicated, or delivered out of order data
  * **Flow Control**     :  Provides a means for the receiver to govern the amount of data     sent
  * **Multiplexing** : Allow for many processes within a single host to use TCP     simultaneously
  * **Connections** : Since data is transferred as a stream and different information needs to be kept for the other functionalities to work, TCP uses the concepts of a connection
  * **Precedence & Security** : Security and precedence can be set
* TCP speaks on one hand to applications, and on the over to protocols of the underlying layer
  * A minimum set of call is expected for the TCP/Application interface :
    * OPEN
    * CLOSE
    * SEND
    * RECEIVE
    * STATUS
  * There's no other expectation for the TCP/Protocols interface, except that **asynchronous** dialogue must be possible
    * TCP can work with a wide variety of protocols, and i'll explain some general concepts, but this summary is primarily focused on TCP over IP
* TCP implementations follow a general principle of robustness:  be   conservative in what you do, be liberal in what you accept from   others
* **Connected** protocol since it consider the data sent as a stream

## Sequence number

* Each byte of data is assigned a   sequence number
* The sequence number of a segment is the one of it's first byte of data
* The acknowledgment number is the next sequence number of the receiver



* When a   segment is sent, a copy of it is put on a **retransmission queue** with a timer
  * When the acknowledgment for that data is received, the     segment is deleted from the queue  
  * If the acknowledgment is not     received before the timer runs out, the segment is retransmitted
* An acknowledgment by TCP does not guarantee that the data has been   delivered to the end user, but only that the receiving TCP has taken   the responsibility to do so





* The receiving TCP specify a window that is the number of bytes starting with the   acknowledgment number, that it can receive
* 
