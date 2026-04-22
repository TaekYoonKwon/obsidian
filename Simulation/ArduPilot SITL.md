This page aims to capture how ArduPilot SITL exactly works. Too often we have been abstracted away from the exact logic flow of how the ArduPilot SITL works, since they are nice plug and play kind of stuff. We will try to demystify ArduPilot SITL, what MAVProxy is, how it actually talks to gazebo etc.

# Build Process
ArduPilot Build pipeline is notoriously messy, because of the hardware dependencies, different vehicle types and trying to handle all the edge cases that may arise due to hardware limitations etc.

This doc is about SITL, but we must still be knowledgeable with the build process and what we get out of it, minus the ChibiOS stuff which is the RTOS/HAL platform for embedded platforms when we are running it on real hardware. 

So instead of looking for hwdef file which contains all the specifics of the hardware platform that the build system is building against, the build pipeline will simply create a standard C++ application to be run locally.

ArduPilot uses waf to manage the complexity of the build - https://waf.io/

When building for SITL, waf swaps ChibiOS for AP_HAL_SITL, which does all the abstraction for hardware system calls and translate it to Linux kernel equivalents.

Then we can run this binary inside build/bin/ArduCopter etc. In fact the standard `Tools/autotest/sim_vehicle.py` with args, it is just a wrapper which parses and executes the binary with corresponding launch arguments.

So looking through `libraries/AP_HAL_SITL` will provide insight into how the SITL is actually run, the initialisation sequence and how it interfaces with gazebo stuff.

*All the stuff below when you launch SITL.. where does it come from??*
```
SIM_VEHICLE: Using defaults from (Tools/autotest/default_params/gazebo-harby.parm)  
SIM_VEHICLE: Run ArduPlane  
SIM_VEHICLE: "/ardupilot/Tools/autotest/run_in_terminal_window.sh" "ArduPlane" "/ardupilot/build/sitl/b  
in/arduplane" "-S" "--model" "JSON" "--speedup" "1" "--slave" "0" "--defaults" "Tools/autotest/default_  
params/gazebo-harby.parm" "--sim-address=127.0.0.1" "-I0"  
SIM_VEHICLE: Run MavProxy  
SIM_VEHICLE: "mavproxy.py" "--out" "127.0.0.1:14550" "--master" "tcp:127.0.0.1:5760" "--sitl" "127.0.0.  
1:5501" "--out" "udp:127.0.0.1:14551" "--out" "udp:127.0.0.1:14552" "--map" "--console"  
RiTW: Starting ArduPlane : /ardupilot/build/sitl/bin/arduplane -S --model JSON --speedup 1 --slave 0 --  
defaults Tools/autotest/default_params/gazebo-harby.parm --sim-address=127.0.0.1 -I0  
RiTW: Window access not found, logging to /tmp/ArduPlane.log  
Connect tcp:127.0.0.1:5760 source_system=255  
...
```

# AP_HAL_SITL Init Sequence
The SITL binary initializes through this call chain:

1. `HAL_SITL_Class.cpp:214` - `HAL_SITL::run()` calls `_sitl_state->init()`
2. `SITL_State.cpp:469` - `SITL_State::init()` calls `_parse_command_line()`
3. `SITL_cmdline.cpp:206` - `SITL_State::_parse_command_line()` sets up all communication ports
4. `SIM_JSON.cpp:89` - `JSON::set_interface_ports(address, port_in, port_out)` configures the UDP socket for the FDM exchange, with the ports parsed from the command line (`SIM_IN_PORT=9003`, `SIM_OUT_PORT=9002`). Sets the socket to non-blocking and stores the target address/port for sending actuator commands.

`parse_command_line()` function exposes many crucial insights to which ports SITL expects to send/receive data by default. 
#### Port Assignments (Instance 0)
The table below captures some of the most significant port mappings:

| Port | Direction        | Protocol | Purpose                                                                                                   |
| ---- | ---------------- | -------- | --------------------------------------------------------------------------------------------------------- |
| 9002 | SITL -> Gazebo   | UDP      | `SIM_OUT_PORT` - actuator commands (motor PWM, servo positions) sent to the Gazebo plugin's `fdm_port_in` |
| 9003 | Gazebo -> SITL   | UDP      | `SIM_IN_PORT` - simulated sensor state (pose, velocity, IMU, GPS) received from the Gazebo plugin         |
| 5760 | SITL <-> GCS     | TCP      | `BASE_PORT` - MAVLink connection (MAVProxy, QGroundControl)                                               |
| 5501 | External -> SITL | UDP      | `RCIN_PORT` - RC input override                                                                           |
#### Multiple instances
If we have instance number set `AP_INSTANCE=N` passed in as a command to `sim_vehicle.py`, then the port numbers offset by 10 * N.

#### SITL Binary Log for debug (/tmp/ArduPlane.log)

The SITL binary's stdout is redirected here because `run_in_terminal_window.sh` can't open a GUI terminal inside a container. This log shows the full init sequence that MAVProxy output doesn't:
```
Setting SIM_SPEEDUP=1.000000           <- speedup factor (1x real time)
Starting SITL: JSON                     <- JSON backend constructor called
JSON control interface set to 127.0.0.1:9002  <- set_interface_ports(), sending actuator cmds to Gazebo plugin's fdm_port_in
Starting sketch 'ArduPlane'             <- AP_HAL main loop starts
Using Irlock at port : 9005             <- IR lock sensor input (unused with Gazebo)
bind port 5760 for SERIAL0              <- MAVLink TCP port (MAVProxy connects here)
Waiting for connection ....             <- blocks until MAVProxy connects on TCP:5760
Connection on serial port 5760          <- MAVProxy connected
Loaded defaults from ...gazebo-harby.parm  <- parameter defaults applied
bind port 5762 for SERIAL1              <- additional MAVLink serial ports
bind port 5763 for SERIAL2
Home: -35.363262 149.165237 alt=584.000m <- home position set (from -L flag or defaults)
No JSON sensor message received, resending servos  <- SITL is sending actuator cmds to 9002 but hasn't received state back from Gazebo yet
```
# Connection to Gazebo
### Gazebo-ArduPilot Plugin
The Gazebo ArduPilot plugin config in the model SDF must match:
```
<plugin filename="libArduPilotPlugin.so" name="ardupilot_gazebo::ArduPilotPlugin">
  <fdm_addr>127.0.0.1</fdm_addr>
  <fdm_port_in>9002</fdm_port_in>    <!-- receives from SITL SIM_OUT_PORT -->
  <listen_addr>127.0.0.1</listen_addr>
  <fdm_port_out>9003</fdm_port_out>  <!-- sends to SITL SIM_IN_PORT -->
</plugin>
```

#### Quick remark: why SIM_JSON, not SIM_Gazebo?
ArduPilot SITL has multiple simulation backend options. `SIM_Gazebo` was the legacy backend for Gazebo Classic (Gazebo 9/11), which used a tightly coupled C++ interface specific to that simulator. It's deprecated along with Gazebo Classic itself, replaced by Ignition (GZ).

The ArduPilot Gazebo plugin (`libArduPilotPlugin.so`) is the Gazebo-side implementation of this JSON protocol. It reads entity state from the Gazebo ECM, serializes it as JSON, sends it to SITL, receives actuator JSON back, and applies it to the entity. The plugin knows about Gazebo; SITL doesn't. SITL just sees a stream of JSON sensor data and doesn't care where it comes from.

This is why the `--model JSON` flag works for both Gazebo Harmonic and any other simulator - the SITL binary is decoupled from the physics engine entirely.

More information can be found here:
https://github.com/ArduPilot/ardupilot/tree/master/libraries/SITL/examples/JSON

## FDM Protocol
We have observed that MAVLink is actually not used for SITL to sim data transfer. Instead they communicate via JSON over UDP (for GZ Ignition) and custom packet structures for each simulator. 
### Input from ArduPilot Plugin (to SITL)
Sent by the plugin every physics step. Contains everything SITL needs to run its estimators and controllers, all pretty self explanatory stuff:
```
{
    "timestamp": 1234.567,
    "imu": {
        "gyro": [0.01, -0.02, 0.005],
        "accel_body": [0.1, 0.0, -9.81]
    },
    "position": [149.165237, -35.363262, 584.0],
    "attitude": [1.0, 0.0, 0.0, 0.0],
    "velocity": [12.5, 0.3, -0.1],
    }
}
```

### Output from SITL (to GZ Plugin)
Sent by SITL after processing the sensor state. Contains the raw servo/motor outputs:
```
{
    "magic": 18458,
    "frame_rate": 1000,
    "frame_count": 45231,
    "pwm": [1500, 1500, 1100, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500, 1500]
}
```
The magic value is a constant of 18458, this is used to confirm the packet is from ArduPilot and is used for protocol versioning.

The number of output channels may be increased to 32 by setting the parameter SERVO_32_ENABLE = 1. The SITL output packet then uses a magic value 29569

- **frame rate**: represents the time step the simulation should take, this can be changed with the SIM_RATE_HZ ArduPilot parameter. 
  Gazebo  is free to ignore this value, a maximum time step size would typically be set. The SIM_RATE_HZ should be kept above the vehicle loop rate, by default this 400hz on copter and quadplanes and 50 hz on plane and rover.
- **frame_count** will increment for each output frame sent by ArduPilot, this count can be used to detect lost or duplicate frames. This count will be reset when SITL is re-started allowing Gazebo to reset the vehicle. 
  If no input data is received after 10 seconds ArduPilot will re-send the output frame without incrementing the counter. This allows the Gazebo Plugin to be restarted and re-connect. Note that this may fill up the input buffer of the Gazebo after some time.
# MAVProxy
I feel like MAVProxy deserves its own page, but I will just write it here, since it overlaps a lot with MAVLink Router, and it is used only locally for SITL instances. 

So we have the FDM (Flight Dynamics Model) data feeding into sim, so that's all good. How about GCS? 

`sim_vehicle.py:1142` - Along with all other args passed into sim_vehicle.py, the argument to decide whether to launch mavproxy or not is also handled here:
```
group_sim.add_option("", "--no-mavproxy", action='store_true', default=False, help="Don't launch MAVProxy")
```

**By default, MAVProxy is launched along with SITL, unless specified otherwise**
##### What it does
MAVProxy is actually a full GCS that sits between SITL, that also happens to do routing for everything else that wants to talk MAVLink. When `sim_vehicle.py` launches, it starts two processes: the SITL binary and MAVProxy. SITL opens a TCP listener on port 5760 and MAVProxy connects to it as the first (and sometimes only) MAVLink client.

MAVProxy's core job is to distribute SITL's SISO MAVLink port, so that multiple instances can listen and publish MAVLink packets to it. SITL exposes a single MAVLink endpoint. MAVProxy connects to that endpoint and then re-publishes the MAVLink stream to multiple outputs - GCS, scripts, ROS bridge, whatever. Without MAVProxy, you'd have to connect everything directly to SITL, but SITL only supports a limited number of serial ports (TCP 5760, 5762, 5763).

### Default run
The SITL binary determines its communication ports through a chain starting in command line parsing and ending in the HAL UART driver.
`SITL_State::_parse_command_line()` sets `_base_port = 5760` and applies an instance offset based on the `-I` flag. This base port is stored on the shared `SITL_State` object, which the HAL's UART driver has access to.

When the ArduPilot application initializes its serial ports (`SERIAL0`, `SERIAL1`, `SERIAL2`, etc.), the UART driver in `AP_HAL_SITL/UARTDriver.cpp` reads the serial port configuration to determine whether each port should be TCP or UDP, then calls `bind()` and `listen()` using the base port plus a per-serial offset:

|Serial Port|Offset|Port (Instance 0)|Purpose|
|---|---|---|---|
|`SERIAL0`|+0|5760|Primary MAVLink (MAVProxy connects here)|
|`SERIAL1`|+2|5762|Secondary MAVLink|
|`SERIAL2`|+3|5763|Tertiary MAVLink|

The protocol (TCP vs UDP) is determined by the serial port's configuration parameter, not hardcoded. By default the MAVLink serial ports use TCP, which is why MAVProxy connects with `--master tcp:127.0.0.1:5760`.

The SITL log confirms the binding sequence:

```
bind port 5760 for SERIAL0
SERIAL0 on TCP port 5760
Waiting for connection ....
Connection on serial port 5760      ← MAVProxy connected
bind port 5762 for SERIAL1
SERIAL1 on TCP port 5762
bind port 5763 for SERIAL2
SERIAL2 on TCP port 5763
```

For multi-instance runs (`-I N`), the entire port block shifts by `N * 10`, so instance 1 binds SERIAL0 on 5770, SERIAL1 on 5772, etc. This is how Matrix runs parallel SITL instances on the same host without port collisions.