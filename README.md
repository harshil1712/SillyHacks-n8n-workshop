# n8n Workshop

This repository contains all the prerequisites for the [n8n](https://n8n.io) workshop.

In this workshop, we will learn
- What is automation?
- Benefits of automation
- Components of n8n
- How to build a workflow that will notify us the amount of time we spend in online classes.
- How to implement automation in factories, and monitor machines' health. 

## Getting Started

#### Prerequisities

Please complete the following steps to have a wonderful learning experience:
- Install [Node.js](https://nodejs.org/en/)
- Install [n8n](https://docs.n8n.io/getting-started/quickstart.html#install-with-npm)
- **Optional**: You can run n8n in Docker, although we will be running n8n using npm in the workshop. To run n8n in Docker, follow the instructions mentioned in the [documentation](https://docs.n8n.io/getting-started/quickstart.html#run-with-docker)

## Time Spen in Online Classes

We will build a workflow that will calculate the time we spend in our online classes in a week and send us an SMS using Twilio. 

### Prerequisites

- Google Calendar credentials. Follow the [documentation](https://docs.n8n.io/credentials/google/#google) to learn how to get the credentials.
	> **NOTE:** Make sure to enable the Google Calendar API in the Google Cloud Console.
- Create an account on [Twilio](www.twilio.com/referral/7LQNh3) and [get a trial number](https://www.twilio.com/docs/usage/tutorials/how-to-use-your-free-trial-account#get-your-first-twilio-phone-number)

### Code Snippets

#### Start and End of Week

```js
var curr = new Date;
var first = (curr.getDate() - curr.getDay()) +1;
var last = first + 4;

var firstday = new Date(curr.setDate(first));
var lastday = new Date(curr.setDate(last));

beginning_week = new Date(firstday.setHours(0,0,0,0));
ending_week = new Date(lastday.setHours(23,59,59,99));

items[0].json.from = beginning_week.toISOString();
items[0].json.to = ending_week.toISOString();

return items;
```

#### Total Class Duration

```js
var new_items = [];
var total_classes_time = 0;

for (var i=0 ; i<items.length ; i++) {
  var duration = 0;
  duration = new Date(items[i].json.end.dateTime).getTime() - new Date(items[i].json.start.dateTime).getTime();
  total_classes_time = total_classes_time + duration;
}

new_items.push({json: {total_classes_time: (total_classes_time/(1000*60*60)).toFixed(1)}});

return new_items;
```
> You can read more about it in the article: [Tracking Time Spent in Meetings With Google Calendar, Twilio, and n8n 🗓](https://medium.com/n8n-io/tracking-time-spent-in-meetings-with-google-calendar-twilio-and-n8n-a5d00f77da8c)

## Smart Factory Automation using IoT and Sensor Data

We will build two separate workflows. The first workflow will send machines' information like sensors to the ActiveMQ queue.
The second workflow will store this information in CrateDB. It will also create an incident report if the temperature of the machine is more than 50C.

### Prerequisites

- Install [ActiveMQ](https://activemq.apache.org/getting-started)
- Get [credentials for PagerDuty](https://docs.n8n.io/credentials/pagerDuty/#pagerduty) 
- Install [CrateDB](https://crate.io/docs/crate/tutorials/en/latest/install-run/index.html)

### Code Snippets

#### Generate Random Temperature

`{{Math.floor(Math.random() * 100);}}`

#### Generate Random Uptime

`{{Math.floor(Math.random() * 100);}}`

#### Generate timestamp

`{{Date.now();}}`

#### Data Enrichment (°C to °F)

```js
temp_fahrenheit = (items[0].json.body.temperature_celsius * 1.8) + 32;
items[0].json.temperature_fahrenheit = temp_fahrenheit;
return items;
```

### CrateDB Commands

#### Create Machine Data Table

`CREATE TABLE machine_data(machine_name STRING, machine_uptime INT, temperature_fahrenheit INT, temperature_celsius INT, time_stamp TIMESTAMP);`

#### Create Incident Data Table

`CREATE TABLE incident_data (incident_id TEXT, html_url TEXT, incident_timestamp TIMESTAMP);`

> You can read more about it in the article: [Tracking Time Spent in Meetings With Google Calendar, Twilio, and n8n 🗓](https://medium.com/n8n-io/tracking-time-spent-in-meetings-with-google-calendar-twilio-and-n8n-a5d00f77da8c)

If you have any questions please ask them on our [community forum](https://community.n8n.io/).
