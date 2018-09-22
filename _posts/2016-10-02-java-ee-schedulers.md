---
author: gualtierotesta
date: 2016-10-02 18:00:35+00:00
layout: post
slug: java-ee-schedulers
title: Java EE schedulers
wordpress_id: 531
categories:
- java
tags:
- '@Schedule'
- '@Singleton'
- '@Startup'
- '@Timeout'
- calendar
- cron
- github
- java
- javaee
- quartz
- scheduler
- timer service
- websphere
---

*Last update: 22 Sep 2018*

Java EE application servers have native scheduling support and, in most of the applications, there is no need to include external dependencies like the famous [Quartz scheduler](http://www.quartz-scheduler.org/) library.

The [Java EE 6 Timer Service](http://docs.oracle.com/javaee/6/tutorial/doc/bnboy.html), available on Java EE 6 and 7 full profile, give us many options to define the scheduling interval and what's happen if we stop and restart the application which contains our scheduler.

A Java EE scheduler can be:

* **persistent**: the application server saves the scheduling events when the application is down  in order to not lose them
* **automatic**: simple scheduler definition, most of the details are handled by the application server
* **programmatic**: we have full control of all scheduler parameters.

To decide which is the best option, we should first answer to the following questions:

# Is it allowed to miss some scheduling events?

If we stop or restart the application (for example during an update) the scheduler will be stopped and some scheduling events could be lost.

The scheduler can be configured to save the missed events and to execute them when the application will be up again. The application server uses an internal database (it is usually a Java DB like Derby) to store the missed events.

This is a persistent scheduler.

**Note**: the application server will generate all missed events at application (re)start. This burst of events is configurable in frequency and delay. See you application server documentation for the details.

We have also the option to not persist the scheduling events which will be lost if application is not running.

In the not persistent case, the scheduler life cycle is the same as the application: it is created at application startup and then destroyed at application shutdown.

On the contrary, a persistent scheduler survives to the application restarts; it is simply sleeping when the application is not running.

How to choose?

If the scheduled functionality is business critical and we cannot afford to miss an event, the persistent scheduler is the way to go.

In all other cases, the not persistent scheduler is lighter (no DB is used) and easier to manage (less hurdle when updating the application because there is no a burst of scheduling events at application restart; the scheduler is always created new at application start ).

# Will the application run in a cluster?

In a cluster, more than one instance of our application is running (one instance per cluster node) and all instances have their own copy of our scheduler.

But we need to have just one scheduler running among all cluster nodes otherwise we will have multiple copies of the same event.

Every application server has its own way to handle the "multiple scheduler instances" problem (for example see [link 2] for WebSphere) but, in general, it is required that the scheduler should be persistent when we are using a cluster.

# Should the scheduling interval be programmable at production?

Another important question to be answered: should we able to change the scheduling after the application has been deployed?

If the scheduling parameters (its frequency) are fixed, the automatic scheduler is the best solution because very simple to code: just one annotation (or few XML lines if you prefer the old way).

On the contrary, if the scheduler should be somehow configurable, the best solution is the programmatic scheduler which allow us to define all scheduler parameters during the application startup, reading them from a property file, a DB or any configuration solution we are using.

Remember:

* the automatic scheduler schedule is defined at build time
* the programmatic scheduler schedule is defined at application start time

# Automatic scheduler

It's very easy to define an automatic scheduler:

1. Create a singleton EJB executed at startup
2. Create a method which will be invoked at every scheduling event

Note: the complete code can be found in the article project [see link 3].

First step:

```java
@Startup
@Singleton
public class MyScheduler
```

The `@javax.ejb.Startup` annotation asks the EJB container to create the EJB (and so our scheduler) at application startup.

The `@javax.ejb.Singleton` annotation forces the EJB container to create just one instance.

Important: the scheduler is used by the application server (the EJB container); it should be never instantiated by the rest of the application code.

Then we need the method which will be invoked at scheduling events:

```java
@Schedule(/** scheduling parameters */)
public void doSomeThing() {..}
```

The method should be public and return void.

The `@javax.ejb.Schedule` annotation defines:

* the scheduling interval, in cron format [see link 4]
* the name of the scheduler (you could have many schedulers in the application)
* a persistent boolean flag which defines if the scheduler is persistent or not

For example:

``` Java
@Schedule(
    minute = "*/15",
    hour = "*",
    info = "15MinScheduler",
    persistent = false )
```

which defines a non persistent scheduler which runs every 15 minutes.

See `AutomaticPersistentScheduler` and `AutomaticNonPersistentScheduler` classes in the article project [link 3] for a complete example.

**Note**: there is also the `@Schedules` annotation [see link 1] which allows the define multiple @Schedule definitions.

It is useful when there are schedule requirements which cannot be expressed in a single cron definition.

# Programmatic scheduler

The programmatic scheduler is more complex to build but it give us the complete freedom
to define the scheduler parameters.

We have more steps:

1. Create a singleton EJB executed at startup
2. Lookup the TimerService resource
3. Create the scheduler at EJB initialization
4. Create a @Timeout method

First step is the same as the automatic scheduler:

```java
@Startup
@Singleton
public class MyScheduler
```

Then (second step) we need to lookup the application server timer service but the injection helps us:

```java
@Resource
private TimerService timerService;
```

At application startup, the EJB container will inject a `TimerService` instance which allow us
to interact with the Timer service. For example, we can list (and even delete) all scheduler
defined for the application.

In our case, the Timer service will be used to create the new scheduler as follows (third step):

```java
String minuteSchedule = "*/15";
String hourSchedule = "*";
ScheduleExpression schedule = new ScheduleExpression()
  .minute(minuteSchedule)
  .hour(hourSchedule);
```

The `javax.ejb.ScheduleExpression` defines the cron [see link 4] schedule like the `@Schedule` annotation.

The very important difference between `@Schedule` and `ScheduleExpression` is that the first one is fixed at build time: to change the schedule parameters (for example, from every 15min to every 30min) we need to change the class code and build and deploy again the application.

In the latter case (SchedulerExpression), the schedule parameters (in the example above
the variables minuteSchedule and hourSchedule ) can be defined and changed at
application startup, reading the minuteSchedule and hourSchedule from, for example,
a property file or a connected DBMS.

```java
TimerConfig timerConfig = new TimerConfig();
timerConfig.setInfo("ProgrammaticPersistentScheduler");
timerConfig.setPersistent(true);
```

The `javax.ejb.TimerConfig` gives us the option to define the name of the scheduler (`setInfo(String)`) and if it is persistent or not ( `setPersistent(boolean)` ) .

Using the `ScheduleExpression` and the `TimerConfig` instance, we can use the Timer service
to create the scheduler ( a calendar timer, to be more precise).

```java
timerService.createCalendarTimer(schedule, timerConfig);
```

The `createCalendarTime()` method returns a `javax.ejb.Timer` instance which can be used to interrogate the timer like when the next future event will happen or even to destroy
the scheduler.

The last step is to define a method in the class which will be invoked at every scheduling event

```java
@Timeout
public void doSomeThing() {..}
```

The method should be public and return void.

And we have our scheduler up and running.

# Conclusions

Java EE standard give us many options to define a scheduler which runs our code in a periodical and repetitive way. There is no need for additional project dependencies.

# Links

1. [Oracle Java EE6 Tutorial on the Timer Service API](http://docs.oracle.com/javaee/6/tutorial/doc/bnboy.html)
2. [IBM WebSphere 8.x Creating timers using the EJB timer service for enterprise beans](http://www.ibm.com/support/knowledgecenter/SSAW57_8.5.5/com.ibm.websphere.nd.doc/ae/tejb_timerserviceejb_enh.html)
3. [Article project on GitHub](https://github.com/gualtierotesta/ee-scheduler)
4. [Cron on Wikipedia](https://en.wikipedia.org/wiki/Cron)
