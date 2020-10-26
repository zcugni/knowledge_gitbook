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
* **Connected** protocol since it consider the data sent as a stream
* TCP implementations follow a general principle of robustness:  be   conservative in what you do, be liberal in what you accept from   others

## Connection

* The data stream between 2 entities is represented by a connection
  * They're uniquely identified by the pair of sockets on each side
* There's no restriction on a particular connection being   used multiple times
  * New instances of a connection are called incarnations of the connection

### Sequence & acknowledgement numbers

* Each byte of data is assigned a   **sequence** number
  * The sequence number of a segment is the one of it's first byte of data
* When a connection is created, an 32-bits **Initial Sequence Number** \(ISN\) is chosen from a 32-bits clock incremented every ~4 microseconds
  * As such, the ISN will cycle after 4.55 hours
  * Since the Maximum Segment Lifetime \(MSL\) is 2 minutes, so the ISN should be unique 
* The **acknowledgment** number is used to confirm the reception of data, it's value is the next sequence number that the receiver will use
  * An acknowledgment of X means that all bytes up to but not including X have been received
  * This also allows duplicate detection
  * An acknowledgment does not guarantee that the data has been     delivered to the end user, but only that the receiving TCP has taken     the responsibility to do so
* When a   segment is sent, a copy of it is put on a **retransmission queue** with a timer
  * When acknowledgment for that data is received, the     segment is deleted from the queue  
  * If the acknowledgment is not     received before the timer runs out, the segment is retransmitted
* Since a connection can be used multiple times, we mustn't use sequence number that may be in segments still in transit
  * Generally, the previously used number is stored so it's not a problem
  * But after recovering from a **crash** without knowledge of the previously used number, the system must keep quiet for the MSL duration





* For each connection there is a send sequence number and a receive

    sequence number

  * The initial send sequence number \(ISS\) is chosen by

      the data sending TCP, and the initial receive sequence number \(IRS\) is

      learned during the connection establishing procedure.

  * For a connection to be established or initialized, the two TCPs must

      synchronize on each other's initial sequence numbers

  * This is done in     an exchange of connection establishing segments carrying a control bit

      called "SYN" \(for synchronize\) and the initial sequence number

  * The synchronization requires each side to send it's own initial     sequence number and to receive a confirmation of it in acknowledgment     from the other side.  Each side must also receive the other side's     initial sequence number and send a confirming acknowledgment

### TCB

* Disclaimer : I didn't understand if the RFC said to implement it exactly like that, of if it was an example
* Several information are kept about a connection, they're put in a **Transmission Control** **Block** \(TCB\)
* These are some of the info kept :
  * Local and remote socket numbers
  * Security & precedence of the connection
  * Pointers to :
    * The user's send & receive buffers
    * The retransmit queue
    * The current segment
  * Variables concerning the sent sequence number :
    * SND.UNA - "send unacknowledged      "
    * Next sequence number
    * Window for segment sent
    * Urgent pointer
    * SND.WL1 - Segment sequence number used for last window update
    * SND.WL2 - Segment acknowledgment number used for last window       update
    * ISS : Initial Send Sequence number 
  * Receive Sequence Variables
    * Next acknowledgment number
    * Window for segment received
    * Received urgent pointer
    * IRS     -  Initial Receive Sequence number
  * Current Segment Variables
    * SEG.SEQ - Segment sequence number
    * SEG.ACK - Segment acknowledgment number
    * SEG.LEN - Segment length
    * SEG.WND - Segment window
    * SEG.UP  - Segment urgent pointer
    * SEG.PRC - Segment precedence value

### Three-way handshake

* To communicate, systems must first established a connection via the **Three-Way Handshake**
  * They likewise need to close them afterwards to free the resources used to keep its information

## Misc

* The receiving TCP specify a window that is the number of bytes starting with the   acknowledgment number, that it can receive
* Note that when the receive window is zero no segments should be

    acceptable except for ACK

  *  Thus, it's  possible to     maintain a zero receive window while transmitting data and receiving

      ACKs

    * However, even when the receive window is zero, a TCP must

        process the RST and URG fields of all incoming segments

* For sequence number   purposes, the SYN is considered to occur before the first actual data

    byte of the segment in which it occurs

  * While the FIN is considered to occur after the last actual byte octet in a segment in which it

      occurs

  * The segment length \(SEG.LEN\) includes both data and sequence     space occupying controls
  * When a SYN is present then SEG.SEQ is the     sequence number of the SYN.

* There's also a flag that says that urgent data is coming down the stream. What the receiving end is supposed to do with this information is not specified, but it should try to prioritize this dat

## Calls

* An OPEN call is used to create a connection, it can be either : 
  * Active : Actively sending a request to a system to start a connection
    * If two system use active OPEN calls for each other at the same time, the connection will still be correctly established
  * Passive : Waiting \(listening\) for an incoming request
    * We can specify from whom we await a connection, or accepts any
* The process of establishing the connection in itself is called the **Three-Way Handshake**
* The SEND call is used to send data
  * Since the connection is a constant stream of data, they're not given to the application after each new segment arrived
  * Instead, it is placed into a buffer
  * The receiving end can decide when to give the data to the application waiting for it
  * Except if the PUSH flag is set, telling the receiving end to immediately push \(give\) the data to the application

