---
description: 'Here, we describe how to profile scenery applications.'
---

# Profiling

## Using Remotery

scenery includes support for the [Remotery profiler](https://github.com/Celtoys/Remotery). Remotery is a simple profiler that can be used from a browser, either on the same machine, or remotely. Here's the series of steps required to profile a scenery-based application:

1. Clone the Remotery repository and open `vis/index.html` in a browser. This is the client that connects to the application and visualises profiling results.
2. In scenery, set up profiling by either handing the \(`SceneryBase`-derived\) application the `scenery.Profiler=true` system property on startup, or adding the a new `Remotery` instance to the `Hub`:  `val profiler = Remotery() hub.add(profiler)` 
3. A certain piece of code can then be wrapped in `begin()`and `end()` blocks of Remotery. The profiler object itself can be queried from the Hub, if available in that routine: \`\`\` val profiler = hub.get&lt;Remotery&gt;\(\) profiler?.begin\("MyProfilingPoint"\) // do stuff profiler?.end\(\) \`\`\`

## Using a 3rd-party Profiler

To profile scenery, a 3rd-party profiler of choice can be used as well. Good candidates are IntelliJ's integrated profiler and Java Flight Recorder. Since Oracle has changed the licensing terms of the JDK recently, we advise to use OpenJDK, and an open-source build of the Java Flight Recorder, which can be used from Java 11 onwards. A good tutorial how to set that up can be found [here](https://dzone.com/articles/using-java-flight-recorder-with-openjdk-11-1).

