---
layout: post
title:  "Mistakes and Learnings."
date:   2017-08-07 20:00:01 +0530
display_date: "Aug 07, 2017"
categories: Software-Engg
permalink: "/mistakes"
---

It’s been over a year since I have started working at my current company. There have been lots of ups and downs. I know that’s a cliched thing to say, but it’s true. In this post, I have decided to really introspect over some of the mistakes I have made during this one year, and try to find some insight that may help, and the people reading, for the future.

For some background, I have been working as a Backend Developer at my current company, mainly developing web services in Python and PHP to handle various operations for our [hotel room booking product](app.selectrooms.co). Although I have worked previously as a database programmer before, this was really a sort of new phase for me. It was the first time I have been handed the responsibility of working on a system (although small) as a whole rather working on one of the many parts of a huge system. Modelling the database, figuring out the appropriate scheme of authentication, experimenting with various tools to solve a problem, learning different frameworks and languages, understanding an existing codebase, and deciding on deployment strategy. Looking back, the past year has been an extremely fulfilling one in terms of my professional growth. But much of that learning came from making many mistakes, some of them multiple times. I will talk about some of them.

I don’t know if this true for everyone, but I have had my fair share of the straight out ‘that was dumb’ mistakes. I can attribute these to brain farts, move on and never bother about them. But I think that would be a missed learning opportunity for me. 

Configuring different environments before starting to write code, is the most important lesson I have learned and am still learning. 

Truncating the table in prod schema instead of dev (aws backups came to rescue!), configuring wrong the database to the wrong environment and breaking head figuring out why requests were failing (this happened multiple times), setting a cron job on server that gets shut down after working hours (this was very embarrassing). A common factor in all of them is the lack of sufficient planning before execution.

As I started learning from my mistakes, I started writing things down and making clear action points before executing even a single command. This is something a lot people know and recognise by default, but I learnt it the hard way. [The Checklist Manifesto](https://www.amazon.com/Checklist-Manifesto-How-Things-Right/dp/0312430000/ref=sr_1_1?ie=UTF8&qid=1504723159&sr=8-1&keywords=The+Checklist+Manifesto) talks about this habit of having checklists for critical situations, I started having one before every deployment where multiple things could go wrong, apart from automating as much as possible.

 
 
 
There were some other instances, when I was having a recurring problem in my code. It can be an environment issue (os or database), things related to the language (Python in my case), related to the design (being unaware of anti-patterns) etc. 



I have felt lot of inertia in transitioning from a beginner level to an intermediate level. There are certain things I have gotten used to, like handling the logic in a particular way that is very primitive in terms of programming standards. It took some effort to break this inertia. Working in a tiny team can be tricky in terms of not having the luxury to learn from other developers. Tasked with handling the whole backend system, I would often sacrifice code quality over getting it to work. 

I have eventually realised the need for adhering to established patterns. But the real struggle has been to overcome the ignorance of such patterns. *When you do not how to define your problem, you can not find the right solution.* This book on [Design Patterns](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=sr_1_1?ie=UTF8&qid=1504723873&sr=8-1&keywords=design+patterns+gang+of+four) has turned to be just the right answer for my problem. Even though not all things in the book are applicable in my daily work, just by having the awareness of their existence has given me a great confidence. 

 
I would love to hear about your self improvement techniques, specially if you are a self learned developer working in a small team like me.
