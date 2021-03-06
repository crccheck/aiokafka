.. _api-doc:
API Documentation
=================

AIOKafkaProducer class
----------------------

.. autoclass:: aiokafka.AIOKafkaProducer
    :members:


AIOKafkaConsumer class
----------------------

.. autoclass:: aiokafka.AIOKafkaConsumer
    :members:

Errors handling
---------------

Both consumer and producer can raise exceptions that inherit from the `kafka.common.KafkaError` class
and declared in `kafka.common` module.

Example of exceptions handling:


.. code:: python

        from kafka.common import KafkaError, KafkaTimeoutError
        # ... 
        try:
            send_future = yield from producer.send('foobar', b'test data')
            response = yield from send_future  #  wait until message is produced
        except KafkaTimeourError:
            print("produce timeout... maybe we want to resend data again?")
        except KafkaError as err:
            print("some kafka error on produce: {}".format(err))

Consumer errors
^^^^^^^^^^^^^^^

Consumer's ``async for`` and ``getone``/``getmany`` interfaces will handle those
differently. Possible consumer errors include:

    * ``TopicAuthorizationFailedError`` - topic requires authorization.
      Always raised
    * ``OffsetOutOfRangeError`` - if you don't specify `auto_offset_reset` policy
      and started cosumption from not valid offset. Always raised
    * ``RecordTooLargeError`` - broker has a *MessageSet* larger than 
      `max_partition_fetch_bytes`. **async for** - log error, **get*** will
      raise it.
    * ``InvalidMessageError`` - CRC check on MessageSet failed due to connection
      failure or bug. **async for** - log error. **get*** will raise it.