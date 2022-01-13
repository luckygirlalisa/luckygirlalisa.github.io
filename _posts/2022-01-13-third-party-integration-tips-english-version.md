---
layout: post
title: "Give Software Engineers a Better Life -- Precautions of Third Party Integration"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## Fear of the unknown

<div style="text-align:center"><img src ="/assets/images/fear.jpeg" style="height:200px;" /></div>

From time to time, team members in software delivery teams are heard complaining about third-party systems integrations:
> "The related functionality from the XXX team(third party) has not been implemented yet, so I can't test this story card..."

> "Why are their APIs reporting the same errors again?!"

> "They didn't notify us that they changed the API, which is the root cause of this pop-up problem on our side..."

> "The third party system doesn't support this function, so we can't do it.."

> "The functionality from the third party system is broken again, causing our showcase failed.."

There are many complaints like this, so colleagues with less experience or context see a story card that needs to do integration with this third party, will be afraid to take it, or even hesitate to gather more information about it. The demonization of the third party has been formed silently in this way. .

This phenomenon is not an isolated case, and third-party integration is something worth paying attention to and dealing with for a project. After all, the third party is not under the control of this project, and most of the members are lack of context of the initial design and subsequent changes of third party system. 

Humans have an innate fear of the unknown. In a previous business opportunity, customers focused on whether we had the experience and ability to integrate with a large number of third parties. It is estimated that they also suffered a lot from third-party integration.

## Unavoidable Integration


<div style="text-align:center"><img src ="/assets/images/walk-around.jpeg" style="height:200px;" /></div>

However, such integration is inevitable for almost every project even though it's not an easy thing. Some students even joked that they were in a third-party integration company, specializing in third-party integration work.

As a result, various services are flying all over the sky today, and the "lazy" of programmers has prompted endless reusable wheels, so that almost every system inevitably uses existing services. Repeatedly building wheels is time-consuming and time-consuming. Spent the money of the master. For example, integrate mature commercial payment services, single sign-on
land system, etc.

Secondly, a large system is usually completed by multiple different vendors and different teams. Even a large team will be split into multiple small teams. As a result, integration with services that other teams are developing or already live in is also essential.

Furthermore, the services we develop may also be third parties for other projects.

## How to ensure smooth integration

-- The history of blood and tears that the third party was found unavailable on the day of launch


Since there is such an inevitable third-party integration work, which even accounts for a large part of the entire project workload, how can we make the integration work less tiring, let the team have the confidence to complete the related work, and try not to appear in an ad-hoc state. Woolen cloth?

We look at the two types of third-party systems, mature online services and services under development.


### Integrate with launched services

<div style="text-align:center"><img src ="/assets/images/on-live-system.png" style="height:150px;" /></div>

If the integration is a service that has been launched and the version is basically stable, it is relatively easier to handle. Common such third-party systems include auth system and payment system.

#### Fully understand the responsibilities, architecture and infra of this service, how to use this service

As a user, you need to clearly know what services are mainly provided by each third-party integrated, so that you can know which functions need to be covered by our system and which systems do not need to be covered when designing the part developed by yourself.

It can be viewed from two perspectives: behavior and data.
- Behaviorally, what kind of services does this third-party system provide? For example, the auth system provides registration, login, and authority management services.
- In terms of data, what data is mainly managed by this third-party system, which data is subject to the third-party system, which data is auxiliary data, and what is the data flow. For example, in the auth system, the user name, password, profile information, and authority management data obtained during registration are all obtained independently from the auth system, and are obtained from the auth system's own page, subject to the auth system. If we, as a consumer, need to read the user's profile information, we should get it from the auth system, whether it is real-time or through batch or cache, and we should not store another set by ourselves, resulting in a series of problems caused by inconsistent data. If the consumer needs to modify the host information of the auth system, it needs to be synchronized to the auth system.

Usually, the scope of services of third-party services is difficult to understand very clearly, especially the details. When it is not possible to understand all the services provided by the third party in a short time, how to design a solution to meet the needs depends on the understanding of the third party's architecture. For example, an auth system
Divided into SP, OP, OOB, responsible for XXX respectively, so you can basically understand the scope of responsibility of this system. Such architectural information generally exists in existing documents for such mature online systems.

As for how to use this service, it is necessary to have an up-to-date document that matches the service. If it is a restful API service provided, try to collect API documents. For example, swagger documents are very important for the subsequent development stage. (Word documents are also available, at least there are documents, but it is very likely that they are not updated synchronously with the system. This requires emphasising the importance of updating documents and notifying changes in advance when contacting third parties.)


#### Test environment and online environment that need to be prepared in advance

"Working code trumps detailed documentation". Therefore, we need a third party to provide a test environment for debugging to ensure that the service functions work properly. Ideally, each environment required by our system corresponds to a different environment of a third party, and the standard dev, qa, uat and prod correspond to each other.
a third-party environment.

But in many cases, the third-party service only provides a test environment (like production environment), and the dev, qa, and uat of our system share a test environment. This will lead to the mutual influence of data between different environments. For example, if a third party logs in to the system, the account created in the high environment will be affected by the configuration of the low environment.
The impact of setting changes may lead to accidents at important moments such as PO signoff and showcase. The impacts of other environmental public resources are numerous and will not be repeated here.

> In the case that the third party has "no way" to provide more test environments, it is necessary to generate a fake third party, such as Mockpass corresponding to Singpass, and API service (moco) generated by contract file.

> Of course, if the third party is a very complex system and cannot fake a system with the same function, only try to strive for more test environments
- For example, when a paid service needs to be bound to a third-party service such as a real bank card, it is relatively troublesome. It is said that the most painful point for a certain project to go online is that the third party does not provide any test environment, and the function is not available until it is online. I know, this can only turn the boy's head in vain, and think of other ways. Compare
Such as continuous communication with third parties and their superiors (break up), let them provide a test environment.
- If you can neither mock the integrated service nor apply for more test environments, you can only defend, so that the entire team is aware of the use of the third-party environment and the possible consequences. For example, after a tester has made some changes in the QA environment, he may receive multiple notification emails from a third-party system about the operation.

#### The impact of third-party maintenance on us
A stable third party may perform maintenance on a regular basis or from time to time. This planned downtime can only be solved by early planning, notification, and timely communication. The maintenance time avoids important time periods, such as showcase, online, etc. Also applies to third-party incompatible upgrades, > or configuration file changes, etc.


### Integration with under development services


<div style="text-align:center"><img src="/assets/images/in-progress-system.jpeg" style="height:200px;" /></div>
If it is to integrate with a third-party system that is under development, or even just beginning to design, it takes more effort, and even needs to help them from design to implementation, to continuous integration and release, and sometimes to help them plan. Maybe it's as a consulting firm solving client problems rather than an outsourcing firm
reflect the value. (Though tiring doing things outside of the purview.)

#### Design

<div style="text-align:center"><img src="/assets/images/design.png" style="height:200px;" /></div>
In terms of design, first set the general direction from the positioning of responsibilities, and what kind of services are mainly provided by the third-party system. For example, the third party that has recently been integrated into the project is primarily responsible for booking services, regardless of the resource being booked.

The system is still viewed from both business capabilities and data perspectives.

> In terms of business capabilities, it is natural to provide the scheduled addition, deletion, and modification services, as well as the corresponding resource establishment, and the authority control part of the resource operation.

> In terms of data, because positioning is a general reservation service, special resource information including business scenarios cannot and should not be owned and managed by this third-party system. This information should only be hosted by our system and transmitted to a third party when it needs to be displayed or represented by a third party. resemblance
Yes, the systems we are responsible for should not store any booking-related information, and every time booking information is used, a third party
Request, take the predetermined data of the third-party system as the source of truth. If there is a need to modify the reservation information from our system, it can only be completed through the interface provided by a third party, or through data synchronization.

The design-to-implementation transition stage needs to be refined to the API design level (design at this level may be done continuously during concurrent development).
- Including the identity authentication method, whether it is authenticated by the SSO of the user logging in to the system, or whether the trust relationship is established between our server and the other party's server. After the trust relationship is established between the servers, what is the resource level that can communicate, and all resources have permission to communicate , or chunked resources are communicable.
- What is the input information to us including the happy path, what is the output information, and the status of the sad path is determined.
- There is also the most important, which fields are used to uniquely identify a certain resource, whether the sequence of such information flow in the entire user flow is logical, and whether each information exchange node has a necessary source of information input.

It is also crucial to pull through what kind of change is breaking change, and what kind of change is acceptable change that does not affect the integration function. Teams often find that third parties modify the API every now and then without notice, rendering functionality unusable. At this time, it is not necessary that the blame third party has changed at the first time. After all, the function of any system or service is constantly evolving, and it is necessary to check whether it is a change that affects the corresponding function. If it is a breaking change, such as There are too many required fields in the API request, and it is indeed necessary to be held accountable to modify it. However, if there are only a few optional fields in the API response, which leads to our functional problems, it means that our code is not robust. For the breaking change of the API, this article is very detailed: https://bambielli.com/til/2018-01-12-what-is-a-breaking-change/.

Although it may take a lot of time to pull through the design information (for example, the initial draft of our recent project took about a month, and it will continue to be carried out later), but we must take the trouble to pull through and leave the results after the pull through. The reference for the implementation of the landing is also used as a follow-up integration question.
The responsible party at the time of the question corroborates.

#### Implementation

<div style="text-align:center"><img src="/assets/images/implementation.jpeg" style="height:200px;" /></div>
When the design framework is basically completed and details start to be implemented, it should be relatively smooth, but there may still be a functional "time difference". The functions that we expect to be completed at a certain time node cannot be completed on time, resulting in failure to integrate and test normally as planned, so that a series of related tasks cannot be moved to
Complete the column.

##### Timeline

At this point, you need to plan and communicate a good time line, about what kind of major functions will be completed at what time, and check regularly. Some people will say that we are agile, we want to embrace change, but agile does not mean no plan. Because of the development sequence and the lack of target time, the mutual block work is not the result that agile expects to see.

##### Real-time communication

In addition to communicating their progress regularly, a real-time communication channel is also needed. Various problems will occur in the implementation stage, such as data preparation, environment failure, incomplete functions, and customer dissatisfaction, all require real-time communication. Find the person who corresponds to the other party and solve it in time. Facts have proved that "individuals and >interactions are higher than processes and tools" is the truth, and processes are also very important. The sense of ritual can bring pressure and seriousness, and improve the efficiency of "contract performance"; individuals and interactions that match it are important for daily problem solving. Crucially, finding the right people and getting a prompt response is the key to reducing the pain of 3rd party integration >.

##### Key time points

In addition, serious communication and confirmation of key time nodes is also an indispensable part, which can greatly reduce the length of hair grabbing. For example, each boss will have a showcase, and it is necessary to confirm and communicate with the third party to maintain the stability of the corresponding environment during this time period -- do not deploy new code, do not
Downtime maintenance, do not modify the configuration file, do not modify the corresponding test data and so on.

The launch plan also needs to be communicated well in advance, including the launch time point, the functions that need to be completed and stabilized by a third party, the data that needs to be initialized, the scope and time of various tests that need to be completed before the launch, and so on. When you meet a professional third-party team, you can clear the timeline and the support you need.
Rest assured, but you cannot avoid third-party teams with low (unreliable) test coverage. Therefore, at least one or two weeks before the launch, you should continue to pay attention to the status of the third-party system. If you find a problem, you should raise it in time and make it a high priority to fix it.

##### "Surprise"

Everything is there, but there may still be "surprises". The day before the launch, it was confirmed that the third-party integration points were all working normally, but it also happened that the same function did not work on the day of launch. At this time, life may be doubted, but sufficient logs can help us find the problem quickly, and notify in advance > ask the standby third-party to locate and fix the problem in advance. Although this process has caused the team's atmosphere to decline, it is also enough to prove our professionalism in front of customers.

##### Partnership -- radish and stick parallel

Finally, although there may be a series of stumbling blocks in the process of collaborating with the third-party team, identify who is responsible for which part of their team and what their abilities are. When a small problem occurs, they will notify the repair in a polite and instructive manner, and when a serious problem occurs, they will be seriously and Politely state facts and influence, with occasional praise
One candy can still dramatically make the collaboration smoother and smoother. The so-called "stay on the front line, and see each other well in the future", which is why I still maintain an attitude of not angry and rational communication when the team complains about the third party. However, occasionally let their management know that they are not
Poverty issues can also work well. Solving technical problems by non-technical means is reflected in third-party integration (management).

## Finally

When I thought of the previous pre-sale, I learned that the only technical requirement of customers is to have experience in integrating with a large number of third parties. Now that various services are exploding, not only can they be smoothly relied on, which is one of the criteria for the success of these services. And being able to use and integrate existing services smoothly is also a great benefit to programmers and
The test standard of software company's ability.

Do you have any tips and/or blood and tears for third-party integration? Let's hear it~
