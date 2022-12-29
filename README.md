# Open Ephys plugin template (`v0.5.x`)

This repository contains a generic template for building plugins for version `0.5.x` of the [Open Ephys GUI](https://github.com/open-ephys/plugin-GUI).

To build plugins for the latest version of the GUI `v0.6.x`, you should use one of the following templates instead:

- [processor-plugin-template](https://github.com/open-ephys-plugins/processor-plugin-template) (most common)
- [visualizer-plugin-template](https://github.com/open-ephys-plugins/visualizer-plugin-template)
- [data-thread-template](https://github.com/open-ephys-plugins/data-thread-template)
- [file-source-template](https://github.com/open-ephys-plugins/file-source-template)
- [record-engine-template](https://github.com/open-ephys-plugins/record-engine-template)

More information about building your own plugins can be found in the [Open Ephys GUI Developer Guide](https://open-ephys.github.io/gui-docs/Developer-Guide/index.html).

## Using this template

1. Add source code files to the `Source` directory. The existing files can be used as a starting point.
2. Edit the `OpenEphysLib.cpp` file to include the correct plugin metadata.
3. Create the build files [using CMake](https://open-ephys.github.io/gui-docs/Developer-Guide/Compiling-plugins.html)

## Using external libraries

To link the plugin to external libraries, it is necessary to manually edit the Build/CMakeLists.txt file. The code for linking libraries is located in comments at the end.
For most common libraries, the `find_package` option is recommended. An example would be

```cmake
find_package(ZLIB)
target_link_libraries(${PLUGIN_NAME} ${ZLIB_LIBRARIES})
target_include_directories(${PLUGIN_NAME} PRIVATE ${ZLIB_INCLUDE_DIRS})
```

If there is no standard package finder for cmake, `find_library`and `find_path` can be used to find the library and include files respectively. The commands will search in a variety of standard locations For example

```cmake
find_library(ZMQ_LIBRARIES NAMES libzmq-v120-mt-4_0_4 zmq zmq-v120-mt-4_0_4) #the different names after names are not a list of libraries to include, but a list of possible names the library might have, useful for multiple architectures. find_library will return the first library found that matches any of the names
find_path(ZMQ_INCLUDE_DIRS zmq.h)

target_link_libraries(${PLUGIN_NAME} ${ZMQ_LIBRARIES})
target_include_directories(${PLUGIN_NAME} PRIVATE ${ZMQ_INCLUDE_DIRS})
```

### Providing libraries for Windows

Since Windows does not have standardized paths for libraries, as Linux and macOS do, it is sometimes useful to pack the appropriate Windows version of the required libraries alongside the plugin.
To do so, a _libs_ directory has to be created **at the top level** of the repository, alongside this README file, and files from all required libraries placed there. The required folder structure is:

```
    libs
    ├─ include           #library headers
    ├─ lib
        ├─ x64           #64-bit compile-time (.lib) files
        └─ x86           #32-bit compile time (.lib) files, if needed
    └─ bin
        ├─ x64           #64-bit runtime (.dll) files
        └─ x86           #32-bit runtime (.dll) files, if needed
```

DLLs in the bin directories will be copied to the open-ephys GUI _shared_ folder when installing.
