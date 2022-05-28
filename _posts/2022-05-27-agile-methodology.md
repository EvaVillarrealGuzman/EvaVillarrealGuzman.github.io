---
title: Agile Methodology
layout: post
subtitle: For some types of software, such as safety-critical control systems, where a complete analysis of the system is essential, a plan-driven approach is the right one. However, in a fast-moving business environment, this can cause real problems. By the time the software is available for use, the original reason for its procurement may have changed so radically that the software is effectively useless. Therefore, for business systems in particular, development processes that focus on rapid software development and delivery are essential.
categories: methodology
tags: [agile methodology, scrum, kanban, extreme programming]
---

Businesses now operate in a global, rapidly changing environment. They have to respond to new opportunities and markets, changing economic conditions, and the
emergence of competing products and services. Software is part of almost all business operations so new software is developed quickly to take advantage of new
opportunities and to respond to competitive pressure. Rapid development and delivery is therefore now often the most critical requirement for software systems. In fact, many businesses are willing to trade off software quality and compromise on requirements to achieve faster deployment of the software that they need

Agile methodologies attempt to produce the proper product through small cross-functional self-organizing teams that produce small pieces of functionality on a regular basis, allowing for frequent customer input and course correction as needed. In doing so, Agile tries to address the issues that traditional "waterfall" methodologies of delivering huge products over extended periods of time encounter, such as client requirements changing frequently and resulting in the delivery of incorrect products.

The reasons for applying agile methodologies are:

- **Faster is Better:** A shorter software development life cycle means that there is less time between paying and receiving payment. As a result, the company will get more profit.
- **Customer Satisfaction has Improved**: Customers don't have to wait months or years for exactly what they don't want with Agile. Rather, they receive fast iterations of something that is extremely near to what they desire. The system adapts swiftly to changes in the overall environment in order to refine the successful client solution.
- **Rework is No Longer Necessary:** The project stays on track and in tune with customer needs at every level by involving the client in more than just the requirements and delivery phases. Between the time we complete the task and the time the customer proposes adjustments, there will be less backtracking and “going out on a limb” time.

## Agile Manifesto

The Agile Manifesto for Software Development was a declaration of a unifying philosophy for frameworks like Scrum, Extreme Programming, and Feature-Driven Development (FDD)

The Manifesto for Agile Software Development states: 

> We are uncovering better ways of developing software by doing it and helping others do it.

Through this work, we have come to value:

**1. Individuals and interactions over processes and tools.**

It is people who drive the development process and respond to business needs on the fly, so they take precedence over processes and tools. If the latter things drive development, the team becomes less responsive and able to meet customer needs.

**2. Working software over comprehensive documentation.**

The Agile Manifesto deemphasizes the documentation of the development process, which historically took a huge amount of time and often bogged down the team. By avoiding unimportant minutiae, Agile shifts the team’s focus from the process itself to the results of the process (actual working software).

**3. Customer collaboration over contract negotiation.**

In traditional project management methods, such as Waterfall, customers negotiate the product requirements in great detail before any work starts, and they’re typically involved only at the beginning and at the end. In Agile, the customer becomes an important collaborator throughout the development process, ensuring their input is incorporated, and the result meets their needs along the way. Their role is provide and prioritize new system requirements and to evaluate
the iterations of the system.

**4. Responding to change over following a plan.**

Because ongoing adaptation isn’t built into the traditional software development process, change is an expensive headache. Agile embraces change, focusing on releasing a minimum viable product that can be evaluated and adjusted from iteration to iteration.


### The principles behind the Agile Manifesto.

The [12 Agile Manifesto principles](https://agilemanifesto.org/principles.html), expanding on the original manifesto, include:

- The highest priority is to satisfy the customer through early and continuous delivery of valuable software.
- The project team welcomes changing requirements, even late in development. Agile processes harness change for the customer’s competitive advantage.
- Deliver working software frequently, from a couple of weeks to a couple of months, with a preference to the shorter timescale.
- Business people and developers must work together daily throughout the project.
- The process builds projects around motivated individuals, giving them the environment and support they need, and trusts them to get the job done.
- A face-to-face conversation is the most efficient and effective method of conveying information to and within a development team.
- Working software is the most important measure of progress.
- Agile processes promote sustainable development. The sponsors, developers, and users should maintain a constant pace indefinitely.
- Pay continuous attention to technical excellence, and good design enhances agility.
- Simplicity is essential. This is the art of maximizing the amount of work not done.
- Self-organizing teams produce the best architectures, requirements, and designs.
- At regular intervals, the team reflects on how to become more effective and adjusts its behavior accordingly.

## The most used agile methodologies

### Scrum

This methodology makes it possible to tackle complex projects that require essential flexibility and speed when executing the results. The strategy will be aimed at managing and normalizing the errors that may occur in developments that are too long through frequent meetings to ensure compliance with the established objectives.

The Scrum approach is a general agile method but its focus is on managing iterative development rather than specific technical approaches to agile software engineering. Scrum does not prescribe the use of programming practices such as pair programming and test-first development. It can therefore be used with more technical agile approaches, such as XP, to provide a management framework for the project.

The meetings are the fundamental pillar of the methodology, where we differentiate between:

- **Sprint planning meeting:**: At the start of each sprint, a sprint planning meeting is held, during which the product owner presents the top items on the product backlog to the team. The Scrum team selects the work they can complete during the coming sprint. That work is then moved from the product backlog to a sprint backlog.
- **Daily Scrum:** Each day during the sprint, a brief meeting called the daily scrum is conducted. This meeting helps set the context for each day’s work and helps the team stay on track. All team members are required to attend the daily scrum.
- **Sprint review meeting:** At the end of each sprint, the team demonstrates the completed functionality at a sprint review meeting, during which, the team shows what they accomplished during the sprint. The meeting must not become a task in itself nor a distraction from the process.
- **Sprint retrospective:** Also at the end of each sprint, the team conducts a sprint retrospective, which is a meeting during which the team (including its ScrumMaster and product owner) reflect on how well Scrum is working for them and what changes they may wish to make for it to work even better.

Another most characteristic aspect of this methodology is that it proposes a way of working where various roles are presented, such as:

- **Scrum Master:** The Scrum Master is responsible for making sure the team is as productive as possible. The Scrum Master does this by helping the team use the Scrum process, by removing impediments to progress, by protecting the team from outside, and so on.
- **Product owner:** The product owner is the project’s key stakeholder and represents users, customers and others in the process. The product owner is often someone from product management or marketing, a key stakeholder or a key user.
- **Stakeholder:** It is the client, who must define the requirements and provide the feedback.
- **Scrum Team:** They are the people who develop or produce the product. Scrum teams develop a deep form of camaraderie and a feeling that “we’re all in this together.”

In the next image we can see the process:

![](https://evavillarrealguzman.github.io/img/agile-methodology/process.png)

On the left, we see the product backlog, which has been prioritized by the product owner and contains everything desired in the product that’s known at the time. The two to four week sprints are shown by the larger green circle.
At the start of each sprint, the team selects some amount of work from the product backlog and commits to completing that work during the sprint. Part of figuring out how much they can commit to is creating the sprint backlog, which is the list of tasks (and an estimate of how long each will take) needed to deliver the selected set of product backlog items to be completed in the sprint.
At the end of each sprint, the team produces a potentially shippable product increment — i.e. working, high-quality software. Each day during the sprint, team members meet to discuss their progress and any impediments to completing the work for that sprint. This is known as the daily scrum, and is shown as the smaller blue circle above.

> To clarify, the product backlog is a list of desired features for the product. The sprint backlog is a list of tasks to be completed in a sprint.

### Kanban 

Kanban is a Japanese word that translated into english means "visual card". Kanban is a visual management approach for tracking work as it progresses through a process. Kanban depicts both the process (workflow) and the actual work that passes through it. Kanban's goal is to identify and eliminate potential bottlenecks in your process so that work can flow at a cost-effective rate.

This methodology proposes real-time communication, controlling the work through a production line, in which three columns are created: pending, in process and finished. This allows you to classify tasks and easily view their progress.

### Extreme Programming (XP)

This methodology is very useful, especially for startups or companies that are in the process of consolidation, since its main objective is to help in relations between employees and clients. It was created to respond to highly changing environments where permanent feedback is needed.

It seeks to put more emphasis on the adaptability of a project than on its predictability, since it ensures that the expected result will be achieve. Consequently, those who participate in this methodology understand that changes are inevitable and, in fact, more beneficial than static growth.

In extreme programming, requirements are expressed as scenarios (called user stories), which are implemented directly as a series of tasks. Programmers work in pairs and develop tests for each task before writing the code. All tests must be successfully executed when new code is integrated into the system. There is a short time gap between releases of the system.

In an XP process, customers are intimately involved in specifying and prioritizing system requirements. The requirements are not specified as lists of required system functions. Rather, the system customer is part of the development team and discusses scenarios with other team members. Together, they develop a *story card* that encapsulates the customer needs. The development team then aims to implement that scenario in a future release of the software.

When a programmer builds the system to create a new version, he or she must run all existing automated tests as well as the tests for the new functionality. The new build of the software is accepted only if all tests execute successfully. This then becomes the basis for the next iteration of the system.

A general problem with incremental development is that it tends to degrade the software structure, so changes to the software become harder and harder to imple-
ment. Essentially, the development proceeds by finding workarounds to problems, with the result that code is often duplicated, parts of the software are reused in inappropriate ways, and the overall structure degrades as code is added to the system. Extreme programming tackles this problem by suggesting that the software should be constantly refactored. This means that the programming team look for possible improvements to the software and implement them immediately. When a team member sees code that can be improved, they make these improvements even in situations where there is no immediate need for them. Examples of refactoring include the reorganization of a class hierarchy to remove duplicate code, the tidying up and renaming of attributes and methods, and the replacement of code with calls to methods defined in a program library.

In principle then, the software should always be easy to understand and change as new stories are implemented. In practice, this is not always the case. Sometimes development pressure means that refactoring is delayed because the time is devoted to the implementation of new functionality. Some new features and changes cannot readily be accommodated by code-level refactoring and require the architecture of the system to be modified.

## Sources

- [Metodologías ágiles: ¿Qué son y cuáles son las más utilizadas?](https://www.aden.org/business-magazine/metodologias-agiles/)
- [Las metodologías ágiles más utilizadas y sus ventajas dentro de la empresa](https://www.iebschool.com/blog/que-son-metodologias-agiles-agile-scrum/)
- [¿Conoces los principios del Agile Manifesto?](https://www.iebschool.com/blog/agile-manifesto-agile-scrum/)
- [Performance Optimization Techniques For React Applications](https://www.c-sharpcorner.com/article/performance-optimization-techniques-for-react-applications/)
- [Metodologías Ágiles: Definición, Tipos, Usos](https://blog.comparasoftware.com/metodologias-agiles/)
- [Agile Methodology](https://www.atatus.com/glossary/agile-methodology/)
- [Agile Manifesto](https://www.workfront.com/project-management/methodologies/agile/agile-manifesto)
- Software Engineering, ninth edition - Ian Sommerville