---
description: Suite of tools facilitating pen test
---

# Burp Proxy

## Setup

* In burp, go in  `Proxy -> Options`
* Retrieve the certificate at [http://burp/](http://burp/)

### With Firefox

* Firefox lets you set up a proxy only for itself and not the whole system, making it practical for that
* In firefox go in :
  * Options
  * Parameters \(at the bottom\)
  * "Manual configuration of the proxy"
  * Set localhost on the port you set in burp \(probably 8080\)
  * Check "Use this proxy for all the protocols"
* Import the certificate by going in :
  *  Options
  * Privacy & Security
  * Show certificates
  * Import
  * Check "Confirm this AC to identify websites"

### With Google

* Use the extension _Fox Proxy_

## Usage

* The superior tabs represent available tools
* The inferior one their options
* By default it's in the **intercept** mode, which stops traffic
  * Change it in `Proxy -> Intercept -> Intercept is on/off`

### Filter tab

Lets you filter request/response on different factors

### Target \(Ctrl + Shit + T\)

* Show a tree view of everything you browsed through the proxy
* Specify scope in `Target -> Scope`
* It can be :
  *  A keyword
  * An ip range
  * An host
  * A regex
  * etc
* We can also add a browsed entry to the scope by right clicking it

### Proxy \(Ctrl + Shift + P\)

* The **Intercept** tab does exactly that to every request, it's on by default
  * You can modify request on the fly with it.
* The **HTTP History** tab shows the history of all requests made and responses received
  * You can highlight request in different color, filter by that and comment them
* In the **Options** tab :
  * The **Match & Replace** section let's you  define rule to modify all traffic going through the proxy
  * In the **Response Modification** section you can, among other things :
    *  Unhide hidden fields
    * Disable javascript validation \(to check if it's only a client side verification\)

### Intruder \(Ctrl + Shift + I\)

* Let brute force / fuzz like a  script would do
* In the **position** tab, you can select the part of the request you want to test and the type of attack
* In the **payload** tab, you can select theI payload you want to use for each position \(chosen the **payload set**\)
* In the **options grep match** tab, you can chose a string against which to match
  * **Grep extract** is similarly useful
* Launch the attack with **Start attack** in the **payload** tab
  * It will open a window with the result
* In the community edition, this feature is restricted

### Repeater \(Ctrl + Shift + R\)

* Let you repeat a request and modify it

### Decoder

* Let you encode/decode text.

### Scanner

* Find vulnerabilities & information
* Be cautious while using it, it can mess up an application if you just launch it on everything
* Pro version only tool

### Sequencer

* If a cookie is set to specific value, the sequencer gene
* The sequencer can generates hundreds of request to retrieve a cookie and analyse how it changes

## Pluggins

* Multiple plugins exist, like :
* Havertor : Way more performant than the decoder
* Logger++ : Quite useful, needs to be the last extension on the list

