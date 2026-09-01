GStreamer is essentially made up of a series of data processing components, connected in series. This connection in series is resembling of pipeline, hence the name video pipeline. 
For video elements, the stream starts at a **"source"**, then goes through a series of processing **"filters"**,  then finally arrives at a **"sink"**. 
# Source Element
This is the initial element of every GStreamer pipeline. This represents the input node, which provides the initial input of the data. This is the part of the pipeline where the data is actually generated, using http or locally stored files. 



# Sink Element
This is the termination element of a GStreamer pipeline. This represents the consumer node of the processed data. This accepts data, and does not produce anymore.
# Filter Element
Filter receives data, and processes the upstream data for the downstream nodes. Common processing includes *converter* (converting from one input form to another), *muxer* (many inputs to one output), *demuxer* (one input to many different outputs) and *codecs* (encodes or decodes the given data).