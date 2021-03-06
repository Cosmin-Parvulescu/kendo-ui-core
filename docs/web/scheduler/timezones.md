---
title: Timezones
page_title: Overview of the Scheduler timezone option
description: Overview of the Scheduler timezone option
---

# Timezones Overview
A ['time zone'](http://www.timeanddate.com/time/time-zones.html) refers to any of the 24 regions loosely divided by longitude, where the same standard time is kept.

## Timezones and the `Date` Object
In JavaScript the `Date` object represents a single moment in time, measured in the number of milliseconds since 01 January, 1970 UTC. The `Date` objects are [always created using the current browser timezone offset](http://www.ecma-international.org/ecma-262/6.0/#sec-localtime). Construct a new `Date` object in the following way:

    new Date(2014,1,1,12,0,0);

The result in the browser console shows that your current timezone offset is already applied:

    Sat Feb 01 2014 12:00:00 GMT+0200 (FLE Standard Time)

If you try to convert the previously created JavaScript `Date` to a JSON string using the [JSON.stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) method , the result is:

    ""2014-02-01T10:00:00.000Z""

## Timezones and the Scheduler
You can define a [timezone](/api/javascript/ui/scheduler#configuration-timezone) option to the Kendo UI Scheduler. It tells the widget what timezone to apply when displaying the appointment dates. This option is **not set** by default and, therefore, the [event dates will be created based on the current client timezone offset](/web/scheduler/timezones#timezone-option-is-not-set). This means that **users from different timezones will see different start and end times**. On the other hand, setting the Scheduler [timezone](/api/javascript/ui/scheduler#configuration-timezone) will force the widget to show the **same start and end times** regardless of the user's timezone.

> **Important**  

> When the remote binding is used, the Kendo UI Scheduler **expects to receive UTC dates**. Respectively, it will send them back to the server in UTC. The **service in use is responsible for keeping the dates in UTC**, without offsetting them against its local time.

> When the Scheduler is bound to a remote service, it is recommended that the **timezone** option is always set, for example, to "Etc/UTC".

> When the **timezone** option of the Scheduler is not set, the current system timezone offset is used.

> The recommended `Date` format for sending and receiving Scheduler event dates is [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) with a **Z** zone designator (UTC date). The same format is used by the [JSON.stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) method, which converts JavaScript `Date` objects to JSON strings.

Based on the specifics of the JavaScript `Date` object explained above, the Scheduler needs to adjust the dates of the events when reading and sending them.

### Read Events from a Remote Source or a Local Array

1.  [SchedulerEvent](/api/framework/SchedulerEvent) instances are created, where start/end dates are instatiated as JavaScript `Date` objects.
During the process the [dates will be offset against the local time](http://www.ecma-international.org/ecma-262/6.0/#sec-localtime). 

2. If the [timezone](/api/javascript/ui/scheduler#configuration-timezone) option is defined, the widget will remove the local timezone offset, converting dates to UTC.
Then it will apply the defined timezone value (e.g., 'America/New_York').

### Send Events to a Remote Service

1. If the [timezone](/api/javascript/ui/scheduler#configuration-timezone) option is defined, the widget will remove the applied timezone offset (e.g., 'America/New_York'), converting dates to UTC. Then it will apply the local time.

2. [SchedulerEvent](/api/framework/SchedulerEvent) instances will be serialized using [JSON.stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). In the process, the dates will be converted to UTC and then formatted according to the [ISO8601 format](https://en.wikipedia.org/wiki/ISO_8601).

## Scheduler without the Timezone Option

If you choose not to define a timezone option, your system timezone settings will apply by deafault. Yet, you can customize them so you can deliver an appointment date either in the local offset, or in UTC. 

If you run the first example below, the Scheduler will show the dates in the local timezone offset. This means that the event will be displayed as scheduled for 2:00pm, regardless of your location - whether you are in the **Europe/Berlin** timezone, for instance, or in the **Europe/Sofia** one. 

###### Example - bind the Scheduler to local dates when the `timezone` option is not set
````html
    <div id="scheduler"></div>
    <script>
    $(function() {
        $("#scheduler").kendoScheduler({
            date: new Date("2013/6/13"),
            startTime: new Date("2013/6/13 10:00"),
            endTime: new Date("2013/6/13 23:00"),
            height: 600,
            views: ["day"],
            editable: false,
            dataSource: [
                {
                    title: "The Internship",
                    image: "../content/web/scheduler/the-internship.jpg",
                    imdb: "http://www.imdb.com/title/tt2234155/",
                    start: new Date("2013/6/13 14:00"),
                    end: new Date("2013/6/13 15:30")
                }
            ]
        });
    });
    </script>
````

If you run the second example below, the Scheduler will show the dates according to the UTC convention. This means that the event will be displayed as scheduled for 4:00pm if you are in the **Europe/Berlin** timezone, for instance, while if you are in the **Europe/Sofia** timezone, the event will appear as scheduled for 5:00pm.  

###### Example - bind the Scheduler to UTC dates when the `timezone` option is not set
````html
    <div id="scheduler"></div>
    <script>
    $(function() {
        $("#scheduler").kendoScheduler({
            date: new Date("2013/6/13"),
            startTime: new Date("2013/6/13 10:00"),
            endTime: new Date("2013/6/13 23:00"),
            height: 600,
            views: ["day"],
            editable: false,
            dataSource: [
                {
                    title: "The Internship",
                    image: "../content/web/scheduler/the-internship.jpg",
                    imdb: "http://www.imdb.com/title/tt2234155/",
                    start: new Date("2013-06-13T14:00:00.000Z"),
                    end: new Date("2013-06-13T15:30:00.000Z")
                }
            ]
        });
    });
    </script>
````