---
title: "Why Big PHP Frameworks Waste Your Time"
description: "After 20 years of PHP development, I evaluated Laravel, Symfony, CodeIgniter and others for a real IoT project. Here's why I ditched them all for Slim4, Mustache, and Composer libs."
date: 2026-03-26T20:48:52+01:00
draft: false
toc: true
image: "php-titanic.jpg"
---

## tl;dr
For my digital signage project named [Garlic-Hub](https://github.com/garlic-signage/garlic-hub), I spent a month researching suitable PHP frameworks and libraries. Instead, I found bloated, overengineered codebases desperate to be the Swiss Army knife for everything. I ended up with SLIM4, Mustache, and Composer Libs.

## The Evaluations
For the last 20 years, I maintained my own framework. Yes, NIH syndrome at its finest. This time I wanted to do it right: use modern tools, embrace the ecosystem, and stop reinventing wheels.

I spent weeks reading tutorials, comparisons, watching endless YouTube videos about Composer and frameworks.

Shoutout to German speakers: [Vitalij Mik](https://www.youtube.com/@VitalijMik) creates great, compact, no-bullshit PHP tutorials.

## The Real-World Problem: Hardware Doesn't Scale Like AWS
Here's the deal: Garlic-Hub run digital signage on IoT devices, too. Not only EC2 instances or Kubernetes clusters. Actual embedded hardware or NAS with 1-2 GB RAM and ARM processors.

After years of selling SaaS, I needed an efficient, open-source, on-premise solution. Something customers can install once and forget. No monthly fees, no cloud dependencies, no forced updates.

Let's start with the quicker research.

### Laravel - The Hippopotamus
- Looks cozy but is dangerously sluggish
- Enormous consumption of resources
- Immobile, bloated
- Territorial because it leads to vendor lock-ins

![Image description](hippo.webp)

In short, Laravel immediately distinguished itself. I can slow down servers with my own shitty code just fine and don't need Eloquent for that.

I wanted to use my own database classes, field-proven and enhanced since 2003. But stripping Eloquent out of Laravel without breaking the foundation is a chore nobody should have to endure.

**Keyword ORM:** Why do we hide SQL commands? Is programming now only for developers who are too lazy to learn the language of the database? What's so bad about SQL anyway? Its efficiency?

It’s pathetic. We’ve reached a point where "modern" developers feel clever for using a complex abstraction to avoid a simple SELECT. And they pay for it with their hardware.

Why the hell would I hydrate 500 heavy objects into RAM, triggering dozens of 'magic' getters and lifecycle hooks, just to display a simple schedule on a screen?

> It's like calling a logistics company to move a single sheet of paper.

On an IoT device with 1 GB RAM, Laravel's Eloquent isn't just overhead; it's a suicide mission. I don't need a framework that treats my hardware like an infinite AWS bucket. I need code that respects the CPU.

It gets especially funny when the oh-so-sophisticated ORMs surprisingly require hand-optimized SQL for more complex joins.

All this overhead makes Laravel slow as hell. Every little bit of abstraction costs performance, and you can't turn features off. Especially with the standard stack for low-end hardware.

It's no coincidence Laravel has the worst performance in every PHP framework [benchmark](https://github.com/myaaghubi/PHP-Frameworks-Bench).

The vendor lock-in comes on top: Eloquent, Blade, routing conventions are an all-or-nothing deal. Started with Laravel? Stay with Laravel.

No, thanks.

## CodeIgniter - The Bat
- Poor documentation left you in the dark
- Night-active community support if you are lucky
- Somewhat creepily outdated

It looks more outdated than my web designs. And that's saying something.

I have the impression it is stuck in 2010. It's still pushing basic MVC patterns like they're groundbreaking.

CodeIgniter 3: no dependency injection, no real updates for years.

Version 4 breaks more compatibility than Apple has in the last 20 years combined.

And it markets itself as "lightweight." Really? Compared to what? Laravel? That's nothing to be proud of.

According to these [benchmarks](https://github.com/myaaghubi/PHP-Frameworks-Bench), CodeIgniter performs in the same class as Symfony.

Not really impressive for a batteries-included framework.

### CakePHP and Zend/Laminas - The Dinosaurs
- Extinct/dying out
- From another era
- Too cumbersome for modern requirements
- Legacy monsters

![Image description](dinosaurs-apocalypse.webp)

Weren't they really popular at one time? What happened?

CakePHP still clings to conventions from the early 2000s like they're sacred scripture. Zend evolved into Laminas. Basically a corporate rebrand that fooled nobody. Both feel like maintaining COBOL codebases: someone's gotta do it, but it won't be me.

Sorry! Nope! Next!

### Yii - The Weasel
- Unknown, hidden
- Niche, not very widespread
- A bit sketchy

I knew Yii through friends in Eastern Europe and Asia, though for Westerners it feels exotic. I don't care about the "it's from China" nonsense. Code is code. According to the benchmarks, Yii looked decent at first sight.

But:
What's with the frontend/backend template split that ultimately doesn't enforce anything?

After playing around, Yii felt like Laravel for people who don't understand Laravel.

Overall, no real progress since 2017. Just old-fashioned service locators, Active Record patterns that lead to bloated model classes, and nearly everything inheriting from yii\base\BaseObject.

Yii stands for "Yes, It Is". My verdict? No, it's not.

### Symfony - The Kraken
- Tentacle components everywhere
- Complex, confusing for beginners
- Difficult to grasp
- Can hide well (magic under the hood)

After more than a week of research, Symfony landed in my PhpStorm for closer inspection. Seemingly flexible, modular. Let's see.

#### The Test Requirements
Integrate your own DB base classes and build a multilingual form login.

The login and multilingualism worked well enough. But why does a language file in a de folder need to be named xyz.de.yaml?

Seriously? There's no other way unless you write probably 20 helper classes to work around it.

**DB**
I built an adapter interface, as it should be. Based on that, adapters for SQLite3, MySQLi, and Postgres. Created instances via Factory and .env parameters. Access via a DB handler, which calls the adapter methods. Everything is clean, one-liner methods.
If you made it through that paragraph, it's totally banal, run-of-the-mill OOP.

Thanks to Symfony's DI container dictatorship, it only works with massive effort in service.yaml. As soon as I try moving the initialization to an import config, Symfony throws a tantrum.

I wanted to use modern DI, but I spent more time configuring the container's XML/YAML/PHP-Autoloading-Logic than actually writing business logic. That's not productivity; that's framework-babysitting.

By the way, YAML files are great for debugging. Just kidding, they're a nightmare.
Why not use PHP config? Same gibberish.

Like Laravel, Symfony relies heavily on "magic." As long as it runs, fine. But when you need to debug this, you're doomed.

![Image description](horror-octopus.webp)

You must dig into framework internals to guess where the error is. Why do I need to know how a car engine works in detail when I just want to drive it?

The last straw before deleting it:
Pass an array; Magic complains it wants an array. Spend hours debugging to find out service.yaml expected a different type hint three layers deep.

I feel sorry for people forced to use this overhyped crap. Structure is important, but when technology constantly blocks me because its creators insist "it has to be this way," it goes straight to the trash.

Especially Laravel and Symfony share one more delusion: proprietary template languages.

## Stan & Olli - The Template Heroes
Let's call them Twig and Blade. Could it be that some people haven't understood "separation of concerns"?

PHP started as a template language. When you need logic in templates, why the hell would you invent a new syntax?

"But context-aware escaping!" Yeah, I hear you. Wrapping output in htmlentities must be unbearable for developers who just configured 500 lines of YAML.

**The irony:** These frameworks preach "don't reinvent the wheel," then force you to learn proprietary template syntax instead of using PHP—the language you're already writing your application in.

Twig has filters, Blade has directives, and both compile back to PHP anyway. It's abstraction for abstraction's sake, solving a problem that doesn't exist.

If you actually want separation, use a truly logic-less template engine. One that enforces separation by removing the temptation entirely.

But we'll get to that.

## Why Frameworks Are Wasting Your Time
According to OOP principles, a framework should be a perfect black box.
In practice, this fails due to two factors: abstraction leaks and inversion of control.

### Abstraction Leaks
Every abstraction is "leaky"—internal implementation details bleed through the surface.

**Example database (Doctrine in Symfony):**
You use objects (OOP), but internally SQL queries are generated. If you don't know how the framework handles "lazy loading," your code suddenly fires 100 database queries instead of one.

Your code follows OOP rules perfectly, but the application crashes.

This defeats the entire purpose of a "helpful" abstraction. You need to understand how it works internally to use it correctly.

![Image description](steampunk-operator.webp)

### Inversion of Control
With a library, you call the code. With a framework like Symfony or  Laravel the framework calls your code.

For Symfony to know when to call your code, you need to understand the lifecycle—the kernel request/response cycle, service container initialization, and event dispatchers.

Without this knowledge, you're fighting against the framework instead of using it.

Because what developers really need is a framework nanny telling them what's right or wrong, right?

### The Magic
Frameworks rely heavily on "magic": reflection, annotations, attributes, and auto-wiring.

When something breaks, you get 50 lines of framework stack trace and zero clues about your actual code.
If you don't understand those internals, you can't fix the error—especially when it's thrown four or five layers deep in framework code you didn't write.

**Here's the uncomfortable truth:** developers flock to frameworks because they can't organize code themselves. Instead of learning architecture and best practices, they outsource decisions to a framework dictator.

Learn to structure your code. Learn design patterns. Learn when to use libraries vs. frameworks.
That knowledge transfers across projects and languages—framework-specific knowledge doesn't.

## Slim, Composer-Libs, and Mustache
A batteries-included framework is just someone's opinionated corset labeled "best practices"—plus a collection of curated Composer libs.

I didn't want that. So I went looking and found it.

### SLIM - The Hummingbird
Small, fast, efficient! Exactly what I needed. At its core, it's just a router built on PSR interfaces.

PSR is what makes it really fascinating, not the performance. There are faster alternatives like [Leaf](https://leafphp.dev/), [F3](https://fatfreeframework.com/3.9/home), or the mostly unknown but blazing fast [Kumbia](https://github.com/KumbiaPHP).

#### PSR
Choose a PSR-11-compatible dependency injection container? You can swap it for another PSR-11 container without rewriting your app.

Same with PSR-15 middleware or PSR-7 message interfaces.

Unlike Laravel's Eloquent or Symfony's kernel, PSR interfaces give you actual flexibility. No other micro-framework leverages standards this heavily.

#### Composer Libs
If you don't want to reinvent the wheel, you need third-party libraries.

I picked wisely—poor library choices are another path to vendor lock-in. Therefore, I chose small libs with minimal dependencies, and only when those dependencies are actively maintained.

In the end, **I decide** what I use and when to update; not the framework maintainers.

For multilingual support and configuration, I needed something lightweight but found only overengineered monsters. So I wrote my own.

#### Mustache
Remember when I said I'd get to truly logic-less templates? Here we are.

Mustache works across 60+ programming languages. The syntax is minimal: {{variable}}, {{#section}}, done.

The "logic-less" approach enforces clean separation: business logic stays in code, not templates. This makes templates maintainable, testable, and secure against XSS attacks by default.

Abstraction is useful where it provides security—not where it kills performance and adds complexity, like ORMs.

![Image description](it-hummingbird.webp)

## Final Words
Big frameworks promise productivity. They deliver dependency hell and cognitive overhead.

I spent 20 years building my own framework. When I finally tried to "do it right" with modern tools, I discovered most popular frameworks are overengineered and optimized for developers who can’t or don’t want to structure their own architecture.

For Garlic-Hub, I needed software that works even on small hardware like a Raspberry Pi, not just cloud servers with infinite RAM.

SLIM + curated libs delivered what Laravel and Symfony couldn't: simplicity, performance, and control.

No Eloquent slowing down my database. No Symfony throwing tantrums over YAML configs. No proprietary template languages. Just clean, boring code that does exactly what I need.

I can't add more RAM to an IoT device. I needed lean code that runs efficiently on hardware where every megabyte counts. Exactly the kind of efficiency modern frameworks abandoned years ago in favor of "developer experience" and bloat.

And here's the kicker: lean code scales better. Fewer resources per request means more requests per server. Laravel might work at scale if you throw enough money at AWS. But why pay for bloat when you can build efficiently from the start?

Think smaller. Build leaner. Choose standards over framework religion.

Garlic-Hub is open source: [github.com/garlic-signage/garlic-hub](https://github.com/garlic-signage/garlic-hub)