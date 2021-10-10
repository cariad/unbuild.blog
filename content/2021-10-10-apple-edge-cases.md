---
date: 2021-10-10T09:00:00
title: Apple's edge cases
slug: apple-edge
---

Apple is great at Big Stuff.

Whether you love or loathe that Big Stuff, you have to agree that a portfolio of operating systems, mobile devices, desktops, laptops, TV production, medical research and retail stores -- both app and glass-and-steel -- is _unusually_ epic, but business-as-usual to the folks in Cupertino.

You'll never be the biggest company in the world if you suck at the Big Stuff.

But oh boy, does Apple suck at the fuzzy edges around the Big Stuff.

<!--more-->

## A story about debit cards

Last month, my bank terminated their customers' Visa debit cards and replaced them all with Mastercard debit cards.

Generally speaking, I don't care. I've been receiving a trickle of emails about failed subscription payments, but it's just been a matter of entering my new card details and hitting a "pay outstanding bill" button.

No single company has needed more than a couple minutes of my time to fix it.

Until sodding Apple.

## iCloud+

I got two emails from Apple this morning. The first concerned my iCloud+ subscription:

> On 10/10/2021, we attempted to charge you for iCloud+ with 200 GB GB of storage, but there is a problem with your payment information.
>
> Your account will be downgraded to the free 5 GB storage plan if we cannot successfully renew your subscription.

The double "GB" up there is amateurish, but y'know, even trillion dollar teams[^biggest] forget to loop in proofreaders.

[^biggest]: [Statista, "The 100 largest companies in the world by market capitalization in 2021"](https://www.statista.com/statistics/263264/top-companies-in-the-world-by-market-capitalization/)

My boyfriend and I have close to 50 GB of stuff in our family iCloud+ subscription. We can't afford to fall into the free plan. So, I got right on it.

To update my payment details, the email says:

> 1. Go to Settings > [your name] > iTunes & App Store.
> 2. Tap your Apple ID, then tap View Apple ID. You might be asked to sign in.
> 3. Tap Manage Payments (if you are using an older version of iOS, tap Payment Information) and follow the prompts.

Sitting here, reading that email in the iCloud web interface on my Debian desktop, I have to assume that it's referring to the **Settings** app in iOS. The instructions to "tap" are a bit of a clue, too.

So I picked up my iPhone. I opened **Settings**. I tapped on my name. But there's no **iTunes & App Store** in here.

The instructions are several iOS releases out of date.

Apple consistently nails the Big Stuff. They get iOS out the door every year. But fuck, do they ever forget to check the little things.

The closest I could find was **Media and Purchases**. That _does_ have a **Manage Payments** option. And, sure enough, my terminated Visa card was in there.

In the list of payment methods I could add, I spotted Apple Pay. Great! That'd save me finding my card and typing in the details.

I selected Apple Pay. It immediately appeared in my list of payment methods. But I couldn't find a button to pay the outstanding bill.

I tried deleting the terminated Visa card, but it failed. I still had subscriptions attached to it. That'll be the iCloud+.

I found my new Mastercard and added it as a direct payment method alongside Apple Pay, but still no luck. There was no way to pay the outstanding bill.

I started hunting around all the iCloud+ settings I could find. How in bloody hell could I transfer the subscription off the dead card and onto the new one?

After hours of dicking around, I figured it out. The only way to pop open a payment method selector was to upgrade my iCloud+ storage from 50 GB to 2 TB, accept the £6.99 charge, then downgrade back to 50 GB.

After being sent incorrect instructions and hunting for payment settings that don't exist, it finally cost me £6.99 to save my iCloud storage from deletion. Not because my bank had a spat with Visa and switched to Mastercard. Because Apple never considered that a customer might need to pay an outstanding bill on a different card.

## Apple Music

The second email I got from Apple this morning concerned my Apple Music subscription.

Unlike the iCloud email, this one contains a singular instruction. Well, a singular button. "Fix Visa".

I clicked that big blue _Fix Visa_ button, assuming it would take me to some Apple Music web page to update my card details.

Nope. That big blue _Fix Visa_ button tries to open iTunes.

The app.

On my Debian desktop.

And when it fails, it prompts me to install iTunes for Windows.

**Fuck.**

So, back to the iPhone. I tapped that big blue _Fix Visa_ button, and it took me directly to the same list of payment methods page I was on for the iCloud subscription. Still with no option to pay the outstanding bill.

So, guess how I ended up migrating my Apple Music subscription from my terminated Visa to my new Mastercard?

You got it. I had to cancel my Apple Music subscription, then restart it on the new card.

## Invest your time well

iOS 15 wasn't a particularly exciting release. I'm not looking forward to anything in macOS Monterey either.

As a software engineer, I'm certain there are plenty of great updates under the hood. I know how it is. Customers never see most of your work. But as a customer, I'm not seeing anything important to me get fixed either.

I'm on my Debian desktop right now, for example, because my almost-brand-new MacBook Pro has slowed to a crawl. Nothing I do can make it usable.

My new iPad Mini is wonderful, but I had to spend _hours_ on the phone with Apple Support to chase the AppleCare+ package I'd bought at the same time. It took _days_ of callbacks and dead-end transfers before I finally got through to someone who knew that my AppleCare+ was active, but a known bug was preventing it appearing in iPadOS.

And replacing an expired debit card should _never_ take three hours and cost £6.99.
