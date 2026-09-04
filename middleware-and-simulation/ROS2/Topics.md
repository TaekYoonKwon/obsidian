Topics act like a bus for [[Node]]s to exchange messages. It will take a message from [[Publishers]], and distribute it among the [[Subscribers]] it has. 
- Within one topic, there could be many publishers and many subscribers.
- A [[Node]] can publish to any number of topics and also have subscribed to any number of topics. 
- The topics **do not exist as its own file or created explicitly**.

Topics are created when a [[node]] publishes to [[DDS]] that it will publish commands with a topic and a [[Topic Type]]. Then another node may subscribe to that topic. This will establish a connection between the publisher and subscriber, over the defined topic. 