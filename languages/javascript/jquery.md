---
description: 'Disclaimer : This is an old note not up to current standard'
---

# Jquery

## Generalities

* To add jquery to a web page, prefer Jquery CDN

## Syntax

* Variable : `var nomVar` \(see `let` in js\)
* jquery object : `$var` ou  `$(...)` Parenthesis can contain these types of elements : 
  * `document`
  * CSS class
  * ID
  * `this`
  * tags
* Example

```javascript
$(document).ready(function() {
    $('.vanish').click(function() {
        fadeOut();
    });
});
```

## Events handling

* You can simply use `.event_name` or :

### $.on :

* The handler is triggered for each event occurring on the binded element and its children
  * Aka, a clic on a element of a `div` will trigger the `.on(click)` of the div
* `$.on( events [, selector ] [, data ], handler )`
  * `$` Represent the jquery object on which the event occurred
    * It must exist when `.on()` is called
  * `events` The name\(s\) of the concerned event\(s\)
  * `handler` An anonymous function of the name of the function to call after the event happened
  * `selector` The child of the `$` on which the event occurred
    * This makes delegation possible
  * `data` Data transmitted accessible by `event.data`
* Example with a function name :

```javascript
$(“dot”).on("mouseenter", selectDot);
```

* Example with an anonymous function :

```javascript
$(“dot”).on(“mouseenter”, function(event){
    event.preventDefault();
    alert(“Hello world”);
});
```

* Example with a selector :

```javascript
$(“main”).on("mouseenter", ".dot", selectDot);
```

* Exemple with data:

```javascript
$(“main”).on("mouseenter", ".dot", “nb : 12”, selectDot);

// In selectDot :
alert(event.data.nb);
```

### Performances

* Delegation can help with performance
* However, if you delegate to a too high level, like the `body`, the event will be triggered continuously which will hinder performances
* It's better to delegate near the target

## ajax

* Ajax lets you send requests and receive responses asynchronously to modify the page without having to reload it
* In js, you must use the `XmlHttpRequest` class
* Jquery has simpler functions : 
  * `$.ajax()`
  * `$.get()` &`$.post()` : shortcuts for the 1st one

```javascript
$("#form").submit(function(e){ 
    e.preventDefault(); 
    $.ajax({
       url : 'page_handling_the_data',
       type : 'POST',
       data : $(this).serialize();
       dataType : 'html',
       success : function(data_received_by_ajax, status){
            //code
       },
       error : function(result, status, error){
            //code
       },
       complete : function(result, status){
            //code
       }
    });
});
```

* `data` Data to send as strings
  * Optional for GET
  * `Serialize()` Let you automatically concatenate form data
* `type` Request type \(GET by default\)
* `dataType` Return type de retour \(html, xml, json, text\)
* For `error` & `complete`, the result is an XHR object

## Source

* Jquery's [doc](https://api.jquery.com/)

