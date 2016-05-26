---
layout: post
title:  "In Defense of the Cascade: Scalable CSS"
subtitle: "The future of CSS is JavaScript"
date:   2016-05-25 20:46:21
---

*The future of CSS is JavaScript*

CSS at scale is a relatively new problem. So far it’s a problem we’ve sucked at solving. I think it’s because we’re approaching it the way we would approach scaling *an actual programming language (#hotdrama)*.

Scaling CSS is a technical problem, but more than that it’s an architecture problem. I don’t think you’ll find its solutions in code, and I don’t think its solution requires new tools or specs. This is a problem we need to solve at a higher level — we need to solve it with design. The reason we fight the cascade, the reason we struggle to solve front-end scale problems, is that we don’t design with scalability in mind.

And also we write bad CSS.

## Don’t Repeat Yourself

Don’t Repeat Yourself. DRY. It’s one of the most fundamental principles of programming. “Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.” It makes sense, and is generally a good rule to follow. In traditional programming it reduces the likelihood of bugs, eases the process of fixing bugs that do happen, and generally makes maintenance easier.

If we try to apply these same principles to front-end styling, it’s not difficult to find the flaws. There are far more unpredictable subtleties to the front-end, it’s far more “alive.” Two components that seem to share the same design may quickly diverge as a site ages. Business needs change, marketing strategies change, user needs change, and it’s entirely possible that these changes don’t affect our two components in the same way.

A lot of solutions to the scaling problem offer OO principles that tie styling directly to markup, and you end up with class lists like <code>section-heading no-margin separated</code>. This certainly makes things more DRY, but it’s impractical in many applications. Making subtle changes is difficult, making modifications to all instances of a component (without affecting others) is laborious, and forget making it work with CMS-authored content. Coupling markup so tightly to appearance also raises concerns for the longevity of the markup.

## Cascading Style Sheets

This is a little bit of tangent, but let’s talk about the cascade for a second. I feel it’s greatly misunderstood.

The cascade can be a scary thing. If you don’t understand it you can easily be caught chasing phantom styles. We’ve all been bitten making a change to one component and having it affect others in unexpected ways. Lots of design patterns have attempted to make the cascade a little less vulnerable — OOCSS, BEM, SMACSS, the list goes on. How well they work depends on how well you use them, but they offer a great starting point.

But the cascade is not evil. It’s not out to get you. There are some really beautiful subtleties to the cascade. Stop hating it, and learn how to love it. *Understand it*. Rather than try to fight it, use it as a tool to help you achieve your dreams. Algorithms are hard too, but no one is going around saying “algorithms are stupid just use JavaScript.”

I want to bring this up mostly because what I’m about to propose is going to rely heavily on isolating styles. This doesn’t mean cascading isn’t important. We’re going to use the cascade for things that are *absolutely global*. Typefaces, font sizes, things that — if they change — you want them to change everywhere. Also I just want people to stop bitching about cascading. It’s not a big deal.

## Designing For Scalable Success

Scaling anything is about consistency and redundancy. Where those things come from is usually pretty clear. I believe that the reason we have so much difficulty scaling CSS is that we’re looking at the code, and we’re seeing code solutions. We need to look at design.

Looking at a design and thinking “that’s going to cause CSS scaling issues” may not be a thought that has ever danced across your mind. It certainly has never crossed your marketing director’s mind. But, even if just for the sake of argument, let’s assume this is the source of all of our Scalable Style Sheet problems.

That marketing director thing is important, by the way. This approach puts some restrictions on flexibility for the sake of consistency. Learn how to say no if you have trouble with that. It’s pretty easy, it’s only one syllable.

The idea is to create a component-based design system that focuses on future-proofing those components. How you break those components down is up to you, but the goal is to create chunks of *stuff* that exist on their own, isolated from all the other chunks, and to use them consistently. Minor variations are a big deal, and should not be made lightly. This is the bedrock of our front-end, and how reusable and stable every part of our web project is depends on how much care we take in this part of the process.

I highly recommend Brad Frost’s [Atomic Design](http://atomicdesign.bradfrost.com/) as a starting point for this. I won’t go into a ton of detail here, because that book says everything so perfectly I could not do it justice. Go read it, ask your designers to read it, and then come back here so we can talk about CSS some more.

## Do Repeat Yourself

Now it’s time to start building the site. The first thing to do is identify some pieces of your puzzle. How this breaks down depends on your approach to our atomic design principles, but essentially we’re going to have two pieces: systemic styles and component styles.

Systemic things are where you leverage the cascade. These are things that will always be global. Your base font size, your default link color, your typeface. Things that, if you change them, you want those changes to affect the entire system. This is where the cascade excels. Go forth and cascade, my friend.

Components, on the other hand, are atomic, isolated. If you make a change to a component it should affect that component, extensions of that component, and absolutely nothing else. With traditional OO approaches to scalable CSS you don’t necessarily get this guarantee. This, this is why I want you to repeat yourself. The promo component and the event component have the same heading style? Style that MFer twice. I don’t care what your computer science office-mate says. Repeat yourself, and never again worry that changing the event is going make your promo go all wonky.

<pre title="style.css"><code class="language-css">
.promo {
    margin: 1em auto;
    padding: 1em;

    .promo_heading {
        font-size: 2em;
        font-weight: bold;
    }
}
.event {
    margin: 1em auto;
    padding: 1em;

    .promo_heading {
        font-size: 2em;
        font-weight: bold;
    }
}
</code></pre>

See how absolutely identical those are? That’s exactly what I want you to do. And I want you to feel good when you do it.
Isolating components this way also solves another problem. Another major challenge to scaling CSS is *humans*, possibly the only thing scarier than the cascade. When you have multiple teams working on a single web asset, it becomes very difficult to synchronize changes between teams. By isolating components each team is responsible for their set of components, and they don’t have to worry about affecting other teams’ components. Only systemic changes require synchronicity between teams.

***Yea but performance***. Your server has gzip. Make sure it’s turned on. Do you know how good gzip is at compressing CSS? *Insanely good*. If you’re concerned about file size go talk to your JavaScript developer, or your content authors with their huge images.

If my pleading does not leave you convinced, I would suggest you go back and read Atomic Design again, and then read Ben Frain’s lovely [Enduring CSS](http://ecss.io/). If you think we’re all crazy and that there are flaws in HTML or CSS, please get yourself involved with the appropriate working groups and help write some specs. It’s not glamorous work, it’s tedious and difficult, but it’s crucial to the evolution of the web.

And if you’re just afraid of actually building things, there are plenty of Medium articles about which JavaScript framework you shouldn’t use.

And remember, the cascade is more afraid of you than you are of the cascade.
