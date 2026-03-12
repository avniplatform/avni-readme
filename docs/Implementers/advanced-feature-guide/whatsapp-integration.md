---
title: Whatsapp integration
excerpt: Talk to your beneficiaries through Whatsapp
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Purpose

Being able to communicate to your beneficiaries through Whatsapp is very powerful in many scenarios of field work. It can be used to provide reminders for important events or your field-worker's visit. You can provide nudges for those who need to be follow a routine.

#### Use cases

1. Remind everyone in a village about an upcoming Village Health and Nutrition Day (VHND)
2. Remind pregnant mothers about an upcoming ANC visit
3. Send motivational videos to all users enrolled into a de addiction programme
4. Inform a student about an upcoming interview
5. Remind a teacher and their principal about an observation session the next day
6. Remind field-workers to submit their monthly reports

## How it works

<Image align="center" width="350px" src="https://files.readme.io/5faa756-Screenshot_2023-10-13_at_1.28.13_PM.png" />

Avni uses [Glific](https://glific.org/) to send Whatsapp messages to beneficiaries and users. Glific is not just a way to connect to Whatsapp. It also provides rich communication between beneficiaries through chatbots. Glific also provides a neat way for two-way communication between beneficiaries.

If an organisation uses Avni, a lot of information about the user is available in Avni. More importantly, Avni also understands when an important event has either happened, or is about to happen. Due to this reason, Avni is well-placed to provide reminders and nudges where they are necessary. Avni-Glific integration has three pieces.

1. Sending of a message on a trigger. Triggers can be registration of a user, enrolment into a program or completion of a visit. When such a trigger happens, Avni can send a message to Glific at a scheduled time.
2. Bulk send of messages for a group of users. Sometimes, the organisation needs to share a piece of information to their entire set of beneficiaries, or a sub-group within it. This can be done through a web-based mechanism
3. Sending of messages to an individual and viewing sent messages on the Avni field-app

**PS**: Interested organisations must create an account in Glific and configure Glific in Avni in order to use this functionality

### Sending messages on a trigger

In the application designer, there is an option within subject types, programs and encounter types to provide

1. A schedule rule that specifies the time in which the message needs to be sent (once the event is triggered)
2. A message rule that helps figure out the parameters required for the message

The messages are scheduled when the event is synced to the server (or at save if you are using the Data Entry Application).

#### Setup

First, you need to enable messaging in the Organisation Settings on the Admin page

![](https://files.readme.io/69637ed-image.png)

Next, provide details to connect to Glific into the external_api table. This currently does not have a UI. Entries need to be made in the format.

`insert into external_system_config(organisation_id, version, created_by_id, last_modified_by_id, created_date_time, last_modified_date_time, system_name, config)
values (2, 1, 1, 1, now(), now(), 'Glific', '{"baseUrl": "API URI field value from password manager", "phone": "Login for API field value from password manager", "password": "Password field value from password manager", "avniSystemUser": "maha@test"}'::jsonb);`

Ensure that atleast one of your form fields is marked as a phone number. This can be done by going to a **Text** or **PhoneNumber** concept, and marking its "contact_number" value to "yes". Use this concept in the form to register the subject. It is to the value of this field, that the whatsapp message will be sent.

<Image align="center" border={true} src="https://files.readme.io/4f4f325-Screenshot_2022-11-14_at_7.07.26_PM.png" className="border" />

Once this configuration is complete, go over to the App Designer and create rules to send messages.

There are 2 rules to be configured here. The first (Schedule rule) determines the time when the message needs to be sent. The second (Message rule) gives the parameters on the message. These parameters can be fetched from any part of the entity. The message rule should return the computed array of parameters for this entity.

You can choose to send the message to either the subject/beneficiary or the user who made the entry.

You can also have multiple rules defined for the same trigger. In this case, you can have a message to be sent immediately, and another to be sent after a week.

You also have the ability to not schedule a message if required by setting the `shouldSend` parameter in the response of the Schedule Rule to false. If this parameter is not set, it defaults to true i.e. the message will be scheduled.

![](https://files.readme.io/cbed815-image.png)For sending messages, for the entities(subject/encounter/enrolment) created in mobile app, the created data in mobile app should have been synced to the server.

### Bulk send of messages for a group of users

![](https://files.readme.io/958a89d-image.png)

Under Broadcast section of the Avni web application, you will now see a new option - WhatsApp. This can be used to send messages to beneficiaries, users or groups.

Check [this](https://drive.google.com/file/d/1J2qt1s2ltJoOjQoWXdmq1GZA171usPsq/view?usp=share_link) video out, to know how to manage groups and send messages to groups.

Currently only name of the subject/user is supported as dynamic parameter. To use this, enter `@name` in the parameter input field.

### Triggering Glific Flows

In addition to HSM messages, Avni now supports triggering Glific flows (workflows) for individual contacts. This is useful for sending automated nudges or multi-step conversational flows to users or subjects.

How flow triggering works

The integration-service calls POST `/web/message/startFlowForContact` with the receiver details and a Glific flow ID. Avni then:

1. Creates a FlowRequest record in the flow_request_queue table (acts as an audit log)
2. Resolves the receiver's phone number from the User or Subject record
3. Gets or creates the corresponding Glific contact using the phone number
4. Triggers the specified flow in Glific using the Glific API
5. Updates the FlowRequest record with the delivery status

Flow requests are only supported for User and Subject receiver types .

The flow_request_queue table provides a complete audit trail of all flow trigger attempts, including their delivery status. This enables:

* Preventing duplicate flow triggers by checking history
* Monitoring and reporting on flow delivery success rates
* Debugging failed flow attempts

Endpoint : POST /web/message/startFlowForContact

Sample Request Body:
```json
{ 
"receiverId": "exampleReceiverId", //user id
"receiverType": "User", // User or Subject
"flowId": "exampleFlowId", // Glific flow id
"parameters": ["param1", "param2"]
}
```

Sample Response Body:

```json
{
"messageDeliveryStatus": "Sent", //Sent / Failed / NotSentNoPhoneNumberInAvni / NotSentInvalidPhoneNumberInAvni / NotSent / PartiallySent
"errorMessage": null
}
```

<br />

### Limitations

Currently, only HSM messages is in scope of this integration. Eventually, the integration will also include triggering workflows in Glific.

### Viewing messages for a Subject, User or a Group

Currently, through the web-app, we are able to look at the Sent and Scheduled messages for a Subject, User or a Group, with a caveat that only the latest 100 messages are fetched. This is due to performance constraints in Avni-Glific data fetch.

### Debugging

* To understand the db design for debugging, checkout [this](https://avni.readme.io/docs/understanding-whatsapp-integration-tables#to-understand-the-status-of-automatic-messages) and [this](https://dbdiagram.io/d/63bb840e7d39e42284e9a83d) link.
* To view the logs printed when executing message rule or schedule rule execute the following after ssh-ing into the server machine:

  ```
  sudo su - rules-server-user
  pm2 logs --lines 1000
  ```
* If expected message not delivered, can check in the glific webapp (credentials in keeweb) to see if any error displayed beside the message in the chat. Sometimes say, when the phone no is invalid or the expected no of parameters not mentioned, an error message stating the reason for inability to deliver the message gets displayed in the chat.
* The background job that runs for sending messages from message_request_queue table is currently configured to run once in 5 mins.
* In order to handle scenarios where either system is unavailable, the background job retries messages that were not successfully sent. Messages older than a certain period are not retried. Default: 4 (days); Configuration: `AVNI_SEND_MESSAGES_SCHEDULED_SINCE_DAYS`
