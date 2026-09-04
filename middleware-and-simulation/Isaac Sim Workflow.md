There are multiple workflows to use Isaac Sim. Main workflows are:
- GUI
	- Visual, intuitive, specialised tools for populating and simulating a virtual world
	- Recommended for world building, robot assemble, sensors attach, visual programming using [[OmniGraphs]]
- [[Isaac Sim Extensions]]
	- Runs asynchronously to allow interaction with the [[stage]], *hot reloading* to apply changes immediately.
	- Recommended for testing python snippets, building interactive GUIs, custom modules, and real-time sensitive applications
- Standalone Python
	- Control over timing of physics, can be run in headless mode
	- Recommended for large scale learning, systematic world generation

Most of the actions that can be performed on GUI can be performed using Python. Anything that was made in GUI, like the robot actions, can be pulled to USD file, into a Python script, systematically modify properties.

