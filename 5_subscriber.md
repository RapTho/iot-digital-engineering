# Subscriber App

## Introduction

This is a Python subscriber app that connects to a Mosquitto broker and subscribes to a specific topic. The app receives messages from the broker and stores them in a Cloudant database.

## Prerequisites

- Mosquitto broker deployed on IBM Code Engine (previous task)
- Python 3.8 or later
- `paho-mqtt` library for MQTT communication
- `requests` library for HTTP requests
- `python-dotenv` library for loading environment variables from a .env file

## Task

Your task is to develop a Python subscriber app that:

- Subscribes to a topic on the previously deployed Mosquitto broker on port 8083 (WebSocket)
- Receives messages from the broker and stores them in a Cloudant database
- Enforce a message schema

## Hints

- Use the paho-mqtt library to connect to the Mosquitto broker and subscribe to a topic.
- Use the requests library to send HTTP requests to the Cloudant database API.
- Use basic authentication to authenticate with the Cloudant database.
- Load environment variables from a .env file using the python-dotenv library.
- Use a try-except block to handle errors when connecting to the Mosquitto broker or publishing messages.

## Cloudant Database API

The Cloudant database API provides a RESTful interface for interacting with the database. You can use the `requests` library to send HTTP requests to the API.

To create a new document, send a POST request to https://<cloudant-account>.cloudant.com/<database-name>.
To retrieve a document, send a GET request to https://<cloudant-account>.cloudant.com/<database-name>/<document-id>.

## Basic Authentication

To authenticate with the Cloudant database, use basic authentication. You can pass the username and password in the Authorization header of the HTTP request.

`Authorization: Basic <base64-encoded-username-and-password>`

Libraries like `requests` have ways of doing the base64 encoding for you.

## Example Code

Here is an example of how you can use the paho-mqtt library to connect to the Mosquitto broker and subscribe to a topic. More documentation is available [here](https://eclipse.dev/paho/files/paho.mqtt.python/html/client.html)

```python
import ssl
import paho.mqtt.client as mqtt

client = mqtt.Client(transport="websockets") # or mqtt.CallbackAPIVersion.VERSION2
client.tls_set(None, cert_reqs=ssl.CERT_NONE) # if you want to ignore TLS
client.username_pw_set("myBrokerUsername", "myBrokerPassword")
client.connect("mosquitto-broker-url", 8083)
client.subscribe("student1/topic", qos=1) # qos=1 is at least once
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

Here is an example of how you can use the requests library to send a POST request to the Cloudant database API:

```python
import requests

url = "https://<cloudant-account>.cloudant.com/<database-name>"
auth = ("myCloudantUsername", "myCloudantPassword")
headers = {"Content-Type": "application/json"}
data = {"key": "value"}
response = requests.post(url, auth=auth, headers=headers, json=data)
```

## Environment variables

Load environment variables from a .env file using the python-dotenv library. For example:

```python
import os
from dotenv import load_dotenv

load_dotenv()
BROKER_URL = os.getenv("BROKER_URL")
CLOUDANT_ACCOUNT = os.getenv("CLOUDANT_ACCOUNT")
CLOUDANT_DATABASE = os.getenv("CLOUDANT_DATABASE")
```
