---
layout: "../layouts/BlogPost.astro"
title: "Don't write Rust like it's Java"
description: "I didn't discover the joy of writing Rust code until I stopped trying to make the language something it isn't."
pubDate: "Oct 7 2023"
---

I’ve been interested in the _idea_ of Rust for a couple years now. Type safe, memory safe, and an emphasis on correctness. What’s not the love?

The percentage of errors I encounter while working on [Apollo](https://apollo.fyi) (a Python app) that could have been caught by the Rust compiler is quite high (I won’t claim 100%, but pretty close). In general, compilers can catch a lot of issues that might otherwise make their way to production when using a dynamic language (like Python or Ruby), though not all compilers are equal. Type safety is great, but Rust’s emphasis on _correctness_ is where I find the most appeal.

I’ve been writing a fair chunk of Java at work. While not my favourite language, the compile time checks are empowering. Significant refactors aren’t as scary as in Python or Ruby. You have the compiler on your side! An incorrect or missing import statement isn’t going to grind your program to a halt at runtime. We usually have tests to catch these issues, yes, but there’s something to be said about having these checks baked into the language.

The Java compiler isn’t perfect however. There are entire classes of errors it does not protect against, the most infamous being null references. (Almost) everything can be null in Java, and you won't find out until runtime. Rust on the other hand has constructs in place to guide you towards handling unknown values. You can of course choose to ignore such guidances, but the compiler forces you to make a deliberate decision to do so.

So is Rust a better Java? There is certainly a lot to like. The _promise_ of Rust is one I find incredibly enticing. But my Rust journey hasn't been all sunshine and rainbows. Despite the similarities, Rust is _not_ Java. I didn't discover the joy of writing Rust code until I stopped trying to make the language something it isn't.

## Everything must be an interface

While not entirely accurate, there's some truth to the trope that Java developers need everything to be an interface (I am one such developer). Interfaces in Java are fun to work with. Your application is composed of small units of work, where no one unit of work has direct knowledge of the inner workings of another. Bootstrapping your dependency tree requires some work up front, but once complete, you have an army of independent services at your beck and call.

We don't have interfaces in Rust; we have [traits](https://doc.rust-lang.org/book/ch10-02-traits.html). They're similar to interfaces in Java in many ways. However, attempting to make _everything_ a trait in Rust isn't fun. Remember that great feature of Rust being memory safe? It comes at the cost of not being able to easily "inject" something that implements a trait.

```rust
trait Named {
    fn name(&self) -> String;
}

struct Service {
    named: Named
}
```

The above code will not compile, as the size of `Named` cannot be determined at compile time. To get around this, we can ["box"](https://doc.rust-lang.org/std/boxed/struct.Box.html) the trait, allowing us to point to dynamically allocate memory on the heap (called a trait object). The `Box` itself is of a known size, allowing our program to compile.

```rust
trait Named {
    fn name(&self) -> String;
}

struct Service {
    named: Box<dyn Named>
}
```

Boxing isn't my favourite pattern as they're awkward to work with. I avoid them if possible. We can instead use _generics_ to specify the trait type.

```rust
trait Named {
    fn name(&self) -> String;
}

struct Service<T: Named> {
    named: T
}
```

How is this different? At first glance the result is the same. The difference comes down to **dynamic** vs **static** dispatch. With a trait object, the concrete type is resolved at _runtime_. With generics, the concrete type is resolved at _compile time_.

In practice this means that as long as we own all code in question, we can get away with only generics. When we're building code with _unknown consumers_ however (such as with a library), a boxed value might be necessary.

## What about ownership?

The question of ownership remains. What if our `Named` trait is a required dependent of other services in our application? Do we create a single "master" `Named` and pass in a `&Named` to each dependant, introducing lifetimes?

```rust
struct Service<'a> {
    named: &'a dyn Named
}
```

Or do we use an [`Arc`](https://doc.rust-lang.org/std/sync/struct.Arc.html) such that our dependent services hold onto an `Arc<Box<dyn Named>>`, allowing concurrent access of the owned resource?

```rust
struct Service {
    named: Arc<Box<dyn Named>>
}
```

I’ve tried both approaches. They _work_, but aren't enjoyable, especially when every service in our app is affected.

## It's okay to use pure functions

Forcing Rust to be a purely object oriented language isn't fun. While I do still write "service objects" as in the above examples, I try and only use them where necessary, instead preferring pure functions.

Consider a function for handling a Stripe checkout session complete event that updates the Stripe customer ID in our system.

```rust
async fn handle_session_completed(
    user_repo: &mut impl UserRepo,
    session: &CheckoutSession,
) -> anyhow::Result<()> {

    let user_id = session
        .client_reference_id
        .clone()
        .context("Missing client reference ID")?;

    let customer_id = session
        .customer_id
        .clone()
        .context("Missing customer ID")?;

    user_repo
        .update_stripe_customer_id(user_id, &customer_id)
        .await?;

    Ok(())
}
```

While we could have written this as a service where `UserRepo` is an injected value, doing so would introduce the complexities we've already explored. There's also no _reason_ to write this as a service as we can still easily inject different implementations of `UserRepo`, such as providing an implementation that doesn't hit a live database. The downside is our function signature can get a bit busy, but this level of "pain" is nothing compared to the alternatives.

## Embrace Rust for what it is

I feel deep into the hole of **Rust is hard**. A big reason was my insistence that Rust code should look like other code I've written before. While drawing from the past is the boon of experience, embracing existing idioms is important to achieve mastery. Rust requires a mindset shift. Don't fight Rust for what it isn't, embrace it for what it is.
