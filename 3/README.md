---
domain: rfc.zeromq.org
shortname: 3/ZDCF
name: ZeroMQ Device Configuration File
status: retired
editor: Pieter Hintjens <ph@imatix.com>
---

The ZeroMQ Device Configuration File format (ZDCF) specifies a standard syntax for configuring 0MQ devices.  It provides information to configure a 0MQ context, and a set of 0MQ sockets.  This specification aims to make it easier to share and reuse devices and build systems for device administration.

## License

Copyright (c) 2010 iMatix Corporation and contributors

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see <http://www.gnu.org/licenses>.

## Change Process

This Specification is a free and open standard (see "[Definition of a Free and Open Standard](http://www.digistan.org/open-standard:definition)") and is governed by the Digital Standards Organization's Consensus-Oriented Specification System (COSS) (see "[Consensus Oriented Specification System](http://www.digistan.org/spec:1/COSS)").

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 (see "[Key words for use in RFCs to Indicate Requirement Levels](http://tools.ietf.org/html/rfc2119)").

## Goals

ZDCF aims to:

* Provide a standard reusable format for device configuration.
* Be as widely accessible as possible for different programming languages.
* Be platform neutral.
* Be easy to read and edit.
* Be lightweight to process.
* Cover all 0MQ context and socket configuration options.

## Architecture

ZDCF is a JSON (see "[Introducing JSON](http://json.org/)") format.  A ZDCF file contains an optional context object and zero or more device objects.  Conceptually, one ZDCF file maps to one process, consisting of a single context and zero or more device threads.

Here is a typical example of a ZDCF file:

```
{
    "context": {
        "iothreads": 1,
        "verbose": true
    },
    "main" : {
        "type": "queue",
        "frontend": {
            "option": {
                "hwm": 1000,
                "swap": 25000000
            },
            "bind": "tcp://eth0:5555"
        },
        "backend": {
            "bind": "tcp://eth0:5556"
        }
    }
}
```

This is also a valid ZDCF file:

```
{
}
```

### The Context Object

The context object is optional and has these properties:

* Its name is "context".
* "iothreads" - (integer) - specifies the number of I/O threads for the context.  Defaults to 1 if not specified.
* "verbose" - (Boolean) - if "true", the program parsing the JSON should output tracing information.  Defaults to "false" if not specified.

### The Device Object

Device objects can occur zero or more times and have these properties:

* The name is any value except "context".
* "type" - (string) - specifies the device type.  Types starting with "z" are reserved for built-in 0MQ devices.  Other device types may be defined by the application as needed.
* Zero or more socket objects.

### The Socket Object

Socket objects can occur zero or more times within a device object, and have these properties:

* The name is any value except "type".
* "bind" - (string) - specifies zero or more endpoints to bind the socket to.
* "connect" - (string) - specifies zero or more endpoints to connect the socket to.
* "option" - (object) - specifies configuration of the socket.

### The Option Object

An option object is optional inside a socket object.  It has these properties:

* Its name is "option".
* "hwm" - (integer) - specifies the ZMQ_HWM option.
* "swap" - (integer) - specifies the ZMQ_SWAP option.
* "affinity" - (integer) - specifies the ZMQ_AFFINITY option.
* "identity" - (string) - specifies the ZMQ_IDENTITY option.
* "subscribe" - (string) - specifies the ZMQ_SUBSCRIBE option.
* "rate" - (integer) - specifies the ZMQ_RATE option.
* "recovery_ivl" - (integer) - specifies the ZMQ_RECOVERY_IVL option.
* "mcast_loop" - (Boolean) - specifies the ZMQ_MCAST_LOOP option.
* "sndbuf" - (integer) - specifies the ZMQ_SNDBUF option.
* "rcvbuf" - (integer) - specifies the ZMQ_RCVBUF option.

See [zmq_setsockopt(3)](http://api.zeromq.org/zmq_setsockopt.html) for details.

### Value Arrays

Properties may be specified as value arrays where it makes sense and at least for:

* The socket "bind" property.
* The socket "connect" property.
* The option "subscribe" property.

For example:

```
    "frontend": {
        "option": {
            "subscribe": [ "10001", "10002" ]
        },
        "bind": [ "tcp://eth0:5555", "inproc://device" ]
    }
```

### Built-in Device Types

The built-in device types that exist at time of writing are:

* "zqueue" - ZMQ_QUEUE
* "zforwarder" - ZMQ_FORWARDER
* "zstreamer" - ZMQ_STREAMER

See [zmq_device(3)](http://api.zeromq.org/zmq_device.html) for details.
