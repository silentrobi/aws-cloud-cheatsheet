# aws-cloud-cheatsheet

## AWS Role vs Policy:
**Policy** is for authorization.

**Role** is for access rights between different aws services. **_Example:_** aws Iot should have a defined role to access aws SNS.


## AWS IoT (Device shadow)
- A device shadow is a JSON, or a JavaScript notation doc
that is used to store and retrieve current state information
for a device.
- The Device Shadow Service maintains a shadow
for each device you can add to AWS IoT.
- You can use the shadow to get and set
the state of a device over MQTT or HTTP,
regardless of whether or not the device
is connected to the internet.
- Each device's shadow is uniquely identified
by the name of the corresponding thing.
AWS IoT provides three methods for working
with the device's shadow:
**Update, Get, and Delete.**
- An Update method creates a device's shadow
if it doesn't exist, or updates the contents
of a device's shadow with the data provided in the request.
The data is stored with the timestamped information
to indicate when it was last updated.
- Messages are sent to all subscribers
with the difference between the desired
or reported state. This is known as the delta.
- Things or apps that receive a message
can perform an action based on the difference
between desired or reported states.
Let's say, I can use a smartphone app
to control the thermostat from my home.
I have set the desired state to 72 degrees Fahrenheit.
This is stored in my thermostat's shadow file.
My husband, however, likes it cooler inside the house.
When my husband gets home and looks at the app
on his phone, the device is pulling the current temperature
from the shadow file to report to him
that I have it set to 72 degrees.
He can choose to lower the thermostat to 65 degrees.
This creates a change, or a delta,
between the shadow files.
The Get method retrieves the latest state
stored in the device's shadow,
in this case, my desired state of 72.
- The Delete methods delete a device's shadow,
including all of its contents.
This removes the JSON document from the data store.
You can't restore a device's shadow
that you've deleted, but you can create a new shadow
with the same name.
- The supported protocols for updating
a device's shadow are supported through both,
MQTT and a RESTful API over HTTPS.
Because MQTT is a publish and subscribe communication model,
AWS IoT implements a set of reserved topics.
Things or applications subscribe to these topics
before publishing on a request topic
in order to implement a requested response behavior.
So how did my thermostat get updated?
When my husband sent his request to lower the thermostat
to 65 degrees, his app used
the UpdateThingShadow RESTful API.
Or it could've used a publish or update topic.
That's how my thermostat thing knew to take action
by lowering the thermostat.
When you use the UpdateThingShadow,
only the fields specified under requests get the update.
You can also retrieve a shadow document.
If my app needed to retrieve my thermostat's shadow,
it would use the GetThingShadow RESTful API
or subscribe or publish to the Get topic.
This retrieves the entire document,
plus the delta between the desired and the reported states.
How does my thermostat know something has changed
and it needs to lower the thermostat?
It receives a notification from published messages
on one of two MQTT topics.
And these are reserved topics.
They are **$aws/things/thermostat1**, in this case,
or the name of your thing,
**/shadow/update/accepted**.
Or, instead of accepted, it's /delta.
The message sent to the update/delta topic is intended
for the thing who's state is being updated.
This message contained only the differences
between the desires and report sections
of the device's shadow document.
Upon receiving this message,
the device, or thermostat, in this case,
should decide whether to make the request a change.
If the device's state is changed,
it's going to publish its new current state.
So when my thermostat goes from 72 to 65 degrees,
it's going to publish it's new state
on the **$aws/things/thermostat/shadows/update** topic.
Devices and applications can subscribe
to either of these topics to be notified
when the state of the document is changed.
In an upcoming module, we will demo how to use shadows.
