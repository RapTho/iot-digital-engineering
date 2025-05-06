# Publisher App

## Introduction

This is a Python publisher app that connects to a Mosquitto broker and publishes messages to a specific topic. The app uses the paho-mqtt library to communicate with the Mosquitto broker.

## Prerequisites

- Mosquitto broker deployed on IBM Code Engine (previous task)
- Python 3.8 or later
- `paho-mqtt` library for MQTT communication
- `jsonschema` library for validating JSON messages
- `python-dotenv` library for loading environment variables from a .env file

## Task

Your task is to develop a Python publisher app that:

- Connects to the Mosquitto broker on port 8083 (WebSocket)
- Validates the JSON messages using a schema
- Publishes messages to a specific topic (e.g., student1/topic)

## Hints

- Use the paho-mqtt library to connect to the Mosquitto broker and publish messages to a topic.
- Use the jsonschema library to validate the JSON messages using a schema.
- Use the python-dotenv library to load environment variables from a .env file.
- Use a try-except block to handle errors when connecting to the Mosquitto broker or publishing messages.

## Schema validation

The schema validation is used to ensure that the JSON messages published to the Mosquitto broker conform to a specific format. You can use the `jsonschema` library to validate the JSON messages.

Define a schema using the jsonschema library.
Use the validate function to validate the JSON messages against the schema.

## Example Code

Here is an example of how you can use the paho-mqtt library to connect to the Mosquitto broker and subscribe to a topic. More documentation is available [here](https://eclipse.dev/paho/files/paho.mqtt.python/html/client.html)

```python
import ssl
import paho.mqtt.client as mqtt

topic = "topic1"
client = mqtt.Client(transport="websockets") # or mqtt.CallbackAPIVersion.VERSION2
client.tls_set(None, cert_reqs=ssl.CERT_NONE) # if you want to ignore TLS
client.username_pw_set("myBrokerUsername", "myBrokerPassword")
client.connect("mosquitto-broker-url", 8083)
client.subscribe(topic, qos=1) # qos=1 is at least once
```

Validating a message

```python
import jsonschema

schema = {
    "type": "object",
    "properties": {
        "id": {"type": "integer"},
        "message": {"type": "string"}
    },
    "required": ["id", "message"]
}

message = {"id": 1, "message": "Hello, world!"}
jsonschema.validate(message, schema)
```

publish a message

```python
from datetime import datetime

topic = "topic1"
result = client.publish(topic, payload=json.dumps(message), qos=1)
result_code = result.rc
if result_code != mqtt.MQTT_ERR_SUCCESS:
    raise Exception(f"[{datetime.now()}] Failed to publish message '{message}' to topic ${topic}. Result code: {result_code}")
else:
    print(f"[{datetime.now()}] Message '{message}' sent to topic ${topic} successfully.")
```

## Environment variables

Load environment variables from a .env file using the python-dotenv library. For example:

```python
import os
from dotenv import load_dotenv

load_dotenv()
BROKER_URL = os.getenv("BROKER_URL")
USERNAME = os.getenv("USERNAME")
PASSWORD = os.getenv("PASSWORD")
```
