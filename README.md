# Asyncronous Communication Methods
This guide has the objective to explain the most common methods of asynchronous communication and how to implement them.

## (Long) Polling (Server-to-Browser or Server-to-Server Comunication)

Your front-end (or back-end) has the responsibility of regularly asking your back-end if there is any fresh data. Hence the front-end (or back-end) will make the same call every few seconds/minutes. Sometimes one of those calls will have a fresh data to handle.

![1](./polling/images/1.png)

Long polling is different in the fact that the request is kept open by the server as long as possible until it eventually returns a fresh data or reaches a timeout.

![2](./polling/images/2.png)

Performance: Bandwidth use could be big, as the front-end creates lots of requests to the back-end even when the back-end could have no new data to return, which is waste of resources. Latency too could be big even with Long Polling, as the front-end has to wait for the next poll to get the most updated data. 

Example Architecture:

## Server-Sent Events (Server-to-Browser Communication)

Your front-end opens a long-lasting, uni-directional communication from your back-end to your front-end through the HTTP protocol. Here as well, the back-end can push a message as soon as necessary. If you want browser to server communication, you'll need do make a separated POST request.

![1](./server-sent-events/images/1.png)

Use cases: Stock prices updates, notifications, anything that needs uni-directional communication.

Example Architecture:

## WebSocket (Server-to-Browser and Browser-to-Server Communication)

Your front-end opens a long-lasting, bi-directional communication with your back-end through a WebSocket protocol. Thus, the back can push a message as soon as necessary and vice versa.

![1](./websocket/images/1.png)

Use cases: Google Wave type of applications, chat applications, anything that needs bi-directional communication.

Performance: Bandwidth and Latency are minimal, as only the necessary data will be exchanged and exactly when it's necessary. As your server could have thousands or even millions of open sockets, that could be a problem, but open sockets don't impact too much on CPU usage, being the Memory the main resource used. [Here](https://stackoverflow.com/questions/17448061/how-many-system-resources-will-be-held-for-keeping-1-000-000-websocket-open) we have a report of a modest server (8 vCPU and 68.4 GiB) taking up to 500k open socket connections.

Example Architecture:

![2](./websocket/images/2.png)

1. Start and keep track of the live WebSocket connection.
2. An event, like a SaaS hook, triggers a DB update.
3. A DynamoDB event then triggering a Lambda to notify the front-end of the updated data.

## Webhooks (Server to Server Communication)

In a uni-directional communication, a source server notifies a destination server that new events happened, as soon as necessary and through a HTTP POST request. Before the notification can be made, the source server must be configured with the events required by the destination and the destination callback URL, through a interface ou API. 

Example Architecture:

![example-architecture](./webhooks/images/example-architecture.drawio.png)

1. Source Server publishes the message in a AWS SNS topic.
2. AWS SNS pushes the message to the destination by using one of the fanout methods, like HTTP/S POST.
3. In case of a failure and after all retries, the message is published in a SQS queue for the desired error handling.

## Pub/Sub Pattern through a Message Broker (Server to Server Communication)

In a uni-directional communication, a producer publishes a message in a topic or queue hosted in a message broker, and one or many consumer services subscribe to this topic or queue. Depending on the message broker used, a push or pull strategy is used. Kafka for example uses a pull strategy, meaning that the consumer service makes requests periodically to the message brokers requesting new data, which is similar do the Polling method explained before. RabbitMQ for example uses a push strategy, meaning that RabbitMQ calls the consumer service when new data is available, which is similar do the Webhook method explained before.

Example Architecture:

## ~~HTTP/2 Push (Server-to-Browser Communication)~~ :skull:	

Developers from Chromium [removed](https://groups.google.com/a/chromium.org/g/blink-dev/c/K3rYLvmQUBY/m/0o4J1GEjAgAJ) in 2021 the support for this feature due to low usage and high maintenance cost. As Chrome is based on Chromium and it has 70% of market share (2022), they are effectively killing HTTP/2 Push. As HTTP/2 Push is not mandatory, Chromium keeps being HTTP/2 compliant.

## Bibliography

https://medium.com/serverless-transformation/asynchronous-client-interaction-in-aws-serverless-polling-websocket-server-sent-events-or-acf10167cc67

https://aws.amazon.com/pt/blogs/compute/from-poll-to-push-transform-apis-using-amazon-api-gateway-rest-apis-and-websockets/

http://www.diva-portal.se/smash/get/diva2:1133465/FULLTEXT01.pdf
