---
layout: post
title: Reusing the Identities Set by the JavaScript Library
categories: [apis, javascript]
author: Eric Fung
summary: Our JavaScript library generates customer identities. You can obtain these identities to use them with our server-side libraries or [URL API][url].
---
Our JavaScript library attempts to reduce your work in keeping track of unique people:

1. It generates its own anonymous IDs to represent unknown visitors.
2. When you `identify` an anonymous user, it connects the anonymous ID with the named one you provide, via an `alias` behind the scenes.
3. It remembers the anonymous or named identities across browsing sessions.

So if you plan to use our server-side libraries or [URL API][url], you can tap into the identities that were created and stored by our JavaScript Library.

# JavaScript Functions

* `KM.i()`: returns the current user's identity, whether it's anonymous or not.
* `KM.gc('ai')`: returns the value of the cookie `km_ai`, which is the current user's generated anonymous identity.
* `KM.gc('ni')`: returns the value of the cookie `km_ni`, which is the current user's named identity. This was set either from making an `identify` API call or from passing the `kmi` parameter in our [URL API][url].

*Note: You likely need to use only `KM.i()`. The function works by checking `KM.gc('ni')`, then checking `KM.gc('ai')` if it doesn't find a named identity.*

## Wait For Our JavaScript Library to Load

Since the KISSmetrics script loads asynchronously, you'll want to make sure that the library has loaded before you try to call `KM.i()`. 

To do this, wrap your call to `KM.i()` in a [callback function][callback], and push that function to `_kmq`

    <script type="text/javascript">
    _kmq.push(function() {
	  alert(KM.i()) // Display an alert box with your current KM identity
	});
    </script>

## PHP Example

One of our customers shows how they check for the `km_ai` cookie using PHP and reuse that ID:

    if (isset($_COOKIE['km_ai'])) {
	  KM::alias($_COOKIE['km_ai'], $email);
      KM::identify($email);
      KM::record('Cart Checkout');
	}

# Generating Your Own Random Identities

You can choose to generate your own identities server-side. However, please *do not* set the `km_ai` cookie yourself - our JavaScript library expects to set the cookies on its own.

Instead, you can call `identify` with your generated ID. For example:

    _kmq.push(['identify', <?php echo $generated.id ?>])

This treats *your* random ID as a 'named' ID, so you'll have to explicitly call `alias` when they truly become identified, after a **Signup** or **Login** or **Subscription** type of event.
-->

[url]: /apis/url
[callback]: /apis/javascript/javascript-specific#callback-functions