========================================================
Pure Python, pure state-machine WebSocket implementation
========================================================

.. image:: https://github.com/python-hyper/wsproto/workflows/CI/badge.svg
    :target: https://github.com/python-hyper/wsproto/actions
    :alt: Build Status
.. image:: https://codecov.io/gh/python-hyper/wsproto/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/python-hyper/wsproto
    :alt: Code Coverage
.. image:: https://readthedocs.org/projects/wsproto/badge/?version=latest
    :target: https://wsproto.readthedocs.io/en/latest/
    :alt: Documentation Status
.. image:: https://codecov.io/gh/python-hyper/wsproto/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/python-hyper/wsproto
    :alt: Code coverage
.. image:: https://img.shields.io/badge/chat-join_now-brightgreen.svg
    :target: https://gitter.im/python-hyper/community
    :alt: Chat community


This repository contains a pure-Python implementation of a WebSocket protocol
stack. It's written from the ground up to be embeddable in whatever program you
choose to use, ensuring that you can communicate via WebSockets, as defined in
`RFC6455 <https://tools.ietf.org/html/rfc6455>`_, regardless of your programming
paradigm.

This repository does not provide a parsing layer, a network layer, or any rules
about concurrency. Instead, it's a purely in-memory solution, defined in terms
of data actions and WebSocket frames. RFC6455 and Compression Extensions for
WebSocket via `RFC7692 <https://tools.ietf.org/html/rfc7692>`_ are fully
supported.

wsproto supports Python 3.6.1 or higher.

To install it, just run:

.. code-block:: console

    $ pip install wsproto


Usage
=====

Let's assume you have some form of network socket available. wsproto client
connections automatically generate a HTTP request to initiate the WebSocket
handshake. To create a WebSocket client connection:

.. code-block:: python

  from wsproto import WSConnection, ConnectionType
  from wsproto.events import Request

  ws = WSConnection(ConnectionType.CLIENT)
  ws.send(Request(host='echo.websocket.org', target='/'))

To create a WebSocket server connection:

.. code-block:: python

  from wsproto.connection import WSConnection, ConnectionType

  ws = WSConnection(ConnectionType.SERVER)

Every time you send a message, or call a ping, or simply if you receive incoming
data, wsproto might respond with some outgoing data that you have to send:

.. code-block:: python

  some_socket.send(ws.bytes_to_send())

Both connection types need to receive incoming data:

.. code-block:: python

  ws.receive_data(some_byte_string_of_data)

And wsproto will issue events if the data contains any WebSocket messages or state changes:

.. code-block:: python

  for event in ws.events():
      if isinstance(event, Request):
          # only client connections get this event
          ws.send(AcceptConnection())
      elif isinstance(event, CloseConnection):
          # guess nobody wants to talk to us any more...
      elif isinstance(event, TextMessage):
          print('We got text!', event.data)
      elif isinstance(event, BytesMessage):
          print('We got bytes!', event.data)

Take a look at our docs for a `full list of events
<https://wsproto.readthedocs.io/en/latest/api.html#events>`!

Testing
=======

It passes the autobahn test suite completely and strictly in both client and
server modes and using permessage-deflate.

If you want to run the compliance tests, go into the compliance directory and
then to test client mode, in one shell run the Autobahn test server:

.. code-block:: console

    $ wstest -m fuzzingserver -s ws-fuzzingserver.json

And in another shell run the test client:

.. code-block:: console

    $ python test_client.py

And to test server mode, run the test server:

.. code-block:: console

    $ python test_server.py

And in another shell run the Autobahn test client:

.. code-block:: console

    $ wstest -m fuzzingclient -s ws-fuzzingclient.json


Documentation
=============

Documentation is available at https://wsproto.readthedocs.io/en/latest/.

Contributing
============

``wsproto`` welcomes contributions from anyone! Unlike many other projects we
are happy to accept cosmetic contributions and small contributions, in addition
to large feature requests and changes.

Before you contribute (either by opening an issue or filing a pull request),
please `read the contribution guidelines`_.

.. _read the contribution guidelines: http://python-hyper.org/en/latest/contributing.html

License
=======

``wsproto`` is made available under the MIT License. For more details, see the
``LICENSE`` file in the repository.

Authors
=======

``wsproto`` was created by @jeamland, and is maintained by the python-hyper
community.
