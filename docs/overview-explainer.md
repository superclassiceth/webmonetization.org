---
id: overview-explainer
title: Explainer Overview
sidebar_label: Overview
---

This document describes the basic terminology and that is needed to navigate
the Web Monetization specification, and explains the other specifications that
Web Monetization depends on.

## Terminology

**Web Monetization** is a standard that allows sites to get paid with real-time
micropayments and respond to payment with an improved user experience.

The **Web Monetization API** connects **Web Monetization providers**, **Web
Monetization agents**, and **Web Monetization receivers** via the browser.

The browser implments this API and ensures that the Web Monetization provider,
the Web Monetization agent, and the Web Monetization receiver do not learn the
user's browsing history.

The Web Monetization provider is a service that users signs up with which is
capable of sending payments over the **Interledger network**. The browser tells
the provider when a site has used the Web Monetization API to communicate its
payment details.

The payment details that the site shares are the location of a Web Monetization
receiver, a wallet capable of receiving payments from the Interledger network.

The Web Monetization agent, which may be separate from the Web Monetization
provider, makes decisions about how much to pay each site before money is sent.

## Components

Everything you need in order to understand Web Monetization lives in the
following standards.

### Interledger

Web Monetization relies on a common payment network between all parties so that
sites can get paid by any provider. Web Monetization standardizes on Interledger
as the common payment network.

For the purposes of understanding Web Monetization, you can think of
Interledger as a payment network with no minimum payment size and latency under
a second. Interledger does not require sender and receiver to agree on a
currency: The sender's preferred currency is converted to the receiver's
preferred currency by the network.

A single HTTP lookup known as an "SPSP query" occurs before Interledger payment
occurs in order to fetch receiver details.

If you want to dive deeper into how Interledger works, the project lives on
[interledger.org](https://interledger.org), and the pieces used by Web
Monetization are contained in the core [ILPv4
protocol](https://interledger.org/rfcs/0027-interledger-protocol-4/) and the
[Simple Payment Setup Protocol
(SPSP)](https://interledger.org/rfcs/0009-simple-payment-setup-protocol/).

### Web Monetization

[The Web Monetization explainer](./explainer) walks through the flow of the Web
Monetization protocol and the proposed API. It is the API implemented by a Web
Monetized browser and consumed by a Web Monetized site.

### Privacy Pass

[The Privacy Pass protocol](https://privacypass.github.io/) defines a way for
some issuer to give cryptographic tokens to a client that can be redeemed and
validated later by that same issuer.

The key property of this protocol is "Unlinkability," meaning the issuer cannot
connect the tokens it has issues to the tokens that a user redeems.

We have written an [alternative version of the Web Monetization API](#todo) which depends on the Privacy
Pass protocol in the form of the [Trust Tokens API](https://github.com/WICG/trust-token-api)
to improve the privacy properties of Web Monetization.

This alternative version changes the API between the Web Monetization
Provider/Agent and the browser, but not between the browser and the Web
Monetized site.

By leveraging the Privacy Pass protocol, we can allow a user to authenticate to
their Web Monetization provider anonymously. This prevents the Web Monetization
provider from learning anything about the user's browsing habits, even in the
[failure cases of the normal WM protocol](#todo).
