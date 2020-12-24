The indi_mr package
===================

.. automodule:: indi_mr

Functions in indi_mr
^^^^^^^^^^^^^^^^^^^^

These first three functions are used to create named tuples.

For example::

    from indi_mr import indi_server, redis_server, mqtt_server

    indi_host = indi_server(host='localhost', port=7624)
    redis_host = redis_server(host='localhost', port=6379)
    mqtt_host = mqtt_server(host='localhost', port=1883)

These variables 'indi_host', 'redis_host' and 'mqtt_host' are then used as inputs to further functions which require definitions of the hosts.

.. autofunction:: indi_mr.indi_server

.. autofunction:: indi_mr.redis_server

.. autofunction:: indi_mr.mqtt_server


The to_indi_topic should be a string, the same string should be used for every connection, so, for example, clients will send data
with this topic, and servers/drivers will subscribe to the topic to receive that data.

Similarly the from_indi_topic should be another, different string, again used for every connection.

The same rule follows for the snoop_control_topic and snoop_data_topic.

The tuples created by the above functions are then used as parameters for the following functions.

.. _inditoredis:

indi_mr.inditoredis
^^^^^^^^^^^^^^^^^^^

An INDI client which reads data from indiserver (port 7624) converts the XML to redis key-value storage. In the other direction,
subscribes to XML data published via Redis and transmits to indiserver. Enables a GUI or web client to communicate to indiserver
purely via redis.

.. autofunction:: indi_mr.inditoredis

For further information on the log_lengths parameter see :ref:`log_lengths`.

So a minimal script using defaults to run inditoredis could be::

    from indi_mr import inditoredis, indi_server, redis_server

    # define the hosts/ports where servers are listenning, these functions return named tuples
    # which are required as arguments to inditoredis().

    indi_host = indi_server(host='localhost', port=7624)
    redis_host = redis_server(host='localhost', port=6379)

    # blocking call which runs the service, communicating between indiserver and redis

    inditoredis(indi_host, redis_host, blob_folder='/path/to/blob_folder')

    # Set the blob_folder to a directory of your choice

Note that BLOB's - Binary Large Objects, such as images are not stored in redis, but are set into a directory of your choice defined by the blob_folder argument.


.. _driverstoredis:

indi_mr.driverstoredis
^^^^^^^^^^^^^^^^^^^^^^

.. autofunction:: indi_mr.driverstoredis

For further information on the log_lengths parameter see :ref:`log_lengths`.

A minimal script using defaults to run driverstoredis could be::

    from indi_mr import driverstoredis, redis_server

    redis_host = redis_server(host='localhost', port=6379)

    # blocking call which runs the service, communicating between the drivers and redis

    driverstoredis(["indi_simulator_telescope", "indi_simulator_ccd"], redis_host, blob_folder='/path/to/blob_folder')

    # The list of two simulated drivers shown above should be replaced by a list of your own drivers.


MQTT Networking
^^^^^^^^^^^^^^^

As an alternative to inditoredis or driverstoredis, the functions below provide communications via an MQTT server.

The MQTT network transmits data between drivers and clients, and also sends snooping data between devices.
Each of the functions requires a unique mqtt_id which is a string identifying the attachment to the MQTT network.
Each function can also take a 'subscribe_list' which indicates the remote connections it 'listens to'.

For example, the functions mqtttoredis and mqtttoport are 'client connections' and their subscribe_list should contain
the mqtt_id's of the driver functions (inditomqtt and driverstomqtt) which those clients wish to connect to.

Similarly inditomqtt and driverstomqtt should have subscribe_list's which contain the client mqtt_id's which connect to them.
For most simple cases, the lists can all be left empty, which, as default, allows all connections - so the clients will see all attached devices.


.. _inditomqtt:

indi_mr.inditomqtt
^^^^^^^^^^^^^^^^^^

Intended to be run on a device with indiserver, appropriate drivers and attached instruments.

Receives/transmitts XML data between indiserver on port 7624 and an MQTT server which ultimately sends data to the remote web/gui server.

.. autofunction:: indi_mr.inditomqtt

Example Python script running on the machine with indiserver and the connected instruments::

    from indi_mr import inditomqtt, indi_server, mqtt_server

    # define the hosts/ports where servers are listenning, these functions return named tuples.

    indi_host = indi_server(host='localhost', port=7624)
    mqtt_host = mqtt_server(host='10.34.167.1', port=1883)

    # blocking call which runs the service, communicating between indiserver and mqtt

    inditomqtt(indi_host, 'indi_server01', mqtt_host)

Substitute your own MQTT server ip address for 10.34.167.1, and your own mqtt id for 'indi_server01'.

To be specific, the mqtt_id should be unique for every connection to the MQTT network.

When choosing an mqtt_id, consider using a prefix, to avoid clashing with other users of the MQTT broker,
such as indi_server01, or indi_client01.

.. _driverstomqtt:

indi_mr.driverstomqtt
^^^^^^^^^^^^^^^^^^^^^

Connects INDI drivers and attached instruments to the MQTT network without needing indiserver.

.. autofunction:: indi_mr.driverstomqtt

Example Python script running on the machine with the connected instruments::

    from indi_mr import driverstomqtt, mqtt_server

    # define the host/port where the MQTT server is listenning, this function returns a named tuple.

    mqtt_host = mqtt_server(host='10.34.167.1', port=1883)

    # blocking call which runs the service, communicating between drivers and mqtt

    driverstomqtt(["indi_simulator_telescope", "indi_simulator_ccd"], 'indi_drivers01', mqtt_host)

    # The list of two simulated drivers shown above should be replaced by a list of your own drivers.

Substitute your own MQTT server ip address for 10.34.167.1, and your own mqtt id for 'indi_drivers01'.

.. _mqtttoredis:

indi_mr.mqtttoredis
^^^^^^^^^^^^^^^^^^^

Intended to be run on the same server running a redis service, typically with the gui or web service which can read/write to redis.

An INDI client which receives XML data from the MQTT server and converts to redis key-value storage, and reads data published to redis, and sends to the MQTT server.

.. autofunction:: indi_mr.mqtttoredis

For further information on the log_lengths parameter see :ref:`log_lengths`.

Example Python script running at the redis server::

    from indi_mr import mqtttoredis, mqtt_server, redis_server

    # define the hosts/ports where servers are listenning, these functions return named tuples.

    mqtt_host = mqtt_server(host='10.34.167.1', port=1883)
    redis_host = redis_server(host='localhost', port=6379)

    # blocking call which runs the service, communicating between mqtt and redis

    mqtttoredis('indi_client01', mqtt_host, redis_host, blob_folder='/path/to/blob_folder')


Set the blob_folder to a directory of your choice and substitute your own MQTT server ip address for 10.34.167.1, and mqtt id for 'indi_client01'.

.. _mqtttoport:

indi_mr.mqtttoport
^^^^^^^^^^^^^^^^^^

Transfers XML data between the MQTT server and a server port, which, in turn, can connect to an INDI client.

.. autofunction:: indi_mr.mqtttoport

Example Python script::

    from indi_mr import mqtttoport, mqtt_server

    # define the mqtt server

    mqtt_host = mqtt_server(host='10.34.167.1', port=1883)

    # blocking call which runs the service, communicating between mqtt and the port

    mqtttoport("indi_port01", mqtt_host, port=7624)


Substitute your own MQTT server ip address for 10.34.167.1, and mqtt id for 'indi_port01'.




