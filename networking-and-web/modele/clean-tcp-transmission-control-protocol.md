# Clean TCP - Transmission Control Protocol

## Generalities

* Transmission is made reliable via the use of sequence numbers and

    acknowledgments. 

  * each byte of data is assigned a     sequence number.  The sequence number of the first octet of data in a     segment is transmitted with that segment and is called the segment

      sequence number. 

  * Segments also carry an acknowledgment number which

      is the sequence number of the next expected data byte of

      transmissions in the reverse direction.

  * When the TCP transmits a     segment containing data, it puts a copy on a retransmission queue and     starts a timer; when the acknowledgment for that data is received, the     segment is deleted from the queue.  
    * If the acknowledgment is not       received before the timer runs out, the segment is retransmitted.
  * An acknowledgment by TCP does not guarantee that the data has been

      delivered to the end user, but only that the receiving TCP has taken

      the responsibility to do so.

  * To govern the flow of data between TCPs, a flow control mechanism is

      employed.  The receiving TCP reports a "window" to the sending TCP.

    * This window specifies the number of octets, starting with the

        acknowledgment number, that the receiving TCP is currently prepared to

        receive.



## Connection

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
    * SND.UNA - The number of the first unacknowledged 
    * SND.NXT - send next
    * SND.WND - send window
    * SND.UP  - send urgent pointer
    * SND.WL1 - Segment sequence number used for last window update
    * SND.WL2 - Segment acknowledgment number used for last window       update
    * ISS : Initial send sequence number 
  * Receive Sequence Variables



          RCV.NXT - receive next

          RCV.WND - receive window

          RCV.UP  - receive urgent pointer

          IRS     - initial receive sequence number

  * Current Segment Variables

          SEG.SEQ - segment sequence number

          SEG.ACK - segment acknowledgment number

          SEG.LEN - segment length

          SEG.WND - segment window

          SEG.UP  - segment urgent pointer

          SEG.PRC - segment precedence value

{% hint style="info" %}
I don't know if i want to detail it like that or not
{% endhint %}

* Each connection is uniquely specified by a pair of sockets   identifying its two sides.
* To communicate, system must first established a connection
  * And they need to close them to free the resources used for it when it's no longer useful



* An OPEN call is used to create a connection, it can be either : 
  * Active : Actively sending a request to a system to start a connection
    * If two system use active OPEN calls for each other at the same time, the connection will still be correctly established
  * Passive : Waiting \(listening\) for an incoming request
    * We can specify from whom we await a connection, or accepts any
* The process of establishing the connection in itself is called the **Three-Way Handshake**

\*\*\*\*

* The SEND call is used to send data
  * Since the connection is a constant stream of data, they're not given to the application after each new segment arrived
  * Instead, it is placed into a buffer
  * The receiving end can decide when to give the data to the application waiting for it
  * Except if the PUSH flag is set, telling the receiving end to immediately push \(give\) the data to the application



* There's also a flag that says that urgent data is coming down the stream. What the receiving end is supposed to do with this information is not specified, but it should try to prioritize this data



## Headers

![](../../.gitbook/assets/tcp-headers.png)

* Source Port : 16 bits
* Destination Port : 16 bits
* Sequence Number :  32 bits
  * The sequence number of the first data octet in this segment \(except

        when SYN is present\). If SYN is present the sequence number is the

        initial sequence number \(ISN\) and the first data octet is ISN+1.
* Acknowledgment Number:  32 bits
  * If the ACK control bit is set this field contains the value of the

        next sequence number the sender of the segment is expecting to

        receive.  Once a connection is established this is always sent.
* Data Offset:  4 bits
  * The number of 32 bit words in the TCP Header.  This indicates where

        the data begins.  The TCP header \(even one including options\) is an

        integral number of 32 bits long.
* Reserved:  6 bits,   reserved for future use, must be zero
* Control Bits:  6 bits \(from left to right\):
  * Bit 0 - URG :  Urgent Pointer field significant
  * Bit 1 - ACK :  Acknowledgment field significant
  * Bit 2 - PSH :  Push Function
  * Bit 3 - RST :  Reset the connection
  * Bit 4 - SYN :  Synchronize sequence numbers
  * Bit 5 - FIN :  No more data from sender
* Window : 16 bits
  * The number of data octets beginning with the one indicated in the

        acknowledgment field which the sender of this segment is willing to

        accept.
* Checksum : 16 bits
  * It's value is the 16-bits 1's complement of the 1's     complement sum of all 16-bits words in the header & text
  * During computation of the checksum :
    * It's value is 0
    * Padding that won't be sent may be added 
  * A 96-bits pseudo header is also virtually prefixed \(it won't be sent along\) to the real header
    * It contains the source address, destination address, protocol information and the TCP length \(without this pseudo headers\)
    * This protects against misrouted segments
* Urgent Pointer : 16 bits
  * This field communicates the current value of the urgent pointer as a

        positive offset from the sequence number in this segment.  The

        urgent pointer points to the sequence number of the octet following

        the urgent data.  This field is only be interpreted in segments with

        the URG control bit set.
* Options : Variable   & Optional
  * May or may not be present, but must be understood by all implementations
  * Their length is a multiple of 8 bits
  * There's either only the 1-byte option type or :
    * The 1-byte option type
    * Plus the 1-byte option length \(which takes the 2 bytes of type + length and the length of the data\)
    * Plus the option data
  * There's 3 possible options :
    * 0 : End of option list
      * Terminate the list in case it doesn't fall on the end of the header
    * 1 : No operation \(filler to align on a boundary for example\)
    * 2 : Maximum segment size
      * Only set with SYN segment
      * Optional, if it's not present any size is allowed
      * This option has a length of 4 \(bytes ?\)
* Padding : Variable & Optional



## States



* LISTEN : Represents waiting for a connection request
* SYN-SENT : Represents waiting for a matching connection request   after having sent a connection request
* SYN-RECEIVED : Represents waiting for a confirming connection request acknowledgment after having both received and sent a connection request
* ESTABLISHED : Represents an open connection, data received can be   delivered to the user
* FIN-WAIT-1 : Represents waiting for a connection termination request   from the remote or an acknowledgment of the connection   termination request previously sent.
* FIN-WAIT-2 : Represents waiting for a connection termination request   from the remote TCP.
* CLOSE-WAIT : Represents waiting for a connection termination request   from the local user
* CLOSING : Represents waiting for a connection termination request   acknowledgment from the remote TCP
* LAST-ACK : Represents waiting for an acknowledgment of the   connection termination request previously sent to the remote TCP   \(which includes an acknowledgment of its connection termination

      request\)

* TIME-WAIT : Represents waiting for enough time to pass to be sure   the remote TCP received the acknowledgment of its connection   termination request
* CLOSED : Represents no connection state at all



## Acknowledgment



* Every byte of data sent   has a sequence number, so each can be acknowledged
* The acknowledgment   mechanism is cumulative so that an acknowledgment of sequence

    number X indicates that all octets up to but not including X have been

    received

  * This mechanism allows for straight-forward duplicate

      detection in the presence of retransmission

  * The first data byte immediately following     the header is the lowest numbered, and the following byte are     numbered consecutively

* A segment on the retransmission queue is fully acknowledged if the sum   of its sequence number and length is less or equal than the   acknowledgment value in the incoming segment
*  Note that when the receive window is zero no segments should be

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

* The protocol places no restriction on a particular connection being

    used over and over again

  * A connection is defined by a pair of     sockets
  * New instances of a connection will be referred to as incarnations of the connection
  * The problem that arises from this is     "how does the TCP identify duplicate segments from previous incarnations of the connection?"

    * To avoid confusion we must prevent segments from one incarnation of a

        connection from being used while the same sequence numbers may still

        be present in the network from an earlier incarnation

    * We want to       assure this, even if a TCP crashes and loses all knowledge of the

      sequence numbers it has been using

    * When new connections are created,       an initial sequence number \(ISN\) generator is employed which selects a       new 32 bit ISN
    * The generator is bound to a \(possibly fictitious\) 32       bit clock whose low order bit is incremented roughly every 4       microseconds
    * Thus, the ISN cycles approximately every 4.55 hours
    * Since we assume that segments will stay in the network no more than       the Maximum Segment Lifetime \(MSL\) and that the MSL is less than 4.55       hours we can reasonably assume that ISN's will be unique.

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

* To be sure that a TCP does not create a segment that carries a   sequence number which may be duplicated by an old segment remaining in   the network, the TCP must keep quiet for a maximum segment lifetime   \(MSL\) before assigning any sequence numbers upon starting up or   recovering from a crash in which memory of sequence numbers in use was   lost.
  * The MSL is 2 min
  * Note that if a TCP is reinitialized in some     sense, yet retains its memory of sequence numbers in use, then it need     not wait at all; it must only be sure to use sequence numbers larger

      than those recently used
* large range of sequence numbers
* 


