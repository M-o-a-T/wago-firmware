===========
wago access
===========

This program runs on a Wago Linux controller.
Its job is to export the external inputs and outputs in a controlled fashion.

It implements the following features (in parentheses: the commands in question; no command means TODO):

 * read inputs (i)
 
 * read and write outputs (s c I)
 
 * set or clear an output for a pre-set time (s c)

 * periodically set+clear an output (Pulse Width Modulation) (s c)

 * send a notification whenever an input changes (m+)

 * count input transitions, with report timer (i.e. don't send a network message at every transition) (m#)

as well as some auxiliary functions

 * measure controller's cycle time (dc)

 * set program's poll time (d)

 * (re)initialize outputs, in case of network outage

 * keepalive messages and timer (m)

 * report the kernel-exported CSV list of I/O fields (D)

The controller has a 3 msec debounce filter on its input. The minimum sensible cycle time therefore is 2 msec.
If you know that no input will change faster than once a second, increasing the timer values will save some power
and allow the program to react faster (since it usually doesn't need to wait for the controller to finish processing
its input+output list).

There can be more than one active server connection.
Monitors, re-inits and keepalives are specific to the connection they have been issued on.

TODO: A challenge/response mechanism is used to authorize the remote side.


Building and installing
=======================

This program depends on uClibc and libevent2.

uClibc is available from Wago.

For libevent2, download the source and configure as

	sh configure LDFLAGS=-Wl,-elf2flt CC=/usr/local/bin/arm-uclinux-elf-gcc --host=arm-uclinux-elf --disable-openssl --disable-thread-support --disable-malloc-replacement --disable-shared --prefix=/usr/local/arm-linux-uclibc/

The line protocol
=================

The interface to the daemon consists of a simple command/response
protocol.

Commands are single letters. They might have arguments.

Responses are each introduced with a single special character:

 *  A spontaneous one-line message, not in response to a command.

 +  a single-line positive response.
    Depending on the command, it may include data.

 ?  A single-line negative response, indicating an error.

 =  a multi-line response, terminated by a single dot. (cf. SMTP.)
    Used primarily for help messages.

The following messages contain a monitor number after the initial character(s).

 !+ a monitor has been created.

 !  a signal from a monitor. Parameters depend on the type of signal requested.

 !- the monitor has been cleared.


Commands
--------

Use 'h' for basic help, and a command list.

Use 'hX' for basic help, and a command list.

