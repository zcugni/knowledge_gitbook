# TCP - Transmission Control Protocol

## Generalities

* Transport layer protocol
* More complex and reliable than UDP but slower
* It's base functionalities are :
  * **Data Transfer** : Transfer a continuous stream of bytes in each     direction between its users
  * **Reliability** : Recovery from damaged, lost, duplicated, or delivered out of order data
  * **Flow Control**     :  Provides a means for the receiver to govern the amount of data     sent
  * **Multiplexing** : Allow for many processes within a single host to use TCP     simultaneously
  * **Connections** : Since data is transferred as a stream and different information needs to be kept for the other functionalities to work, TCP uses the concepts of a connection
  * **Precedence & Security** : Security and precedence can be set
* \*\*\*\*[**Connection-oriented**](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols) ****& ****[**stateful**](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols)\*\*\*\*
* TCP implementations follow a general principle of robustness:  be   conservative in what you do, be liberal in what you accept from   others
* The header + data is called a **segment**
* Errors messages for different failures :
  * Segment rejected / blocked : "connection refused"
  * Segment ignored :  "connection timed out"

## Connection

* The data stream between 2 entities is represented by a connection uniquely identified by the pair of sockets on each side
* There's no restriction on a particular connection being   used multiple times
  * New instances of a connection are called incarnations of it

### Sequence numbers

* All bytes of a connection are assigned a **sequence** number that identifies them
  * With the acknowledgment mechanism, it makes it possible to check that everything arrived
  * **SYN** & **FIN** each consume a number
  * **ACK** does not
* The **Initial Sequence Number** \(ISN\) is chosen randomly from a 32-bits clock incremented every ~4 microseconds
  * As such, it''ll cycle after 4.55 hours
  * Since the Maximum Segment Lifetime \(MSL\) is 2 min \(by default\), the ISN should be unique 
* The seq number of a **segment** is the one of its first byte of data
  * Since SYN consume a number, the first byte of data of a connection has a seq number of ISN+1
* Since a connection can be used multiple times, we mustn't use seq numbers that may be in segments still in transit
  * Generally, the previously used number is stored so it's not a problem
  * But after recovering from a **crash** without previous knowledge of used number, the system must keep quiet for the MSL duration to assure that seq numbers will be unique

### Acknowledgement number

* The **acknowledgment** number is used to confirm the reception of data
  * An ack of X means that all bytes up to but not including X have been received
* It's value is the next **sequence** number that the receiver will use
* It is sent along **every** segment except for the initial SYN
* When a   segment is sent, a copy of it is put on a **retransmission queue** with a timer
  * When acknowledgment for that data is received, the     segment is deleted from the queue  
  * If the acknowledgment is not     received before the timer runs out, the segment is retransmitted
  * The timer is dynamically chosen

### Handshakes

* To establish a connection, each side must synchronize it's sequence number with the other
  * This is done through a process called the **Three-Way Handshake**
  * It still works if the two system simultaneously attempt it
  * The synchronization segment can already carry data, the receiving end will just put them into a buffer until the connection is established
  * The process goes like this :
    * A segment with the SYN flag on is sent, it's seq number is the ISN
    * A corresponding segment is received, with the SYN & ACK flags on
      * It's seq number is the IRS
      * It's ack number is our next seq number
    * We send as a response a segment with the ACK flag on, the number being the received sequence number + 1
      * Our sequence number is incremented like usual
    * The connection is established, all following segment will have a seq and ack number

```text
STATE                                                       STATE 

CLOSED                                                      LISTEN
SYN-SENT    --> <seq = 100> <SYN>                       --> SYN-RECEIVED
ESTABLISHED <-- <seq = 300> <ack = 101> <SYN> <ACK>     <-- SYN-RECEIVED
ESTABLISHED --> <seq = 101> <ack = 301> <ACK> <data>    --> ESTABLISHED
```

* Likewise, to close the connection, they use a **Four-Way Handshake**

```text
STATE                                                STATE
ESTABLISHED                                          ESTABLISHED
FIN-WAIT-1  --> <seq = 100> <ack = 300> <FIN> <ACK>  --> CLOSE-WAIT
FIN-WAIT-2  <-- <seq = 300> <ack = 101> <ACK>        <-- CLOSE-WAIT
...
TIME-WAIT   <-- <seq = 500> <ack = 101> <FIN> <ACK>  <-- LAST-ACK
TIME-WAIT   --> <seq = 101> <ack = 501> <ACK>        --> CLOSED
(2 MSL) CLOSED
```

### State

* `LISTEN` Waiting for a connection request
* `SYN-SENT` Waiting for a matching connection request   after having sent one
* `SYN-RECEIVED` Waiting for a connection request acknowledgment after having both received and sent a connection request
* `ESTABLISHED` An open connection, data received can be   delivered to the user
* `FIN-WAIT-1` Waiting for either : 
  * A connection termination request     from the remote
  * An acknowledgment of the connection     termination request previously sent
* `FIN-WAIT-2` Waiting for a connection termination request   from the remote
* `CLOSE-WAIT` Waiting for a connection termination request   from the local user
* `CLOSING` Waiting for a termination request   acknowledgment
* `LAST-ACK` Waiting for an acknowledgment of the   termination request previously sent
* `TIME-WAIT` Waiting for enough time to pass to be sure   the remote received the acknowledgment of its termination request
* `CLOSED` No connection state at all

### Window

* The receiving system specify a window that represent the number of bytes that it can receive, starting from the   ack number
* When the receive window is 0, no segments other that ACK are   acceptable
  * However, even when the receive window is zero, it must process incoming RST & URG fields
*  If more data   arrives than can be accepted, it will be discarded, resulting  in excessive retransmissions

### Urgent pointer

* When the URG flag is set, the URG field contains an offset from the seq number to the end of the urgent data
  * If this point to data not yet acknowledged, the user must be notified to pass in urgent mode 
  * What is done in this mode isn't part of the protocol
* To use the URG flag, the sender must send at least 1 byte of data

### Transmission Control Block \(TCB\)

* Information about a connection are stored inside a TCB, some of them are :
  * Local & remote socket numbers
  * Security & precedence
  * Local & remote windows values
  * Pointers to :
    * The send & receive buffers
    * The retransmission queue
    * The current segment
  * Local & remote urgent pointers \(if needed\)
  * Variables concerning our seq number :
    * Its value
    * The seq number of the first segment not yet acknowledged
    * ISS : Initial Send Sequence number 
  * Variables concerning the ack number :
    * Its value
    * IRS  -  Initial Receive Sequence number
  * Current segment variables that will go into the header

### Reset & Error handling

* Reset \(RST\) close a connection, the receiver state influence the response :
  * If it's in LISTEN, it ignores it
  * If it was in LISTEN and now is in SYN-SENT, it goes back to LISTEN
  * Any other state : it goes to CLOSED
* An RST uses the ack number of the segment it responds to as it's seq number, not the one it should be using \(in case the two ends are de-synchronized\)
  * If the received segment ACK flag is on set, it will : 
    * Use 0 as it's seq number
    * Set its ack number to the received segment seq number + length
* RST are sends when :
  * Any segment other than RST is received on a CLOSE connection
  * Any segment with non-matching security settings is received
    * Except if our SYN hasn't yet been acknowledged, then we must try to match the setting or send an RST
  * The connection is in a non-synchronized state and the ack number of the received segment is wrong
* When a connection in a synchronized state receive a segment with an unacceptable ack number, it must return an empty ACK segment with the correct seq and ack fields
  * The connection remains in the same state

## Interface

* TCP has 2 interfaces, one with **applications**, and one with **lower protocols**
* There's isn't any expectation for the protocol one, except that **asynchronous** dialogue must be possible
  * TCP can work with a wide variety of protocols, but the most commonly used is **IP**
* The interface with application isn't much defined,  but some basic calls are expected
  * Format can be adapted on implementation
  * OPEN - Create a connection
    *  Format :  `OPEN (<local_port>, <foreign_socket>, <active | passive>       [timeout] [precedence] [security] [options])        -> local_connection_name`
    * Depending of the       implementation, the **source address** will be supplied by TCP or lower protocols
    * Either **actively** starting a connection or **passively** waiting for one
      * We can specify the **foreign socket** from whom we await a connection or accept any
      * SEND will transform a passive call to an active one
    *  A TCB is created and partially       filled in from the parameters
    * The timeout is by default 5 \(what ?\), if it occurs, the connection will be aborted
    * The rights of the user to open a connection are checked
    * A **local connection name** is returned, it's a short hand for the connection defined by the &lt;local socket, foreign socket&gt;       pair
  * SEND - Send the data in the buffer through the connection
    *  Format :  `SEND (<local_co_name>, <buffer_address>, <byte_count>, <PUSH>, <URGENT>[timeout])`
    * Since the connection is a constant stream of data, they're not given to the application after each new segment arrived
      * Instead, it is placed into a buffer
      * The receiving end can decide when to give the buffer to the application waiting for it
      * Except if the **PUSH** flag is set, telling the receiving end to immediately push \(give\) the data to the applicatio
    * Some implementation do an implicit OPEN when a SEND is made on a non-existent connection, others return an error
  * RECEIVE - Allocates a receiving buffer associated with the     specified connection
    * Format : `RECEIVE (<local_co_name>, <buffer_address>, <byte_count>) -> byte_count, urgent_flag, push_flag`
  * CLOSE - Indicate that there isn't any more data to send \(send a FIN\)
    * Format :  `CLOSE (<local_co_name>)`
    * Outstanding SENDS will still be transmitted & retransmitted
    * Implies PUSH
    * Data can still be RECEIVEd until the other side CLOSEs the connection
    *  Communication is needed to close a connection, so the system may stay in the CLOSING state some time
  *  STATUS - Depend on implementations, gives info to the application, usually one from the TCB
    * Format :  `STATUS (<local_co_name>) -> status_data`
  * ABORT -  Abort all pending SENDs and RECEIVES, remove the TCB send a special RST
    * Format :  `ABORT (<local_co_name>)`

## Headers

![](../../.gitbook/assets/tcp-headers.png)

* Source Port : 16 bits
* Destination Port : 16 bits
* Sequence Number :  32 bits
* Acknowledgment Numbe r:  32 bits
* Data Offset :  4 bits
  * The number of 32 bit words in the TCP Header
  * Indicates where     the data begins
* Reserved :  6 bits,   reserved for future use, must be 0
* Control Bits :  6 bits \(from left to right\) :
  * Bit 0 - URG :  Urgent Pointer field significant
  * Bit 1 - ACK :  Acknowledgment field significant
  * Bit 2 - PSH :  Push Function
  * Bit 3 - RST :  Reset the connection
  * Bit 4 - SYN :  Synchronize sequence numbers
  * Bit 5 - FIN :  No more data from sender
* Window : 16 bits
* Checksum : 16 bits
  * It's value is the 16-bits 1's complement of the 1's     complement sum of all 16-bits words in the header & text
  * During computation of the checksum :
    * It's value is 0
    * Padding that won't be sent may be added 
  * A 96-bits pseudo header is also virtually prefixed \(it won't be sent along\) to the real header
    * It contains the source & destination addresses, protocol information and the TCP length \(without this pseudo headers\)
    * This protects against misrouted segments
* Urgent Pointer : 16 bits
* Options : Variable   & Optional
  * May or may not be present, but must be understood by all implementations
  * Their length is a multiple of 8 bits
  * There's either only the 1-byte option type or :
    * The 1-byte option type
    * + The 1-byte option length \(which takes the 2 bytes of type + length and the length of the data\)
    * + The option data
  * There's 3 possible options :
    * 0 : End of option list \(terminate the list in case it doesn't fall on the end of the header\)
    * 1 : No operation \(filler to align on a boundary for example\)
    * 2 : Maximum segment size
      * Only set with SYN segment
      * Optional, if it's not present any size is allowed
      * Length : 4 \(bytes ?\) 
* Padding : Variable & Optional

## Modification

* A number of details in RFC 793 were corrected, modified, or clarified in RFC 1122
* This is the correlation table

| Feature | RFC 793 Section | RFC 1122 Section |
| :--- | :--- | :--- |
| Received PUSH bit | 2.8 | 4.2.2.2 |
| Urgent Pointer | 3.1 | 4.2.2.4 |
| TCP state diagram | 3.2, p.23 | 4.2.2.8 |
| Simultaneous Open | 3.4, Fig 8 | 4.2.2.10 |
| Retransmission Timeout | 3.7 | 4.2.2.15, 4.2.3.1 |
| Event Processing | 3.9 | 4.2.2.20 |

## Sources

* [RFC 793](https://tools.ietf.org/html/rfc793)
* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson
* Pentesterlab

