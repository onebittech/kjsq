# kjsq

Produce a stream of kafka messages.

## How to use

```bash
$ npm install
$ node main.js
```

That starts a web server on http://127.0.0.1:3000

#### Env variables

```
DBPATH: Path to sqlite db file (default: streams.db)
PORT: Webserver port (default: 3000)
```

### Stream of messages

Is an array that can contain objects, numbers or strings.

If the element is an object it should have the following structure:

```js
// Object
{
    "key": 231, // [optional] used for partitioning
    "payload": "body" // message to send
}
```

If the element is number, then the producer waits `n` ms to send the next message.

If no key is specified the producer defaults to "0".

#### Example

```js
[
  {
    key: 1,
    payload: {
      id: 1,
      name: "John Doe",
    },
  },
  10000,
  {
    key: 2,
    payload: {
      id: 2,
      name: "Alice",
    },
  },
  3000,
  "Bob",
];
```

The first message is sent with key: 1 and the payload indicated. Then the producer sleeps for 10 seconds and sends the second message with key: 2. Then it halts for 3 seconds and sends the message "Bob" with key: "0".

Try it yourself!

![](img/screenshots/test.png)

### API

If you prefer you can use the REST api instead of the web.

POST `/api/streams/produce`

```js
POST `/api/streams/produce` {
    "kafkaHost": "broker1:9092,broker2:9092",
    "topic": "test_topic",
    "messages": [
        {
            "key": 1,
            "payload": {
                "id": 1,
                "name": "John Doe"
            }
        },
        10000,
        {
            "key": 2,
            "payload": {
                "id": 2,
                "name": "Alice"
            }
        },
        3000,
        "Bob"
    ]
}

Response
{
    "id": "d4c93279-b3ab-4695-9688-76a2de184e28",
    "state": {
        "status": "not_started",
        "messagesAcked": []
    }
}
```

POST `/api/streams/save`

```js
POST `/api/streams/save` {
    "name": "test",
    "kafkaHost": "broker1:9092,broker2:9092",
    "topic": "test_topic",
    "messages": [
        {
            "key": 1,
            "payload": {
                "id": 1,
                "name": "John Doe"
            }
        },
        10000,
        {
            "key": 2,
            "payload": {
                "id": 2,
                "name": "Alice"
            }
        },
        3000,
        "Bob"
    ]
}

Response
"ok"
```

GET `/api/streams/:name`

```js
GET `/api/streams/test`

Response
{
    "kafkaHost": "broker1:9092,broker2:9092",
    "topic": "test_topic",
    "messages": [
        {
            "key": 1,
            "payload": {
                "id": 1,
                "name": "John Doe"
            }
        },
        10000,
        {
            "key": 2,
            "payload": {
                "id": 2,
                "name": "Alice"
            }
        },
        3000,
        "Bob"
    ]
}
```


GET `/api/streams/state/:id`

```js
GET `/api/streams/state/d4c93279-b3ab-4695-9688-76a2de184e28`

Response
{
    "status": "done",
    "messagesAcked": [0, 2]
}
```
