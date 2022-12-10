---
layout: "../layouts/BlogPost.astro"
title: "A Look Inside Mastodon"
description: "A look inside the inner workings of Mastodon's decentralized social network."
pubDate: "Nov 28 2022"
---

[Mastodon](https://joinmastodon.org/) is an open source social network similar to [Twitter](https://twitter.com/), but it’s _decentralized_. The network is made of individual servers that are _federated_ (connected). Accounts exist on a single server, but posts and other activity transcend server boundaries.

Understanding the decentralized nature of Mastodon isn’t paramount. Creating and sharing posts is a familiar process. But understanding the three “timelines” will augment the experience.

- **Home.** Posts from users you follow.
- **Local.** Posts from users on your server.
- **Federated**. Posts from users your server is _federated_ with.

We’ll dive into **federation**, but first let’s explore how posts from users you follow make it to your **home** timeline.

> Before we go deeper, it’s worth noting Mastodon is powered by [ActivityPub](https://www.w3.org/TR/activitypub/), an open source social networking protocol. Most of the proceeding is an explanation of how ActivityPub works. Mastodon is just one implementation of it.

Accounts exist on a single server. For example, [my account](https://fosstodon.org/@jgayfer) is on [fosstodon.org](https://fosstodon.org/). The server is responsible for storing account data such as posts, likes, and followers. An account can also be referred to as an [Actor](https://www.w3.org/TR/activitypub/#actors), as defined by the [ActivityPub](https://www.w3.org/TR/activitypub/) specification. An Actor has many attributes, but some are of particular note to understanding federation.

- [**Inbox.**](https://www.w3.org/TR/activitypub/#inbox) Messages received by this Actor.
- [**Outbox.**](https://www.w3.org/TR/activitypub/#outbox) Messages produced by this Actor.
- [**Following.**](https://www.w3.org/TR/activitypub/#following) Actors this Actor is following.
- [**Followers.**](https://www.w3.org/TR/activitypub/#followers) Actors that follow this Actor.

When a user follows another, the respective **following** and **followers** fields are updated. Users do not need to be on the same server to follow each other. The definition of a users account resides on a single server, but references can exist on many servers through the **following** and **followers** fields. This is why Mastodon’s search behaves differently across servers; the server only knows about users it has seen before.

When a user creates a post, a message is added to their **outbox**. The message is then pushed to the **inbox** of all users in the author’s **followers** collection. These messages are often published cross-server, but can just as easily be published to an inbox on the same server.

> The keen observer may note a potential redundancy. If a user has multiple followers on the same server, publishing the same message to each inbox is inefficient. This is where the [`sharedInbox`](https://www.w3.org/TR/activitypub/#sharedInbox) field comes in. Users on a server have the same [`sharedInbox`](https://www.w3.org/TR/activitypub/#sharedInbox), allowing for smart message batching.

When a server routinely publishes posts to another server’s inbox, we consider them _federated_. This is how posts make their way to the **federated** timeline. The **federated** timeline contains unique posts across all **inboxes** and **outboxes** on the server. More plainly, it contains all posts the server knows about.

The culmination of this interconnected mesh of federated servers is referred to as the [fediverse](https://fediverse.info/). Servers are independently hosted, but communicate to produce an experience greater than the sum of its parts.
