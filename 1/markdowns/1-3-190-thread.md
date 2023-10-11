
# Post \#68477402 [Link](https://stackoverflow.com/questions/68477402/)

## Listen to mqtt topics with django channels and celery

**Vote**: 2 (631/702) **Views**: 2728 (581/702) 

**Internal ID** \#1-3-190

Created at 2021-07-21 23:04:08

Tags: `django` `websocket` `celery` `mqtt` `django-channels`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I would like a way to integrate django with mqtt and for that the first thing that came in my mind was using django-channels and an mqtt broker that supports mqtt over web sockets, so I could communicate directly between the broker and django-channels.
However, I did not found a way to start a websocket client from django, and acording to this [link](https://forum.djangoproject.com/t/add-a-websocket-client-to-django-not-a-server/2916/8) it's not possible.
Since I'm also starting to study task queues I wonder if it would be a good practice to start an mqtt client using paho-mqtt and then run that in a separate process using celery. This process would then forward the messages receives by the broker to django channels through websockets, this way I could also communicate with the client process, to publish data or stop the mqtt client when needed, and all that directly from django.
I'm a little skeptical about this idea since I also read that process run in celery should not take too long to complete, and in this case that's exactly what I want to do.
So my question is, how much of a bad idea that is? Is there any other option to directly integrate django with mqtt?
*


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-07-28 00:08:45

------------

I found a better way that does not need to use celery.
I simply started a mqtt client on app/apps.py on the ready method, so a client will be started everytime I run the application. From here I can communicate with other parts of the system using django-channels or signals.
apps.py:
```
from django.apps import AppConfig
from threading import Thread
import paho.mqtt.client as mqtt


class MqttClient(Thread):
    def __init__(self, broker, port, timeout, topics):
    super(MqttClient, self).__init__()
    self.client = mqtt.Client()
    self.broker = broker
    self.port = port
    self.timeout = timeout
    self.topics = topics
    self.total_messages = 0

#  run method override from Thread class
def run(self):
    self.connect_to_broker()

def connect_to_broker(self):
    self.client.on_connect = self.on_connect
    self.client.on_message = self.on_message
    self.client.connect(self.broker, self.port, self.timeout)
    self.client.loop_forever()

# The callback for when a PUBLISH message is received from the server.
def on_message(self, client, userdata, msg):
    self.total_messages = self.total_messages + 1
    print(str(msg.payload) + "Total: {}".format(self.total_messages))

# The callback for when the client receives a CONNACK response from the server.
def on_connect(self, client, userdata, flags, rc):
    #  Subscribe to a list of topics using a lock to guarantee that a topic is only subscribed once
    for topic in self.topics:
        client.subscribe(topic)


class CoreConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'core'

def ready(self):
    MqttClient("192.168.0.165", 1883, 60, ["teste/01"]).start()
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-27 18:12:50

------------

If you are using ASGI in your Django application you can use [MQTTAsgi](https://pypi.org/project/mqttasgi/). Full disclosure I'm the author of MQTTAsgi.
It's a complete protocol server for Django and MQTT.
To utilize the mqtt protocol server you can run your application, first you need to create a MQTT consumer:
```
from mqttasgi.consumers import MqttConsumer
class MyMqttConsumer(MqttConsumer):

    async def connect(self):
        await self.subscribe('my/testing/topic', 2)

    async def receive(self, mqtt_message):
        print('Received a message at topic:', mqtt_mesage['topic'])
        print('With payload', mqtt_message['payload'])
        print('And QOS:', mqtt_message['qos'])
        pass

    async def disconnect(self):
        await self.unsubscribe('my/testing/topic')
```

Then you should add this protocol to the protocol router:
```
application = ProtocolTypeRouter({
      'websocket': AllowedHostsOriginValidator(URLRouter([
          url('.*', WebsocketConsumer)
      ])),
      'mqtt': MyMqttConsumer,
      ....
    })
```

Then you can run the mqtt protocol server with*:
```
mqttasgi -H localhost -p 1883 my_application.asgi:application
```

*Assuming the broker is in localhost and port 1883.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-01-09 19:08:21

------------

I wanted to solve this problem too but found no good solutions out there that really fitted the Channels architecture (though MQTTAsgi came close but it uses paho-mqtt and doesn't fully use the Channels-layer system).
I created: [https://pypi.org/project/chanmqttproxy/](https://pypi.org/project/chanmqttproxy/)
(src at [https://github.com/lbt/channels-mqtt-proxy](https://github.com/lbt/channels-mqtt-proxy))
Essentially it's a fully async Channels 3 proxy to MQTT that allows publishing and subscribing. The documentation show how to extend the standard Channels tutorial so chat messages are seen on MQTT topics - and can be sent from MQTT topics to all websocket browser clients.
I don't know it this is what the OP wants as far as listening to MQTT topics goes but for the general case I think this is a good solution.


------------
    
    