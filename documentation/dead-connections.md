# Dead Connection Detection

Dead connections will be detected through the use of a noop command which will be scheduled at a given interval. The noop will be sent on a per-connection basis as opposed to being sent for each stream. Upon receiving a noop the Consumer should immediately respond with a noop response.

#### Enabling dead connection detection

Dead connection detection is turned off by default and must be enabled by the Consumer. It is highly recommended that the Consumer enable dead connection detection if the Consumer is not on the same node as the Producer. To enable dead connection detection the Consumer must send two [Control](commands/control.md) messages. The first [Control](commands/control.md) message will contain the key set to *"enable_noop"* and the value set to *"true"*. The second control message should set the noop interval by setting the key to *"set_noop_interval"* and the value should be a number (in seconds) in string form. We recommend setting the noop interval to 120 seconds. If the recommended value is choosen then the value of this [Control](commands/control.md) message will be *"120"*.

#### Handling a noop message

When the Consumer receives a noop message it should immediately respond with a noop response. The Producer will expect a response when it sends the Consumer this message and if no response is received the Producer will disconnect its connection. The Producer will wait for a response for an amount of time equal to the noop interval.

#### Consumer dead connection detection

The Consumer should assume the connection is dead if it has not seen any messages for 2 * noop_interval. If no messages are seen the the Consumer should disconnect its connection.

**Note:** The noop is only sent by the Producer if it doesn't have anything to send for a time equal to one noop interval. If any message is sent from the Producer then the noop is rescheduled. Therefore if messages are constantly being sent by a Producer then the Consumer will never receive a noop.
