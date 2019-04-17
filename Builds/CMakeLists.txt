cmake_minimum_required(VERSION 3.1.0)
project(oe_plugin)
set(CMAKE_SHARED_LIBRARY_PREFIX "")

#change this line to modify the plugin name
set(PLUGIN_NAME OEPlugin)

set(GUI_BASE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../plugin-GUI)

include_directories(${GUI_BASE_DIR}/JuceLibraryCode ${GUI_BASE_DIR}/JuceLibraryCode/modules ${GUI_BASE_DIR}/Source/Plugins/Headers)

set_property(DIRECTORY APPEND PROPERTY COMPILE_DEFINITIONS
	OEPLUGIN 
	"$<$<PLATFORM_ID:Windows>:JUCE_API=__declspec(dllimport)>" 
	$<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_WARNINGS> 
	$<$<PLATFORM_ID:Linux>:JUCE_DISABLE_NATIVE_FILECHOOSERS=1>
	$<$<CONFIG:Debug>:DEBUG=1>
	$<$<CONFIG:Debug>:_DEBUG=1>
	$<$<CONFIG:Release>:NDEBUG=1>
	)
	

set(SOURCE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../Source)
file(GLOB_RECURSE SRC_FILES LIST_DIRECTORIES false "${SOURCE_PATH}/*.cpp" "${SOURCE_PATH}/*.h")

add_library(${PLUGIN_NAME} SHARED ${SRC_FILES})

target_compile_features(${PLUGIN_NAME} PUBLIC cxx_auto_type cxx_generalized_initializers cxx_nonstatic_member_init)

#Libraries and compiler options
if(MSVC)
	set(GUI_BIN_DIR ${GUI_BASE_DIR}/Builds/VisualStudio2013)
	if (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(GUI_BIN_DIR ${GUI_BIN_DIR}/x64)
	endif()

	target_link_directories(${PLUGIN_NAME} PUBLIC $<$<CONFIG:Debug>:${GUI_BIN_DIR}/Debug/bin>)
	target_link_directories(${PLUGIN_NAME} PUBLIC $<$<CONFIG:Release>:${GUI_BIN_DIR}/Release/bin>)
	target_link_libraries(${PLUGIN_NAME} open-ephys.lib)
	target_compile_options(${PLUGIN_NAME} PRIVATE /sdl-)
elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
	set(GUI_BIN_DIR ${GUI_BASE_DIR}/Builds/Linux/bin)

	target_link_libraries(${PLUGIN_NAME} GL X11 Xext Xinerama asound dl freetype pthread rt)
	set_property(TARGET ${PLUGIN_NAME} APPEND_STRING PROPERTY LINK_FLAGS
		"-fvisibility=hidden -fPIC -rdynamic -Wl,-rpath,'$$ORIGIN/../shared'")
	target_compile_options(${PLUGIN_NAME} PRIVATE -fPIC -rdynamic)
endif()

#create filters for vs and xcode

foreach( src_file IN ITEMS ${SRC_FILES})
	get_filename_component(src_path "${src_file}" PATH)
	file(RELATIVE_PATH src_path_rel "${SOURCE_PATH}" "${src_path}")
	string(REPLACE "/" "\\" group_name "${src_path_rel}")
	source_group("${group_name}" FILES "${src_file}")
endforeach()

