---
layout: post
title:  "Observations on API Design."
date:   2018-03-22 20:00:01 +0530
display_date: "Mar 22, 2018"
categories: Software-Engg
permalink: "/api-design"
tags: api-design, python, software-engineering
---


There are a lot of resources regarding designing good APIs. I personally found [the talk from Joshua Bloch](https://www.youtube.com/watch?v=aAb7hSCtvGw) to be highly informative.  I can also add Martin Fowler's articles and talks, along the with highly recommended book "Design Patterns" by Erich Gamma et al., as good sources to refer when it comes to designing software applications. In this blog post, I will try to distill their wisdom and mix it with my experience. My work has been mainly building web services, and I am going to use API as a placeholder for HTTP API in the context of this blog, but it can also be applicable to any kind of API.

I am going to borrow some of the key points from Jasmin Blanchette's [The Little Manual of API Desing](https://issuu.com/madman1969/docs/the_little_manual_of_api_design) about how to approach designing APIs.

---

### Easy to learn and memorize.
A good naming convention can have a huge impact on how easy it can get to consume an API. Following a consistent naming convention can make the API simple to use. I once built something like the following endpoints for fetching two different kinds of hotels. 
```
api/XHotels/
api/XHotel/


api/search/Y/?method=list
api/search/Y/?method=detail
```
The two are essentially fetching hotels from two different providers, and the reason they have such arbitrary URLs is that they were built without realizing how are they going to be used. The API user shouldn't be bothered to memorize two different patterns of URLs for essentially receiving an identical list in either case.


### Adaptable to change. 
 
Over-reliance of design on implementation details makes it difficult to add/remove logic. 

Software requirements always tend to be fluid in nature, especially if you are working on customer facing applications. A good software design must consider the "ease of change" when it comes to evaluating the design. Bad design will cause the software to break when new changes are introduced.

One example of how bad design that doesn't take future changes into account is how the implementation details of the API dictates how an API should or should not be used. This is problematic because if the implementation has to be changed in the future so does the design.

Eg: Writing a function that behaves differently based on the number of arguments being passed. I have myself in the past made this design choice while writing the interface to an API. Letting the interface be dictated too much by the implementation of the feature makes the API fragile. 

Let us say if we have two kinds of customer, Premium and Basic. Now observe the following piece of code. 

    def create_organisation_profile(**kwargs):
        if 'registration' in kwargs:
            model = PremiumUser
        else:
            model = BasicUser
        ...

Making the logic depend on whether the input data contains a particular element makes the code a poor fit for future additions/removals. It is much more efficient to have an extra field passed to function/API to distinguish which logic to execute. Deciding between booleans v/s enums becomes important in this case, depends on whether our use case can have more than two variations or not, i.e. predicting accurately whether there can only be 2 of a kind. A much more durable way of writing the above logic would be the following -
    
    def create_organisation_profile(**kwargs):
        if kwargs['type'] == PREMIUM:
            model = PremiumUser
        elif kwargs['type'] == BASIC:
            model = BasicUser
        ...

where more `elif` conditionals can be added to handle any future customer types.

### Separating out code that interacts with different layers.

I have found that having the source code separated into different logical layers is extremely helpful in both the development and maintenance of the application. In other words following an MVC/MVP/MTV pattern. The aforementioned patters get implemented by creating separate files for models, controllers, and views. But what gets overlooked or forgotten during the development process is to deliberately use this separation to decide where your code should be written. In other words, asking the question - 'Where should this logic stay?' - every time you write a piece of code. Another thing one can often miss is, to stop after creating these 3 files. A better, cleaner project structure can be attained by having a further classification of the logic written (or the files created).
    
    def register_user(request):
        . . .
        user = User.objects.create(username=username,
            email=email,
            password=password,
            first_name=first_name,
            last_name=last_name)
        
        email_text = email_text.replace('#FIRST_NAME',user.first_name)
        
        notification = Noitification.objects.create(user=user,
           notification_type=SIGN_UP)
        notification.send(text=text)
        

As you can see from above, having all your operational logic within a single function even though being bug proof is a bad idea. One of the key moments of transforming out of a novice developer is not falling into the trap of thinking “what’s wrong with this, it works perfectly”. A more seasoned developer would put it in a slightly different manner - “it works perfectly NOW, BUT what can go wrong”
Another use of having code separation is it enables the code to be DRY, i.e. if we have a method/function that has our authentication logic, we can reuse it without rewriting the code everywhere.

 

### Easy to read is easy to debug. 
Design patterns are useful tools in approaching a problem. They help us in thinking in terms of reusability, isolation, refactorability etc. On an average day, I am usually writing my logic on top of an already well-designed framework, which has all the right kind of patterns baked in. But once in a while, I encounter a problem, which forces me to customize the out-of-the-box solutions provided. This is when design patterns help. Using them judiciously wherever needed/applicable can immensely improve code readability and maintainability.

> Application code is written only once but read over and over again by different developers over the lifetime of the application. Readable code is easier to document and maintain. It is also less likely to contain bugs because the bugs are made more visible by the code’s readability. (The Little Manual of API Design)

I think everyone will come across the readability problem at one point or another. But I'd like to emphasize how design patterns can make the code not only more robust but also much more readable and maintainable.

`if` or `switch` statements are inevitable when it comes to implementing business rules. *If* your business rules are too complex, you might end up with something looking like this -

```
...

if payment.customer in discounted_customers:
    # custom flow for premium customers

if payment.get_pending_amount() > 0:
    # flow for handling pending payments
else:
    # usual flow 

...

```

Using a slightly customized Strategy Pattern can help avoid so many possible issues that may arise with the above code.

```
# strategy.py
def get_customer_strategy(payment):
    if payment.customer.type == PREMIUM:
        return process_premium_customer
    return process_free_customer

def get_payment_strategy(payment):
    if payment.get_pending_amount() > 0:
        return remind_pending_amounts
    elif payment.get_pending_amount() == 0:
        return process_order
```
Splitting up the conditionals like this will automatically improve readability just with the mere presence of function names which are descriptive and small enough by themselves to digest the logic.


### Find the right balance between generalizing and specializing the scope of each aspect of API - when in doubt, leave it out
Joshua Bloch brings talks about this in his talk about API design. 
It is advised not to overgeneralize the API use cases. Bloch uses libraries as an example of APIs in his talk, but this can also be applied to Web Services. Making an endpoint perform too many actions per request can lead to a host of debugging problems. 
    
    def post(request):
        ...
        create_event(**data)
        create_attendee_types(**types)
        create_attendees(attendee_list)
        ...

A good example is creating parent and child records from the same API call. This leads to failures caused by having too many dependencies and can mess up the state of the application. 

### Writing testable code.
Lastly, testing also plays an important part in designing APIs. A well-written test suite can catch design loopholes and bugs early in a development process, and more.
- Helps in regression testing after every bug fix, refactor or feature addition.
- Crystallises the requirements - what is the code expected to do? 
- Makes you think whether you’ve actually solved the problem.
- Forces you to make better design decisions.
- Gives you a measure of quality and progress.
- Catches bugs early.
- Helps in knowledge transfer. 
- Makes your code readable and easy to navigate.

> There’s no secret to writing tests .. there are only secrets to writing testable code. - Misko Hevery of Google

Writing testable code is an art. But thankfully writing testable code overlaps with designing good APIs/software. 




