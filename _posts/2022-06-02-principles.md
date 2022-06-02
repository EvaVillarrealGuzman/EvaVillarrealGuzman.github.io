---
title: Principles
layout: post
subtitle: Consistently following good principles is important to good code. We will see some of them
categories: design
tags: [design, SOLID, DRY, KISS]
---

Software design principles are the backbone or the support system for any software designing and development. Following these principles need proper principles and practices otherwise all software will move into the threat of becoming old as no new feature enhancement will ultimately lead to making software as legacy software.

## Don't Repeat Yourself (DRY)

The Don't Repeat Yourself (DRY) principle states that **duplication in logic should be eliminated via abstraction; duplication in process should be eliminated via automation**.

Adding additional, unnecessary code to a codebase increases the amount of work required to extend and maintain the software in the future.  Duplicate code adds to technical debt.  Whether the duplication stems from Copy Paste Programming or poor understanding of how to apply abstraction, it decreases the quality of the code.

Duplication in process is also waste if it can be automated.  Manual testing, manual build and integration processes, etc. should all be eliminated whenever possible through the use of automation.

## Once and Only Once

The Once and Only Once principle can be thought of as a subset of the Don't Repeat Yourself principle, and is one of the most fundamental principles of software development.  Once and Only Once basically states that **any given behavior within an application is defined Once and Only Once**.

Duplication of behavior is one of the most common sources of bugs in software systems, since it becomes increasingly likely that changes to behavior defined in one location may not be propagated to all locations where this behavior is defined.  Eliminating the duplication caused by not following the Once and Only Once principle is one of the primary reasons for refactoring and is also at the core of many design patterns.

## Hollywood

The Hollywood Principle states, *Don't Call Us, We'll Call You.* It's closely related to the Dependency Inversion Principle, and illustrates a different way of writing software from the more traditional form of programming in which one's own code dictates the flow of control. When following the Hollywood Principle, code is written to respond to external events, such as from an existing framework.

## Boy Scout Rule

Boy Scouts have a rule regarding camping, that they should leave the campground cleaner than they found it. By ensuring that the campground is cleaner when they leave than it was when they arrived, they can guarantee that they are doing no harm, at least when it comes to the cleanliness of the site.

In software, keeping code clean is a constant challenge, and developers and software teams must decide if, when, and how they will go about maintaining the cleanliness of their code. When does it make sense to spend time working to improve the design of the codebase? Over time, the quality of the underlying source code of a system tends to degrade, accumulating technical debt. Paying down this debt via refactoring is necessary in order to keep the code in a state where it is economical to extend and maintain it. Some teams take the approach of shutting down all value-adding work and simply trying to clean up the codebase for a week or a month or some other period of time. The Boy Scout Rule suggests an alternative approach, which is to simply try and ensure that with each commit, you leave the code better than you found it.  Maybe only slightly. Following this principle, teams can improve the quality of their code over time, while continuing to deliver value to their customers and stakeholders.

## Fail Fast

Fail fast **suggests that if an error or problem is going to occur, it's best to detect it as early as possible**.

Improper or invalid inputs to a system or to individual methods, are a common source of problems in software applications. Sometimes, these problems are persisted, resulting in corrupt or invalid data that can be difficult to isolate and correct. Sometimes, the problems leave the system in an unresponsive state, consuming resources and possibly denying other users access to it.

Practices like defensive programming and patterns like guard clauses are frequently applied in order to follow the fail fast principle.

Fail fast doesn't just apply to low level coding. It is also often applied to product development and marketing experiments. Rather than committing a large amount of resources to a product or campaign that is destined for failure, fail fast suggests running experiments as quickly as possible that can determine whether the product, campaign, or idea has merit before committing full resources to it.

## Keep it simple, Stupid! (KISS)

The more complex something is, the more ways there are for it to fail, and the more difficult it is to explain to someone else who needs to understand it.  When building software, an incremental approach that keeps things as simple as possible for as long as possible tends to yield working software with fewer defects, faster.  One way to minimize the amount of bugs in an application is to maximize the number of lines of code that aren't written, and avoiding needless complexity is a sure way to help achieve this goal.  It's also important to remember, when debating whether some complexity might be worthwhile, that many times You Aren't Gonna Need It.  If we write our software such that it is flexible, we can add new functionality later when it's needed.  To this end, simple software tends to be more malleable than complex software.

## Persistence Ignorance (PI)

The PI holds that **classes modeling the business domain in a software application should not be impacted by how they might be persisted**. Thus, their design should reflect as closely as possible the ideal design needed to solve the business problem at hand, and should not be tainted by concerns related to how the objects' state is saved and later retrieved. Some common violations of Persistence Ignorance include domain objects that must inherit from a particular base class, or which must expose certain properties. Sometimes, the persistence knowledge takes the form of attributes that must be applied to the class, or support for only certain types of collections or property visibility levels. There are degrees of persistence ignorance, with the highest degree being described as Plain Old Java Objects (POJOs) in the Java world.

## Tell, Don't Ask (TDA)

The TDA principle **suggests that it is better to issue an object a command do perform some operation or logic, rather than to query its state and then take some action as a result**. It is related to the Flags Over Objects antipattern as well as the Anemic Domain Model antipattern. You can easily spot violations of TDA in code that queries or uses several properties of an object in order to perform some calculation. This is especially problematic when the same kind of calculation is done in many places (violating the Don't Repeat Yourself principle), but can represent a design deficiency even if it only occurs in one location in the current codebase.

Example that violates TDA:

```java
public class CpuMonitor
{
    public int Value { get; set; }
}
public class Client
{
    public void AlertService(List<CpuMonitor> cpuMonitors)
    {
        foreach (var cpuMonitor in cpuMonitors)
        {
            if (cpuMonitor.Value > 90)
            {
                // alert
            }
        }
    }
}
```

Example refactored

```java
public class CpuMonitor
{
    private readonly int _alertThreshold;
    private readonly Action<CpuMonitor> _alertAction;
    public CpuMonitor(int alertThreshold, Action<CpuMonitor> alertAction)
    {
        _alertThreshold = alertThreshold;
        _alertAction = alertAction;
    }
    public int Value { get; set; }
    public bool ExceedsThreshold { get { return Value >= _alertThreshold; } }
    public void Sample()
    {
        if (ExceedsThreshold)
        {
            _alertAction(this);
        }
    }
}
public class Client
{
    public void AlertService(List<CpuMonitor> cpuMonitors)
    {
        foreach (var cpuMonitor in cpuMonitors)
        {
            cpuMonitor.Sample();
        }
    }
}
```

In the example above, the refactoring looks at the magic number representing the alert threshold, and moves this concept into the monitor itself. This might not be worth correcting if this client code were the only instance of this behavior, but if you find repetition in this kind of code, follow the Don't Repeat Yourself principle and consolidate it into a class (in this case, CpuMonitor). Also, the refactoring moves the responsibility for sending alerts into the monitor itself, while still avoiding tightly coupling it to any particular alert implementation. In this way, monitor remains loosely coupled from whatever alert implementation the system might have in place.

It's not necessary to follow the "don't ask" part of this principle to the extreme of eliminating all access to objects' state. In general, it's ok to query an object for its state, provided the information isn't being used to make a decision related to the object. If it is, then that decision and any corresponding behavior should most likely be moved within the object itself. Another consequence of violating TDA is that often magic numbers or business rules end up sprinkled throughout code that references object state, rather than embedded within the object itself or passed into the object as a defined and well-named construct (such as CpuAlertThreshold in the example above).

## You Ain't Gonna Need It (YAGNI)

The principle states that **always implement things when you actually need them, never when you just foresee that you may need them**.

There are a number of reasons why this principle exists.  Firstly, it maximizes the amount of unnecessary work that is left undone, which is a great way to improve developer productivity and product simplicity.  Remember, features are expensive, both to develop and maintain, and for users to learn and navigate around.  Features that aren't actually necessary are a huge source of waste.

It follows that YAGNI is closely related to te KISS principle. By avoiding adding features and complexity until it's actually needed, the overall design of the system can remain simpler, longer.

This principle emerged as one of the key principles of Extreme Programming.

## Explicit Dependencies

The Explicit Dependencies Principle states that **methods and classes should explicitly require (typically through method parameters or constructor parameters) any collaborating objects they need in order to function correctly**.

If your classes require other classes to perform their operations, these other classes are dependencies.  These dependencies are implicit if they exist only in the code within your class, and not in its public interface.  Explicit dependencies appear most often in an object's constructor, for class-level dependencies, or in a particular method's parameter list, for more local dependencies.

Classes with implicit dependencies cost more to maintain than those with explicit dependencies.  They are more difficult to test because they are more tightly coupled to their collaborators.  They are more difficult to analyze for side effects, because the entire class's codebase must be searched for object instantiations or calls to static methods.  They are more brittle and more tightly coupled to their collaborators, resulting in more rigid and brittle designs.

Classes with explicit dependencies are more honest.  They state very clearly what they require in order to perform their particular function.  They tend to follow the Principle of Least Surprise by not affecting parts of the application they didn't explicitly demonstrate they needed to affect.  Explicit dependencies can easily be swapped out with other implementations, whether in production or during testing or debugging.  This makes them much easier to maintain and far more open to change.

The Explicit Dependencies Principle is closely related to the Dependency Inversion Principle and the Hollywood Principle.

## Stable Dependencies

The Stable Dependencies Principle states that **the dependencies between software packages should be in the direction of the stability of the packages. That is, a given package should depend only on more stable packages**. Whenever a package changes, all packages that depend on it must be validated to ensure they work as expected after the change. Thus, the more packages that depend on an unstable package, the greater the disruption whenever it changes.

## Inversion of Control (IoC or IOC) 

Inversion of Control describes a system that follows the Hollywood Principle. That is, flow of control within the application is not controlled by the application itself, but rather by the underlying framework. Typically in such an architecture, the application is written such that it ties into the application framework by handling framework events or plugging in to framework extension points.

An IOC Container, also known as a Dependency Inversion (DI) container, is a specialized factory used to facilitate dependency injection.

Inversion of control is a way of working that breaks with the traditional format, where the programmer is in charge of defining the sequence of operations that must be carried out to reach a result. In this sense, the programmer must know all the details of the framework and the operations to be performed on it, on the other hand, the Framework knows absolutely nothing about our program. That is, it only exposes operations to be executed. The following image shows the normal working way.

![](https://evavillarrealguzman.github.io/img/design-principles/ioc-1.png)

Let's see that our application is the one that calls the Framework at all times and it does what the App requests. On the other hand, the Framework doesn't care about the App at all.

But what would happen if we inverted the way we work, and instead of the App calling Framework functions, it is the Framework that executes operations on the App?  This is Inversion of Control. Let's see in the following image how IoC works:

![](https://evavillarrealguzman.github.io/img/design-principles/ioc-2.png)

This last image looks quite similar to the previous one, however, it has a big difference, and that is that the calls were inverted, now the Framework is the one that performs operations on our App. Well, IoC is based on introspection (in Java called Reflection) which is a procedure by which we read metadata from the App that allows us to understand how it works. Metadata can be expressed mainly in two ways, the first is through configuration files such as XML or with metadata directly defined on the classes of our program, in Java these metadata are the @Annotations and it is through this metadata and with introspection techniques that it is possible to understand the operation of the App.

Surely at this point you are wondering how it is that a Framework that does not know your App and that was even compiled long before can perform operations on our App. The answer is simple but complicated, and that is that using IoC is much simpler than It would seem, but implementing a Framework of this type has a high degree of complexity, in other words, if you are the App you can easily implement the Framework, but if you are developing the Framework it can be a real headache.

### IoC in React

The IoC principle is a way of building an API for some module that allows flexible customizations of this module without tons of input parameters like:

- flags (e.g., “visibleIcon”, “disabledSubSecionSubmitButton”, and similar)
- types (e.g., iconType="primary", subSectionSubmitButonType="secondary")

This principle makes a lot of shared things or libraries easier to customize and safer to use. Allow you give to the people the tools to solve their own problem. It benefit the extensivity of your components.

There are several patterns we can follow implementing inversion of control for React component:

- render props
- React children

Both approaches are good. Every one of them may be useful in different cases. These patterns are tightly connected with a “Compound Components” pattern. It is a useful thing for developing cohesive components.

Here are some interesting post about IoC with React:

- [Inversion of Control](https://kentcdodds.com/blog/inversion-of-control)
- [Inversion of Control with shareable react components](https://loweisz.com/inversion_of_control_with_react_components/)
- [What You Need to Know about the Inversion of Control (for React)](https://javascript.plainenglish.io/what-you-need-to-know-about-the-inversion-of-control-for-react-278c0f4dcd18)


> If you are having more than two or three conditions to display something or not in a component, is a point to think about inverting the control.

> Don't use more than two optional and visual props and if you are using one of them more than two times, make it a separate component.


## Separation of Concerns

A key principle of software development and architecture is the notion of separation of concerns.  At a low level, this principle is closely related to the Single Responsibility Principle of object oriented programming.  The general idea is that **one should avoid co-locating different concerns within the design or code**.  For instance, if your application includes business logic for identifying certain noteworthy items to display to the user, and your application formats such items in a certain way to make them more noticeable, it would violate separation of concerns if both the logic for determining which items were noteworthy and the formatting of these items were in the same place.  The design would be more maintainable, less tightly coupled, and less likely to violate the Don't Repeat Yourself principle if the logic for determining which items needed formatted were located in a single location (with other business logic), and were exposed to the user interface code responsible for formatting simply as a property.

At an architectural level, separation of concerns is a key component of building layered applications.  In a traditional N-tier application structure, layers might include data access, business logic, and user interface.  More modern N-tier application designs might include a core domain model and separate infrastructure modules in addition to one or more front end services and/or user interfaces.  Web pages, to a greater or lesser degree, separate concerns relating to structure, logic, and formatting through the use of HTML, JavaScript, and CSS.  At a lower level, the networking model used by the Internet is broken into a series of layers each with specific concerns and responsibilities, and demonstrates how separation of concerns can be effectively applied.

Separation of Concerns tends to be a natural consequence of following the Don't Repeat Yourself principle, since of necessity abstractions must be built to encapsulate concepts that would otherwise be repeated throughout the application.  As long as these abstractions are logically grouped and organized, then Separation of Concerns should be achieved.

### Separation of Concerns in React

We learned that the separation of concerns is important, and we used to think about it in terms of separating the logic from the templates. Our goal has always been to write the JavaScript and the HTML in different files. Various templating solutions have been created to help developers achieve this.

The problem is that most of the time, that kind of separation is just an illusion and the truth is that the JavaScript and the HTML are tightly coupled, no matter where they live.

Templates highly depend on the models they receive from the logic layer to display the information. On the other hand, JavaScript interacts with the DOM elements rendered by the templates to update the UI, even if they are loaded from separate files.

The same problem applies to styles: they are defined in a different file, but they are referenced in the templates and the CSS selectors follow the structure of the markup, so it is almost impossible to change one without breaking the other, which is the definition of coupling.

That is why the classic separation of concerns ended up being more a separation of technologies, which is of course not a bad thing, but it does not solve any real problems.

React tries to move a step forward by putting the templates where they belong: next to the logic. The reason it does that is because React suggests you organize your applications by composing small bricks called components.

The framework should not tell you how to separate the concerns, because every application has its own, and only the developers should decide how to limit the boundaries of their apps.

The component-based approach drastically changes the way we write web applications, which is why the classic concept of separation of concerns is gradually being taken over by a much more modern structure.

Using JavaScript for both logic and templating not only helps us separate our concerns in a better way, but it also gives us more power and more expressivity, which is what we need to build complex user interfaces.

There is another concept, which is pretty controversial which React: moving the styling logic inside the component, too.

The end goal is to encapsulate every single technology used to create our components and separate the concerns according to their domain and functionalities.

## Law of Demeter (LoD)

The Law of Demeter or principle of least knowledge tells us that **we must know as little as possible of a class, in such a way that we avoid knowing the structure or properties  of the component**. In its general form, the LoD is a specific case of loose coupling.

The law of Demeter is similar to the phrase *Don't talk to strangers*. It's promotes that we only speak with the direct properties of the component to communicate, without getting too involved in its internal properties. We will see an example:

```java
String country = invoice.getCustomer().getAddress().getCountry();
```

In this example, we see that the invoice object which has a customer, which, in turn, has an address, which in turn has a country. In this sense, we could say that the invoice is an acquaintance, and we know the client through the invoice; up to this point we can say that we are talking to an acquaintance, but at the moment we ask the client for the address, we are talking to an acquaintance of an acquaintance, that is, for us he is already a stranger; in the same way, the country would also be unknown. For this reason, the law of demeter tells us that we should avoid talking to strangers, because the more we know the structure of the component, the more we adapt to it.

The more we depend on the structure of the invoice, the more susceptible we are to any change in its functioning, very different from that, if we only spoke with the “acquaintances”, because our level of coupling would decrease.

The next example, don't violate this law:

```java
String country = invoice.getCountry();
```

## SOLID

This acronym was introduced for the first time in the early 2000s by Robert C. Martin (better known to many as "Uncle Bob") and is the union of 5 principles that for many are the most important of all.

### Single Responsability Principle (SRP)

This principle states that **a class should only have a single reason to change**. It should only know about one thing. However, like other principles, it's unwise to try and apply SRP to everything from the outset.  If a particular class is stable and isn't causing needless pain as a result of changes, there is little need to change it. 

Some examples of responsibilities to consider that may need to be separated include:

- Persistence
- Validation
- Notification
- Error Handling
- Logging
- Class Selection / Instantiation
- Formatting
- Parsing
- Mapping

The Single Responsibility Principle is closely related to the concepts of coupling and cohesion.  **Coupling** refers to **how inextricably linked different aspects of an application are**, while **cohesion** refers to **how closely related the contents of a particular class or package may be**. All of the contents of a single class are tightly coupled together, since the class itself is a single unit that must either be entirely used or not at all. When other classes make use of a particular class, and that class changes, the depending classes must be tested to ensure they continue to function correctly with the new behavior of the class, increasing the total surface area of the application that is affected by the change. If a class has poor cohesion, some part of it may change that only certain depending classes utilize, while the rest of it may remain unchanged. If instead the class were broken up into several, highly cohesive classes, each would be used by fewer other elements of the system, and so a change to any one of them would have a lesser impact on the total system.

A close neighbor of the SRP is Don't repeat yourself (DRY). Keeping an application DRY requires strict adherence to the Single Responsibility Principle. A DRY class would typically not contain any code that isn't its primary behavior.

Now we will see an example, take a look in the next image. The employee object is responsible for saving him or her self to the database or printing their own details in the reports or terminating themselves from the company. The problem with the design is that this employee class is responsible for way too much and when classes stretch their scope of responsibility things seem out of place. Especially their behavior. This object knows way too much. It enforces how it should be saved to the database so it knows about the database details. It also forces its clients to use its report formatting for printing an XML or CSV.

![](https://evavillarrealguzman.github.io/img/design-principles/employee.png)

Can you spot how many potential reasons the current design of the Employee class may have for change?. It's safe to say that it has at least three reasons:

- the employee model itself.
- if the company's database implementation changes all such classes containing database code would need to be modified. 
- requirement changes for report formats may result in modifying both XML and CSV methods of this class.

In the next image is a new design for the program that conform the Single Responsability Principle:

![](https://evavillarrealguzman.github.io/img/design-principles/employee2.png)

- EmployeeDAO is responsible for saving and deleting employees in the database. The naming convention used here is quite common in the industry. DAO stands for *data access object*.
- Employee class is now just a POJO. In other words just a basic Java class that will represent employee objects in our application.
- The report formatter is a parent class also referred to as a base class. The employee report formatter is a child of that class specific to employees and inherits the formatting rules and methods defined in its parent. So we'll actually be defining the default formatting rules in this parent report formatter and its children will be able to use that functionality.
- Database connection manager is responsible for managing database connectivity. The employee DAO class requires a database connection object to connect and execute database operations so it requires this connection manager to hand it a connection object.

With respect of the relationship between SRP and DRY, the code that prints reports should simply be elsewhere in another class. That's better suited for that responsibility rather than duplicated in several different objects. In case of requirement changes only a single place in the application code would require modification and we wouldn't have to touch all those classes.

### Open Closed Principle

This principle states that **software entities (classes, methods and packages) should be open for extension but closed for modification**. In other words software should be designed in such a way that new functionality should cause minimum changes to already working and tested code in the development life cycle.

New requirements continue to pour in and if the developers are forced to constantly change the already completed and tested modules to support new requirements the design is considered fragile.

The Open-Closed Principle can also be achieved in many other ways, including through the use of inheritance or through compositional design patterns like the Strategy pattern.

Let's expand upon that idea to represent the work that employees do in an emergency room setting. Obviously not all employees have the same exact behavior. If the employee is a nurse he or she checks vitals of patients. If the employee is a doctor he or she diagnosis patients and prescribes medicine. So I'll first create an implementation that violates the open closed principle:

```java
package hospital;


public class Doctor extends Employee {
	
	public Doctor(int id, String name, String department, boolean working) {
		super(id, name, department, working);
	}
```

```java
package hospital;


public class Nurse extends Employee {
	
	public Nurse(int id, String name, String department, boolean working) {
		super(id, name, department, working);
	}
```

Now hospital management should have the capability of being able to call upon any employee to carry out their duties:

```java
package hospital;


public class HospitalManagment {
	
	public void callUpon(Employee employee) {
		if( employee instaceOf Nurse ){
            checkVitalSigns();
            drawBlood();
            cleanPatientArea();
        } else if( employee instanceOf Doctor ){
            prescribeMedicine();
            diagnosePatients();
        }
	}

    //Nurses
    private void checkVitalSigns(){
        System.out.print("Checking vitals");
    }

    private void drawBlood(){
        System.out.print("Drawing blood");
    }

    private void cleanPatientArea(){
        System.out.print("Cleaning Patient Area");
    }

    //Doctors
    private void prescribeMedicine(){
        System.out.print("Prescribing medicine");
    }

    private void diagnosePatients(){
        System.out.print("Diagnosing Patients");
    }

}
```

Next I'll create a class called emergency room. I'll call it emergency room process. This class will contain a main method so that we can test our objects. So basically the scenario we are going to create here is that hospital management will enter the emergency room and tell all kinds of employees to perform their respective duties. And that logic is captured in the hospital management class here well instantiate the objects and test our system so let's get started on that.

```java
package hospital;


public class EmergencyRoomProcess {
	
	public static void main(Strings args[]) {
		
        HospitalManagment ERDirector = new HospitalManagment();
        Employee peggy = new Nurse(1, "Peggy", "emergency", true);

        ERDirector.callUpon(peggy);

        Employee suzan = new Doctor(2, "Suzan", "emergency", true);
        ERDirector.callUpon(suzan);

	}
```

Now there are several issues with this design:

- The first thing to always look for when improving design is object responsibility. Understand that we are developing a component that represents employees working. Take a look at the employee classes. You have nurse, doctor and then we have the Employee class. There's no work behavior defined for any of these employees. So where is this behavior defined if it's not on the employee objects?
- Hospital management class is containing all kinds of behaviors. Anytime you see conditional statements based around the instance of keyword, you should think about object responsibility and taking that away someplace else because these messages certainly do not belong here.
- Let's say requirements came in to also support the work done by the hospital CEO or department directors or security guards. HospitalManagement class would quickly become messy with many many more instance of conditionals. This class already knows way too much. It knows what doctors and nurses do.
  
So let's quickly fix this before more business requirements come in to support other employees. Let's give employee objects the behavior that they are responsible:

```java
package hospital;


abstract public class Employee {
	
    private int id;
    private String name;
    private String department;
    private boolean working;

	public Employee(int id, String name, String department, boolean working) {
		super();
        this.id = id;
        this.name = name;
        this.department = department;
        this.working = working;
	}

    public abstract void performDuties();

    @Override
    public String toString(){
        return "Employee [id=" + id + ", name=" + name + ", department=" + deparment + ", working=" + working +"]";
    }

```

```java
package hospital;


public class Doctor extends Employee {
	
	public Doctor(int id, String name, String department, boolean working) {
		super(id, name, department, working);
	}

    private void prescribeMedicine(){
        System.out.print("Prescribing medicine");
    }

    private void diagnosePatients(){
        System.out.print("Diagnosing Patients");
    }

    public void performDuties(){
        prescribeMedicine();
        diagnosePatients();
    }
```

```java
package hospital;


public class Nurse extends Employee {
	
	public Nurse(int id, String name, String department, boolean working) {
		super(id, name, department, working);
	}

    private void checkVitalSigns(){
        System.out.print("Checking vitals");
    }

    private void drawBlood(){
        System.out.print("Drawing blood");
    }

    private void cleanPatientArea(){
        System.out.print("Cleaning Patient Area");
    }

    public void performDuties(){
        checkVitalSigns();
        drawBlood();
        cleanPatientArea();
    }
```


Now hospital management should have the capability of being able to call upon any employee to carry out their duties. If more types of employees are added such as department managers or security guards they would all just do work as defined in their own class definitions and the specifics of their behavior will be no one's business.

```java
package hospital;


public class HospitalManagment {
	
	public void callUpon(Employee employee) {
        employee.performDuties();
	}

}
```

This also could have been done using an interface rather than making the Employee class abstract and for that we could have define an interface say employee work and added the perform duties behavior there and have all our employees implement that interface. Both approaches allow for abstraction and that's what this design principle is about. It makes the system more abstract to support extensibility.

A unit test written for the hospital management class would not require modification if we extended the system to support several different kinds of employees because all these employees would respond to the perform duties message.

Another simple examples of this principle is to consider a method that does one thing. Let's say it writes to a particular file, the name of which is hard-coded into the method. If the requirements change, and the filename now needs to be different in certain situations, we must open up the method to change the filename. If, on the other hand, the filename had been passed in as a parameter, we would be able to modify the behavior of this method without changing its source, keeping it closed to modification.

### Liskov Substitution Principle (LSP) 

The LSP states that **subtypes must be substitutable for their base types**. When this principle is violated, it tends to result in a lot of extra conditional logic scattered throughout the application, checking to see the specific type of an object. This duplicate, scattered code becomes a breeding ground for bugs as the application grows.

A very common violation of this principle is the partial implementation of interfaces or base class functionality, leaving unimplemented methods or properties to throw an exception (e.g. NotImplementedException). In code that you know is only going to be used by one client that you control, this is fine, but if such classes are going to be in a shared codebase, or worse, framework code that is shipped to third parties, such implementations should be avoided. If a given interface has more features than you require, follow the Interface Segregation Principle and create a new interface that includes only the functionality your client code requires, and which you can implement fully.

A common code smell that frequently indicates an LSP violation is the presence of type checking code within a code block that should be polymorphic. For instance, if you have a foreach loop over a collection of objects of type Foo, and within this loop there is a check to see if Foo is in fact Bar (subtype of Foo), then this is almost certainly an LSP violation. If instead you ensure Bar is in all ways substitutable for Foo, there should be no need to include such a check.

Let's take an example consider a high school in which we have different kinds of teachers. We can have an abstract base class called teacher. I've made the teach method abstract because I want to make sure all derivatives of this class are forced to implement the teach method in their own way whatever teaching means for their particular subject. But teachers don't only teach, they carry out other responsibilities in a high school. So let me define some more methods, for example, all teachers make announcements, they also take attendance and they collect the paperwork.

![](https://evavillarrealguzman.github.io/img/design-principles/school1.png)

But let's say we add a substitute teacher class.

Substitutes teacher must assume almost all duties of the regular classroom teacher such as make announcements, take attendance and collects paperwork. Basically everything a teacher would be responsible for but the one thing that most substitute teachers don't actually do is teach new lessons to students. That's the responsibility of the actual teacher that happens to be absent on the day of the substitute is covering. Here occurs a violation of the Liskov Substitution Principle.

![](https://evavillarrealguzman.github.io/img/design-principles/school2.png)

The substitute is forced to implement the teach method. Substitute Teacher class is not completely substitutable for the teacher base class. Now some people might handle this problem by throwing a custom *cannot teach exception* to indicate that calling this method is illegal. This is a common anti-pattern that should be avoided at all costs. Another thing that some developers might do is downcast teachers to concrete derivatives and check for the case where they are a substitute in the client code. And this is even worse because now code that was supposed to operate on the teacher base class and we'd be seeing those infamous instance of conditional statements again and violating other design principles.

To fix this problem I create a school staff parent class and create an interface for Course instructor in which we declare the teach contract. Notice how they have broken out the responsibility of teaching to a particular interface called *Course instructor* and the general responsibilities that all high school staff members should have is encapsulate in this *school staff* abstract class.

![](https://evavillarrealguzman.github.io/img/design-principles/school3.png)

You know you are violating LSP when it doesn't quite make sense for a method to be called on the subclass object.

In the example that we saw in the Open Closed Principles, we was violated this principle. Then we fixes when eliminated those instance of conditionals in the Hospital Managment class. We changed the Employee class to be abstract and all subtypes of that class were required to implement the abstract performed duties method. This provided a tremendous amount of freedom for the client because it could now just call upon all employees to do work without knowing the details of their specific jobs. Now this was only possible because the derivatives of the Employee class such as a doctor or nurse were genuinely employees.

### Interface Segregation Principle (ISP)

This principle **states that no clients should be forced to depend on methods they do not use**. This often becomes a problem when clients have dependencies on what we call those fat classes. A fat class is a class that has too many responsibilities having an interface. To separate behavior is a good idea and it prevents tight coupling between between the objects.

Another benefit of smaller interfaces is that they are easier to implement fully, and thus less likely to break the Liskov Substitution Principle by being only partially implemented. They also provide greater flexibility in how you implement functionality, since parts of a larger interface can be implemented in different ways. Consider the Repository pattern, which usually includes methods for reading as well as writing. A common performance pattern for database reads it so add a caching layer, but this generally only makes sense for read operations. Likewise, scalability can often be improved by queuing commands (like write operations) rather than executing them immediately, but you wouldn't queue queries. Thus, having an IRepository interface composed of an IReadOnlyRepository and an IWriteRepository would allow base implementations that go against a data store and separate implementations that employ caching and queuing, as well.

We will see an example. The model in the below image represents customer transactions. the customer transaction object contains a lot of behavior that other client modules like accounts receivable and report generator depend on. And this transaction object is obviously unaware of these two clients because it's the clients that are pointing at it rather than it containing references to the two clients. But both of the clients don't require all of the behavior of the customer transaction object has to offer. For example the accounts receivable client only relies on functionality for charging the customer and preparing an invoice report generator. On the other hand uses methods like get name get date and get product breakdown for reporting purposes.

![](https://evavillarrealguzman.github.io/img/design-principles/isp1.png)

Changes in the customer transaction class could result in recompilation and redeployment of both of these dependent client modules. For instance if requirement changes force us to modify charge customer method to accept a new argument the report generator would also be dragged into recompilation and redeployment even though it does not use that particular method. And the reason for this is because there is a direct relationship with the underlying customer transaction object only the accounts receivable module should be recompiled and redeployed. Why does the report generator need to be recompiled and redeployed? the way around this is to put an interface between the objects that depend on this customer transaction object and only to find those methods in the interface that are specific to the particular module that is being separated by that interface.


```java
import java.util.Date;

public class CustomerTransaction {

    public String getName(){
        return 'name';
    }

    public Date getDate(){
        return new Date();
    }
	
    public void productBreakdown(){
        System.out.print("List of products for reporting");
    }

    public void prepareInvoice(){
        System.out.print("Invoice prepared...");
    }

    public void chargeCustomer(){
        System.out.print("Charged the customer");
    }

}
```

```java

public class AccountsReceivable {

    private CustomerTransaction transactionObject;
    
    public AccountsReceivable(CustomerTransaction aTransaction){
        transactionObject = aTransaction
    }

    public void postPayment(){
        transactionObject.chargeCustomer();
    }
	
    public void sendInvoice(){
       transactionObject.prepareInvoice ();
    }

}
```

If we change something or the customer transaction object that is not specific to accounts receivable accounts receivable module would still have to be recompiled to get the latest code.  That's sort of a dependency should not be there between these objects directly because accounts receivable doesn't use all of the customer transactions behavior. It only uses two of the methods. So what we can do now is extract an interface from the customer transaction object.

```java
public class Customer {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}	
}
```

```java
public class Product {
	private int productId;
	private String productName;
	
	public int getProductId() {
		return productId;
	}
	public void setProductId(int productId) {
		this.productId = productId;
	}
	public String getProductName() {
		return productName;
	}
	public void setProductName(String productName) {
		this.productName = productName;
	}
}
```

```java
import java.util.Date;

public interface Reporting {

	// methods for reporting
	public String getName();

	public Date getDate();

	public String productBreakDown();
}
```

```java
public interface Accounting {

	public void prepareInvoice();

	public void chargeCustomer();
}
```

```java
import java.util.Date;
import java.util.List;


public class CustomerTransaction implements Accounting, Reporting {
    
    private List<Product> products;
    
    private Customer customer;
    
    public CustomerTransaction(Customer customer, List<Product> products){
        this.products= products;
        this.customer = customer;
    }
    
    // methods for reporting
    /* (non-Javadoc)
        * @see Reporting#getName()
        */
    @Override
    public String getName(){
        return customer.getName();
    }
    /* (non-Javadoc)
        * @see Reporting#getDate()
        */
    @Override
    public Date getDate(){
        return new Date();
    }
    
    /* (non-Javadoc)
        * @see Reporting#productBreakDown()
        */
    @Override
    public String productBreakDown(){
        String reportListing = null;
        for(Product product: products){
            reportListing +=product.getProductName();
        }
        return reportListing;
    }
    
    /* (non-Javadoc)
        * @see Accounting#prepareInvoice()
        */
    @Override
    public void prepareInvoice(){
        System.out.println("invoice prepared...");
    }
    
    /* (non-Javadoc)
        * @see Accounting#chargeCustomer()
        */
    @Override
    public void chargeCustomer(){
        System.out.println("charged the customer");
    }	

}
```

### Dependency Inversion Principle (DIP)

The DIP states that **high level modules should not depend on low level modules; both should depend on abstractions. Abstractions should not depend on details.  Details should depend upon abstractions**. It's extremely common when writing software to implement it such that each module or method specifically refers to its collaborators, which does the same. This type of programming typically lacks sufficient layers of abstraction, and results in a very tightly coupled system, since every module is directly referencing lower level modules.

For example, noticed the diagram in the below image. Engine class that were written making use of the new keyword to instantiate objects. This is  actually tightly coupled to specific kinds of objects, every instance of car that we created is going to have the same kind of engine. But we would like to make this more dynamic.


```java

public class Vehicle {
   Engine myEngine = new LargeEngine();

    public void crackIgnation(){
        myEngine.startEngine();
        System.out.print("Vehicle is running");
    }
}
```

```java

public class App {
    
    public static void main(String[] args){
        Vehicle raceCar = new Vehicle();
        raceCar.crackIgnation();
    }

}
```

Dependency Injection allows us to do is to get rid of all of these new keywords that we have inside of this specific objects, so that those objects are not tied to those specific implementations. Dependency injection is a powerful mechanism for separating the construction of an object from its eventual use.

Let's do this more flexible. We changed the hard coding of a particular implementation to sort of make it more generic. Now the constructor is expecting an engine argument to create a vehicle object. This object is created on the heap and then it's passed into the constructor of the vehicle. And then we create an object of type vehicle already set injected with the proper engine that it needs.

```java

public class Vehicle {
    Engine myEngine;

	public Vehicle(Engine anEngine){
        myEngine = anEngine;
    }

    public void crackIgnation(){
        myEngine.startEngine();
        System.out.print("Vehicle is running");
    }
}
```

```java

public class App {
    
    public static void main(String[] args){
        Vehicle raceCar = new Vehicle(new SmallEngine(100));
        raceCar.crackIgnation();
    }

}
```

A very popular Dependency Injection implementation is one that's provided as part of the Spring Framework. It's often called the spring container and that comes up bundled with the overall Spring Framework as we'll see shortly. Developers define the objects that should be wired together in configuration file. And those are basically instructions for constructing objects organized in an XML format. And what spring does is it goes in that XML file and reads that information or those instructions for how the objects should be created. And the other code you can actually access the objects by their name as you've defined them in the XML. So when the application starts up spring automatically constructs the objects appropriately with their proper dependencies injected.

## Sources

- [SOLID](https://deviq.com/principles/solid)
- [Boy scout rule](https://deviq.com/principles/boy-scout-rule)
- [Dependency Inversion Principle](https://deviq.com/principles/dependency-inversion-principle)
- [Don't repeat yourself](https://deviq.com/principles/dont-repeat-yourself)
- [Explicit Dependencies Principle](https://deviq.com/principles/explicit-dependencies-principle)
- [Fail fast](https://deviq.com/principles/fail-fast)
- [Hollywood Principle](https://deviq.com/principles/hollywood-principle)
- [Interface Segregation Principle](https://deviq.com/principles/interface-segregation)
- [Inversion of control](https://deviq.com/principles/inversion-of-control)
- [Keep it simple](https://deviq.com/principles/keep-it-simple)
- [Liskov Substitution Principle](https://deviq.com/principles/liskov-substitution-principle)
- [Once and only once](https://deviq.com/principles/once-and-only-once)
- [Open-Closed Principle](https://deviq.com/principles/open-closed-principle)
- [Persistence Ignorance](https://deviq.com/principles/persistence-ignorance)
- [Separation of Concerns](https://deviq.com/principles/separation-of-concerns)
- [Single responsability Principle](https://deviq.com/principles/single-responsibility-principle)
- [Stable Dependencies](https://deviq.com/principles/stable-dependencies)
- [Tell, Don't Ask ](https://deviq.com/principles/tell-dont-ask)
- [YAGNI](https://deviq.com/principles/yagni)
- [Master Object Oriented Design in Java - Homework + Solutions](https://www.udemy.com/course/mastering-object-oriented-design-in-java)
- React Design Patterns and Best Practices-Packt Publishing - Bertoli, Michele
- Introducción a la arquitectura de software - Oscar Javier Blancarte Iturralde
1