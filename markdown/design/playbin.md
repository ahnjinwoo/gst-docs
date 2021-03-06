# playbin

The purpose of this element is to decode and render the media contained
in a given generic uri. The element extends `GstPipeline` and is typically
used in playback situations.

Required features:

 - accept and play any valid uri. This includes
 - rendering video/audio
 - overlaying subtitles on the video
 - optionally read external subtitle files
 - allow for hardware (non raw) sinks
 - selection of audio/video/subtitle streams based on language.
 - perform network buffering/incremental download
 - gapless playback
 - support for visualisations with configurable sizes
 - ability to reject files that are too big, or of a format that would
   require too much CPU/memory usage.
 - be very efficient with adding elements such as converters to reduce
   the amount of negotiation that has to happen.
 - handle chained oggs. This includes having support for dynamic pad
   add and remove from a demuxer.

## Components

### decodebin

 - performs the autoplugging of demuxers/decoders
 - emits signals when for steering the autoplugging
 - to decide if a non-raw media format is acceptable as output
 - to sort the possible decoders for a non-raw format
 - see also decodebin2 design doc

### uridecodebin

 - combination of a source to handle the given uri, an optional
   queueing element and one or more decodebin2 elements to decode the
   non-raw streams.

### playsink

 - handles display of audio/video/text.
 - has request audio/video/text input pad. There is only one sinkpad
   per type. The requested pads define the configuration of the
   internal pipeline.
 - allows for setting audio/video sinks or does automatic
   sink selection.
 - allows for configuration of visualisation element.
 - allows for enable/disable of visualisation, audio and video.

### playbin

 - combination of one or more uridecodebin elements to read the uri and
   subtitle uri.
 - support for queuing new media to support gapless playback.
 - handles stream selection.
 - uses playsink to display.
 - selection of sinks and configuration of uridecodebin with raw
   output formats.

## Gapless playback feature

playbin has an `about-to-finish` signal. The application should
configure a new uri (and optional suburi) in the callback. When the
current media finishes, this new media will be played next.
