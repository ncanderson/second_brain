202304241705
Status: #idea
Tags: #work #programming #linux #gss 

# Plugins
## For Dev:
- Install poe14-rc:
```bash
 sudo /home/nanderson/projects/poe_versions/14/20230608_opensea_environment_v14.0.0-6591801_ubuntu_bionic_x86_64-dev.run 

```
- Make and install `libopensea_sonar`

## developing plugins
- Look at libsonar_navnet_radar to determine what you need in your cmake file

## Plugin implementation notes
- There is a function in `oculus.cpp` that references something in Openfls_defs that my machine can't find.
- `sonar.h` needs the following:
	- \#include <openfls_types.h>
	- \#include <lcm_log_writer.h>
	- \#include <gss_profiler.h>
- Remove link to navnet dll from openfls, and all references to the files from openfls

## Samples/Notes
- [GSS_EXPORT_PLUGIN](https://www.greensea.com/sdk/libopensea_core/group__plugin__macros.html#gae86900d1166bf4d351e7e3490f27fd59): make the plugin usable elsewhere. [This example from here](https://github.com/GreenseaSystems/libopt_mdas_types/blob/8d463e20aa1fdb3ac9cb295cf016908f93d032f8/lcm_to_pcomms/opt_mdas_lcm_to_pcomms.cpp#L1293):
```c++
GSS_EXPORT_PLUGIN(Pcomms_conversion_loader, ::gss_types::Pcomms_converter)
```
- Make sure that the install location you choose in the CMakeLists.txt for the plugin is one of the locations that your plugin loader is searching.
- Create a vector of plugins where we want to use the plugins. [This example from here](https://github.com/GreenseaSystems/libopensea_pub_sub/blob/master/src/pcomms_converter.cpp#L144):
```c++
std::vector<gss::Plugin<gss::Pcomms_conversion_loader>> m_plugins;
```
- Load an exported plugin using the above attr. [This example from here](https://github.com/GreenseaSystems/libopensea_pub_sub/blob/master/src/pcomms_converter.cpp#L68):
```c++
Pcomms_converter()
  :  m_plugins(gss::Plugin<gss::Pcomms_conversion_loader>::Load_all("Pcomms_conversion_loader"))
  { ... }
```
- From shay:
  >when `Pcomms_converter` calls `gss::Plugin<>::Load_all()`, the `Load_all` function searches in three different locations for plugins that implement the specified interface, and loads any matching plugins found in any of the locations:  
> 1.  First is looks in any paths specified in `GSS_PLUGIN_PATH`
> 2.  Next it looks in any directories that get passed to `Load_all`
> 3.  Finally it looks in the hardcoded platform specific plugin directory
- `GSS_DECLARE_PLUGIN_INTERFACE` will be in the `sonar.h` class. The radar implementation wil then use `GSS_EXPORT_PLUGIN`

# [[Cmake]]
A plugin template will require some work on [[cmake]]. On reviewing the `CMakeLists.txt` file in libeod_tasks, the following thoughts:
- Update the header with package name and info
- Update the `license.txt` file? What is it?
- Make sure we're looking for the correct `find_package`
- Correct compiler flags per Shay's slack message, especially `-Wl,-z,defs`
- Make sure `file(GLOB...` points at `src` or wherever the code ends up
- This is probably correct but I think is critical for the repo to build correctly (with exported plugins): `add_library(${project_name} SHARED ${node_headers} ${node_sources})`
- Any/all Doxygen
- What's this? `include_directories(${CMAKE_CURRENT_SOURCE_DIR}/nodes)`. Don't want this `nodes` dir for navnet
- Shouldn't need this: 
```c++
set(public_libs ${public_libs} ${opensea_behavior_trees_LIBRARIES})
set(public_libs ${public_libs} ${gss_tasks_LIBRARIES})
```
- This is important:
```c++
install(TARGETS ${project_name}
  COMPONENT lib
  DESTINATION ${LIB_INSTALL_DIR}/opensea/plugins/)
```
- Default plugin install location - `/usr/local/lib/opensea/plugins/`

# References
Conversations with Chris B.
Slack conversation with Shay:
[https://github.com/GreenseaSystems/libopensea_core/blob/master/include/opensea/plugin.h](https://github.com/GreenseaSystems/libopensea_core/blob/master/include/opensea/plugin.h)
[https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/src/node_factory.cpp](https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/src/node_factory.cpp)
"in this case I have a factory class that loads different node constructors from plugins"
"Creating plugins is pretty similar to how you do it with Qt plugins. There's a macro `GSS_DECLARE_PLUGIN_INTERFACE` that you place at the end of the interface class that defines the plugin interface"
"The macro takes two arguments, the first is the fully qualified name of the interface class, the second argument is the "name" that is used to identify the type of plugin"
"e.g. (again for behavior trees) : [https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/include/opensea/obt/node_loader.h#L127](https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/include/opensea/obt/node_loader.h#L127)"
"and then for each implementation of the type of plugin you're creating you use the `GSS_EXPORT_PLUGIN` macro, which also takes two arguments, the first being the "name" of the type of plugin, and the second being the fully qualified name of the class that provides the plugin implementation"
"e.g. [https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/nodes/core_node_loader.h#L68](https://github.com/GreenseaSystems/libopensea_behavior_trees/blob/v1-rc/nodes/core_node_loader.h#L68)"