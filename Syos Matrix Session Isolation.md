DISPLAY? - from my research, this just specifies a screen within X-Server that sends rendering information. Many GUI applications will check that the session has a valid screen attached to it. Essentially, based on the DISPLAY env var set, it will try to connect to the Screen instance in the X-Server, if it requires GUI, since that's where all the information needed to render will be sent. If it cannot find this, or DISPLAY var is unset, it will crash and say "cannot open display". The DISPLAY in short is just a connection instance to X11 server. 
This concept of DISPLAY is separate from how many screens are connected. Typical desktop session just has one DISPLAY. Screen is where it gets separated. Modern X11 merges multiple monitors to a single screen 0, so we actually just have 1 logical screen in X11 that renders for both monitor 1 and 2. 
For our parallelised simulation runs, this is largely not a concern because none of the docker sessions require a "screen". Even if it does, having a duplicate screen would just mean we are pushing all the windows and rendering information to a single screen instance of the X-Server. We will never render that screen anyway. So this should be okay.

![[Pasted image 20260424134041.png]]

> [!important]
> GZ_PARTITION - IMPORTANT. otherwise we constantly get shit. Line it up with the actual sim instance so that users can view real time in their gz GUI if they wish.
ROS_DOMAIN_ID - IMPORTANT. Not needed currently, but if there are GZ + ROS2 nodes integrated cases, this will need to be set.

AP_INSTANCE - should be fine across sessions, but will be needed for swarming stuff

