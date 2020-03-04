---
id: privacy-explainer
title: Privacy Model
sidebar_label: Privacy
---

> TODO: clarify which of these constraints are imposed by the interaction
> between browser components and which are imposed by the interaction between
> parties over Interledger/Web APIs

## Privacy Goals

Because Web Monetization can happen without user interaction, it's very
important that it doesn't leak sensitive information to third parties.

The most critical information we aim to protect is the user's browsing history
(list of visited domains or visited URLs). We also want to ensure the user's
identity (known by the Web Monetization provider) is never learned by the
Web-Monetized sites that they visit.

## Types of Information

- **Browsing history** allows an actor to learn what URLs or domains you are
  visiting. No actors in WM learn Browsing History.

- **Wallet history** allows an actor to learn which WM receiver a user is
  paying to.

- **Unique identity** allows an actor to uniquely identify a given user.

- **WM provider** allows an actor to learn which web monetization provider a
  user has.

- **Timing** allows an actor to learn when a user is using Web Monetization,
  and the size of the micropayments.

Without learning **unique identity**, an actor cannot associate this
information to a given identity, not even an anonymous one. So an actor who
learns **timing** would learn the times/amounts of micropayments paid in a
given page visit, but not across two page visits.

An actor may learn **unique identity** through other methods (e.g. a publisher
may let you sign in), but that's unrelated to Web Monetization.

### Failure Cases

**Wallet history** is usually much more vague than **browsing history**. Many
sites use the same wallet, so an actor learning **wallet history** cannot map
those wallets back to sites.  However there are failure cases where **wallet
history** can be tantamount to **browsing history**: if a wallet has an
implementation bug then it may leak the specific user to actors who learn
**wallet history**, or if a given site runs its own wallet then **wallet
history** would map 1:1 to **browsing history** for that site. The privacy-pass
based WM scheme is intended to stop the Web Monetization provider from tying
that leaked information back to the user in that case, neutralizing this
threat.

## Threat Actors

> TODO: STREAM changes may prevent WM receiver from learning WM provider.
> However WM changes may make it useful to tell WM receiver who WM provider is
> anyways.

| Actor | Information learned during WM |
|:--|:--|
| [WM Receiver](#web-monetization-receiver) | timing, WM provider |
| [WM Provider](#web-monetization-provider) | unique identity*, timing, wallet history |
| [Intermediary Connectors](#intermediary-connectors) | timing, wallet history |
| [Publisher](#publisher) | timing |
| [ISP](#isp) | timing, WM provider, wallet history |

\* **Unique identity** is not learned by WM provider [if privacy pass protocol is used](#todo)

### Web Monetization Receiver

The Web Monetization Receiver learns **timing information** and **web
monetization provider**.

The Web Monetization Receiver learns timing information due to the real-time
nature of WM. It handles incoming micropayments so it knows when they occur +
the amounts.  However, it cannot use this to track a user across sessions.

The receiver can learn which WM provider a visitor belongs to, but again cannot
use this to track the user across sessions. Because WM providers take on a
large number of users this information is not a productive vector for tracking
the user on its own.

### Web Monetization Provider

The Web Monetization Provider learns **timing
information**, **wallet history**, and sometimes **unique identity**.

Because the WM provider is involved in all WM sessions, it has the threat of
building up the largest profile about a user based on their WM usage. That's
why we make sure there are countermeasures to ensure the WM provider never
learns **browsing history**.

The user must sign into their WM provider, which means the WM provider learns
**unique identity**. It's possible the WM provider may learn more identifying
information like full name, credit card information, or even KYC data,
depending on their requirements.

The WM provider _does not_ learn **unique identity** if the [privacy pass
scheme](#todo) is used, because the user is authenticating with temporary-use
blinded tokens and not their real identity. It separates the sign-in from the
WM operations where information could leak.

The WM provider sends all payments to the WM receivers for sites visited by the
user, and as a result learns the **wallet history** & **timing**. In the [failure
cases](#failure-cases) of **wallet history** this can be tantamount to
**browsing history**, but if the [privacy pass scheme](#todo) is used then a
browsing profile cannot be built because the history cannot be associated to a
unique identity or user.

### Intermediary Connectors

Intermediary Interledger connectors learn **timing** and **wallet history**
information.

Intermediary connectors of the interledger network pass on micropayments so that
they eventually reach the WM receiver. As a result, they learn the **wallet history**
and **timing**.

Source information (the originating user) is not learned by intermediary
connectors though, so they _do not_ learn the **WM provider**, nor do they
learn **unique idenity**.  This means that even in the [failure
case](#failure-cases) of **wallet history** the intermediary connectors can
never create a browsing history profile.

### Publisher

The publisher learns only **timing** from the events emitted by the Web
Monetization API.

However, the publisher chooses the WM receiver they use.  If they choose a WM
receiver who colludes with them to share information, this means they could
learn **WM provider** too.

In many cases, the publisher will have a **unique identity** associated with
the user due to some process other than Web Monetization (for instance the user
may sign into the publisher's website). In that scenario they could associate
the times of micropayments to a user. This is intentional, though, because it
allows a publisher to determine whether a user is Web Monetized.

### ISP

An ISP can see when connections are made to an IP address that is known to be a
WM provider, and so learns **WM provider**, **timing**, and **wallet history**.

The ISP could monitor traffic to known WM provider IPs to determine when
ILP-like traffic is occuring. All ILP traffic goes over TLS but could look
distinctive to an ISP. Furthermore it can monitor connections to known
ILP-enabled wallets to build up a profile of **wallet history**.

The [failure cases](#failure-cases) of **wallet history** are less of a concern
here, however. In the case where wallet maps 1:1 to domain, it is largely
inconsequential because the ISP already knows the domains visited off the IP
address.

In the case where different pages on a publisher site use different wallets,
the ISP could monitor connections to the different wallets' IP addresses to
guess at which page of the publisher's site the user is on.  For instance if
one creator on a video site runs their own wallet, and the ISP sees a
connection to that wallet's IP and the video site's IP it could guess at which
page of the video site you're viewing. This risk can be mitigated on the
publisher's side by proxying SPSP requests to the wallet, or can be mitigated
by the WM provider offering some kind of proxying service to anonymize requests
to wallets.
