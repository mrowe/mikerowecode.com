---
layout: post
title: "A Clojure library to authenticate with LDAP"
---

[My employer][] has released a small Clojure library I wrote that allows you to easily authenticate users against an LDAP server:

[https://github.com/realestate-com-au/clj-ldap-auth](https://github.com/realestate-com-au/clj-ldap-auth).

It uses the [UnboundID][] [LDAP SDK for Java][] to look up a user name in an LDAP server and attempt to bind with specified credentials.

[My employer]: http://realestate.com.au/
[UnBoundID]: http://www.unboundid.com/
[LDAP SDK for Java]: https://www.unboundid.com/products/ldap-sdk/

The simplest usage looks like:

{% highlight clojure %}
(require '[clj-ldap-auth.ldap :as ldap])

(if (ldap/bind? username password)
  (do something-great)
  (unauthorised))
{% endhighlight %}

That works, but isn't very helpful when authentication fails. So you can also pass a function that will be called with a diagnostic message in the event that authentication fails:

{% highlight clojure %}
(let [reason (atom nil)]
  (if (ldap/bind? username password #(reset! reason %1))
    (do something-great)
    (unauthorised @reason)))
{% endhighlight %}

The provided function should take a single argument, which will be a string.

Configuration of the library (i.e. the ldap server to connect to, etc.) is via system properties. See the [README][] for details.

[README]: https://github.com/realestate-com-au/clj-ldap-auth/blob/master/README.md


## Implementation

The library first establishes a connection to the server, optionally using SSL. If a `bind-dn` is configured (i.e. credentials with which to connect to the LDAP server), it is used to bind to the server. If that's successful, we then look up the provided `username` (in the attribute `uid`). If found, the entry's distinguished name (`DN`) is extracted and this DN and the provided `password` are used to bind a new connection.

If any of these steps fail (e.g. the `binddn` is unauthorised, the `username` can't be found, or the looked up DN and password can't bind) the function returns false (and calls the provided sink function to say why). If everything works and the connection can be bound with the target DN and password, it returns true (and the sink function is not called).

## Limitations

It would probably be useful to be able to specify what attribute(s) to use for looking up the `username`, but for now it is hard coded to `uid`. Also, current test coverage (using [midje][]) is minimal. UnboundID provide an in-memory LDAP server implementation, which could probably be used to build some fast-running integration tests.

[midje]: https://github.com/marick/Midje
