.. _tools:

Tools
=====

.. automodule:: indi_mr.tools

.. autofunction:: indi_mr.tools.open_redis

Reading values
^^^^^^^^^^^^^^

.. autofunction:: indi_mr.tools.getproperties_timestamp

.. autofunction:: indi_mr.tools.last_message

.. autofunction:: indi_mr.tools.devices

.. autofunction:: indi_mr.tools.properties

.. autofunction:: indi_mr.tools.elements

.. autofunction:: indi_mr.tools.attributes_dict

.. autofunction:: indi_mr.tools.elements_dict

.. autofunction:: indi_mr.tools.property_elements

Reading logs
^^^^^^^^^^^^

This function reads logs from the logdata key stores. See :ref:`logs`.

.. autofunction:: indi_mr.tools.logs

.. _sending:

Sending values
^^^^^^^^^^^^^^

The following functions create the XML elements, and uses redis to publish the XML on the to_indi_channel.
This is picked up by the inditoredis or other functions (which subscribe to the to_indi_channel), and
which then transmits the xml on to indisserver or to MQTT.

.. autofunction:: indi_mr.tools.getProperties

.. autofunction:: indi_mr.tools.newswitchvector

.. autofunction:: indi_mr.tools.newtextvector

.. autofunction:: indi_mr.tools.newnumbervector

.. autofunction:: indi_mr.tools.newblobvector

.. autofunction:: indi_mr.tools.enableblob

Utilities
^^^^^^^^^

.. autofunction:: indi_mr.tools.number_to_float

.. autofunction:: indi_mr.tools.format_number

.. autofunction:: indi_mr.tools.clearredis

The clearredis function is called when inditoredis is started, and deletes current redis keys, but not the logdata keys.

clearredis does not delete the BLOBs folder or contents.



