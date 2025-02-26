---
date: 2024-08-01
title: "We’ve decided to make less money [Part 2]"
featuredImage: >-
  https://res.cloudinary.com/dmukukwp6/image/upload/posthog_is_the_cheapest_e77c4ea4a5.jpg
author:
  - james-hawkins
featuredImageType: full
category: PostHog news
---

## TL;DR

- Last week, we [slashed the cost of session replay](/blog/session-replay-pricing) by more than 50%, making PostHog the cheapest session replay tool in its class.

- Today we're announcing Part 2, a change to our pricing that makes tracking events for anonymous users up to 80% cheaper.

- We'll see a ~30% decline in product analytics revenue from the change, but it will unlock several new use cases, such as web analytics at massive scale, and tracking high-volume backend events, that were previously prohibitively expensive.

## What's changing?

Until now, we charged the same for all events we ingested. This is standard practice among product analytics tools. Popular tools like Mixpanel, Amplitude, and Heap all charge customers the same for each tracked user, session, or event, regardless of the cost to them.

But now we’re changing that by splitting our product analytics pricing into two event types:

<h3><strong className="text-red dark:text-yellow">1. Identified events</strong> <span class="text-sm font-normal text-primary/75 dark:text-primary-dark/75">(previous default event type)</span></h3>

Events generated by logged-in users associated with a person profile, which **can include custom data** like a user's name and email address

Identified events let you create _user-specific insights and cohorts_ based on person properties. _They cost the same as our existing event pricing._

<h3><strong className="text-blue">2. Anonymous events</strong> <span className="relative -top-0.5 inline-flex px-1 rounded bg-blue text-white uppercase font-bold text-sm">New</span></h3>

Events that **only include non-identifying data,** such as referral data (UTMs, domains, etc), device type, and basic location information

Anonymous events are _between 55% and 80% cheaper_ for most of our customers, and will soon become the default event type.

<div class="md:hidden mb-4">
<img src="https://res.cloudinary.com/dmukukwp6/image/upload/event_types_mobile_light_eca61d99fe.png" alt="Event prices" class="dark:hidden" />
<img src="https://res.cloudinary.com/dmukukwp6/image/upload/event_types_mobile_dark_686a64f4f1.png" alt="Event prices" class="hidden dark:block" />
</div>

<div class="hidden md:block mb-4">
<img src="https://res.cloudinary.com/dmukukwp6/image/upload/event_types_desktop_light_f8989c06cd.png" alt="Event prices" class="dark:hidden" />
<img src="https://res.cloudinary.com/dmukukwp6/image/upload/event_types_desktop_dark_89c055bd76.png" alt="Event prices" class="hidden dark:block" />
</div>

In reality, if you're tracking both anonymous traffic (like website visitors) and identified users (like logged-in customers), you'll likely use a mix of both event types. We've updated [our pricing calculator](/pricing#calculator) to help you better estimate your exact usage and potential savings.

If you were to switch to 100% anonymous events, here's a breakdown of the cost savings:

| **Monthly events** | **Identified events** | **Anonymous events** | **Change** |
|--------------------|--------------------------|-----------------------------------|----------------|
| <strong class="text-15px">0-1 million</strong>      | Free | Free  | &nbsp |
| <strong class="text-15px">1-2 million</strong>      | <span class="text-[15px] font-semibold">$0.0002480</span><span class="text-sm opacity-70">/event</span> | <span class="text-[15px] font-semibold">$0.0000500</span><span class="text-sm opacity-70">/event</span>  | <strong class="text-green">80% cheaper</strong> |
| <strong class="text-15px">2-15 million</strong>     | <span class="text-[15px] font-semibold">$0.0001040</span><span class="text-sm opacity-70">/event</span> | <span class="text-[15px] font-semibold">$0.0000343</span><span class="text-sm opacity-70">/event</span>  | <strong class="text-green">67% cheaper</strong> |
| <strong class="text-15px">15-50 million</strong>    | <span class="text-[15px] font-semibold">$0.0000655</span><span class="text-sm opacity-70">/event</span>  | <span class="text-[15px] font-semibold">$0.0000295</span><span class="text-sm opacity-70">/event</span> | <strong class="text-green">55% cheaper</strong> |
| <strong class="text-15px">50-100 million</strong>   | <span class="text-[15px] font-semibold">$0.0000364</span><span class="text-sm opacity-70">/event</span>  | <span class="text-[15px] font-semibold">$0.0000218</span><span class="text-sm opacity-70">/event</span> | <strong class="text-green">40% cheaper</strong> |
| <strong class="text-15px">100-250 million</strong>  | <span class="text-[15px] font-semibold">$0.0000187</span><span class="text-sm opacity-70">/event</span>  | <span class="text-[15px] font-semibold">$0.0000150</span><span class="text-sm opacity-70">/event</span> | <strong class="text-green">20% cheaper</strong> |
| <strong class="text-15px">250+ million</strong>     | <span class="text-[15px] font-semibold">$0.0000100</span><span class="text-sm opacity-70">/event</span>  | <span class="text-[15px] font-semibold">$0.0000090</span><span class="text-sm opacity-70">/event</span> | <strong class="text-green">10% cheaper</strong> |

This means a website owner who sends 10m events per month would pay just $324 per month if they switched to only sending anonymous events, saving $9,072 per year in the process.

## When should I use anonymous events?

Anonymous events are ideal for tracking users who aren't logged in, or for use cases where you only need to analyze behavior at an aggregate level, such as:

- Visitors to your marketing website
- Shoppers on an e-commerce store
- Logged-out users of mobile apps
- Tracking API and backend events
- Events generated by LLMs
- Error monitoring

If you mostly track logged-in users, however, you should continue to do so. 

## Why are anonymous events so much cheaper?

The ELI5 version is that they cost less to ingest and process, so we're passing that saving onto you.

Here's the slightly longer version:

- When you call `posthog.identify` or capture an event from an identified user, we do a bunch of processing to create, update, or merge person profiles in our backend. These are stored in Postgres, separate from our events tables in ClickHouse, making this one of the most complex and expensive steps in [our ingestion pipeline](/docs/how-posthog-works/ingestion-pipeline).

- When ingesting anonymous events, we can entirely rely on our cheaper, faster ClickHouse tables. For example, we use a sessions table to capture session properties, such as the referring domain and the URLs visited.

- This lets us charge you much less for these events, saving you money and enabling many use cases that would have been cost prohibitive before.

## Why are you deliberately making less money?

Because it's the right thing to do for both our users and our business.

Most of our competitors are inefficient. They employ huge [outbound sales teams](/founders/negotiate-software-better) to grow revenue. Their salaries and commissions for closing deals are passed onto their customers through higher prices.

In contrast, we're 100% inbound, we [grow mostly through word of mouth](/handbook/how-we-get-users), and we charge based on actual usage. 

We don't believe in loss-leaders, so we make a modest positive margin on each event sold, but what we charge is directly connected to what it costs us, not what we think we can get away with charging.

We grow our revenue through helping you grow, and onboarding you onto other tools, like session replay, feature flags, and surveys.

We think this is better than trying to squeeze you for every cent you have. You have a great experience, and we'll enjoy better retention and word of mouth for doing the right thing.

## When is Part 3?

It's in pre-production. Release date TBD.

## Would you rather fight one horse-sized hedgehog, or 100 hedgehog-sized horses?

We would never fight hedgehogs, how dare you. 

<br />

> ## FAQ
> <p class="!-mb-12 text-xs">&nbsp;</p>
>
> ### Do I still get 1 million free events?
> Yes, your first 1 million events each month are free, regardless of what type of events you're sending.
>
> ### How will my bill change?
> 
> All events are charged at the base rate for anonymous events. Identified events are then billed as an extra rate on top of them, referred to as 'Person profiles' on your bill. 
>
> ### What are the limitations of anonymous events?
>
> With anonymous events, you cannot:
> - Filter on persons (e.g. an individual user).
> - Do multi-touch attribution – only last touch is supported.
> - Target by person properties for feature flags, A/B tests, and surveys.
> - Create cohorts filtered on person properties (e.g. paying vs non-paying users).
> - View a person’s profile in the app, or query the persons table in our SQL insights.
> - Use [groups analytics](/docs/product-analytics/group-analytics) on anonymous events.
>
> If you need the above functionality for all your traffic, you don't need to change anything. Continue to send person data with your events.
>
> ### Can I use anonymous and identified events together on the same project?
>
> Absolutely. You can capture identified events where you need more detailed information and anonymous events when you don't need that detail.
>
> Here's how we use them:
>
> - On our website, most users are tracked using anonymous events. When people visit our website, referral data like Entry UTM Source are stored in the sessions table.
>
> - When someone signs up to PostHog and becomes an identified user, referral data like `Entry UTM Source` are merged into the identified user's person profile as `Initial UTM Source`, `Initial Referring Domain`, and so on.
>
> - We run our website and product on the same project. This is how we recommend using PostHog for tracking behavior on both your product and website.
>
> Note, though, that once a user becomes identified, all events they perform are identified events, because we need to check and update their profile each time.
> 
> ###  How do I start using anonymous events?
>
> Anonymous events are the default for the JavaScript Web SDK and snippet.
> 
> They will track anonymous events until an identifying action is taken – i.e. using `identify()`, `group()`, setting person properties with `$set`, etc.
>
> You can get more details on this and other use cases, like server-side events, in our [persons documentation](/docs/data/persons).
>
> ### I have a question you haven't answered...
>
> It may be covered in our [person](/docs/data/persons) and [person properties](/docs/product-analytics/person-properties) docs.
>
> If not, ask it below, in [our community forum](/questions), or raise a support ticket in the app.
