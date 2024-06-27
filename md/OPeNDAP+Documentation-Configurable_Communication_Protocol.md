Currently the PPT protocol is built into the BES and the BES client
applications. There has been requirements from different groups to be
able to use other communication protocols, such as AMQP. We need the
ability for the BES to be able to load the appropriate communication
protocol instead of just using PPT. This will require modularizing the
PPT, and providing a method for other developers to load communication
protocols such as AMQP to be able to handle BES requests. This will need
to happen for both the BES server and the BES client.

[Configurable Communication Protocol](Category:Development "wikilink")
[Configurable Communication
Protocol](Category:Hyrax_Development "wikilink")