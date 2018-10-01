---
layout: post
title:  "Managing Python Dependencies The Right Way."
date:   2018-10-01 12:00:01 +0530
display_date: "Oct 01, 2018"
categories: Software-Engg
permalink: "/python-dependencies"
tags: python, software-engineering
---

I wanted to write a blog post about something I learned this week. We use a standard set of open source packages in our projects. Almost all of them are pretty stable and cause no problems, but on some rare occasions, more rare than in the case of code I write, there’s a bug or two that gets pushed into PyPi. Or it may not even be a bug, like a newer version that stops supporting Python 2. 

So in this particular instance, our project was using the django-scarface package, that helps us sending those SNS messages pretty easily. The mistake we did, was not pinning down the version in the requirements.txt file. 

```
. . 
django-scarface
. .
``` 

The reason why I want to talk about this is to demonstarte how such a mistake can sneak upon you in production. The way the python packaging manager PIP works is that whenever you do not specify a version along with the package name, it always installs the latest version of the package. This part is probably expected by everyone. But if the package is already installed and you don’t specify a version, it will do nothing even if there is a newer version available.

So here goes a scenario that will make this bug unnoticed until very late:
- Create a new project in local -> PIP install -> All installed & deployed perfectly -> Awesome.
- After sometime you deploy to a Dev/QA/Staging server -> PIP install -> All setup perfectly here too -> Great.
- You have shippable product in you hand. Let’s go LIVE! -> PIP install -> Code is deployed -> It’s LIVE folks!
- Now if your production setup runs on a single server that is getting vertically scaled up whenever needed, you may not notice the bug unless you’ll have migrate to another server for some reason. But in a lot of cases high traffic is handled through horizontally scaling the systems by having a trigger configured to spawn or take down systems accordingly. That means a new system will always need a fresh install of the whole project, including the dependencies. Now when django-scarface dropped support for Python 2.7 in their 3.2 release, the very next autoscaling trigger on you production setup will result in a pretty ugly error like the following.
```
IOError: [Errno 2] No such file or directory: '/tmp/pip-install-ji4l4h/django-scarface/django_scarface/__init__.py’
```

So all we need to do is specify the stable versions of all the packages we use as dependencies and this problem will never occur. And you will never have to bother about ever again. Right? Not really. 

These awesome OSS packages that we use to make our lives simple, can also make the same sort of mistakes that we do. They too can overlook the need to pin down their dependencies, and in that case you will run into the same problem as above or something even worse. An example of this is when Kombu, a messaging library used by Celery, released their 4.2 version that broke Celery. Celery which was careful in not leaving out a version range for Kombu in their `requirements.txt`, still had to face the issue as their `requirements.txt` read something like `kombu>=4.0.2,<5.0`. 


How do we detect breakages like these? A CI/CD pipeline that builds and runs tests can help us in detecting PIP installation errors in some cases. The catch here is that we CI/CD pipelines get triggered only when new code is pushed. Also in the case of Celery the installation did not raise any errors, it was only when Celery service was trying to boot up it threw an error. Having an error logging service like Sentry can help in these cases. Monitoring services that run detailed health checks like executing a critical part of the system and ensuring everything works as expected, is another way to deal with this. These practices are well recommended to handle production outages/errors in general, and they'll also work for our case.


That's it! That's all I have to say. The younger more naive python programmer would have trouble paying attention to such detail. But I’m lot wiser now. I have witnessed some really ugly stack traces in Sentry. Stack traces can be helpful in debugging, but they also can be revealing of how horrible results can some of the seeming harmless bugs can lead to. And they will force you to correct your shit. 😂
