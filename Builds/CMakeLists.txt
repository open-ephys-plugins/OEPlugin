cmake_minimum_required(VERSION 3.1.0)
project(oe_plugin)

#change this line to modify the plugin name
set(PLUGIN_NAME OEPlugin)

set(GUI_BASE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../../plugin-GUI)

include_directories(${GUI_BASE_DIR}/JuceLibraryCode ${GUI_BASE_DIR}/JuceLibraryCode/modules ${GUI_BASE_DIR}/Source/Plugins/Headers)

add_compile_definitions(OEPLUGIN 
	"$<$<PLATFORM_ID:Windows>:JUCE_API=__declspec(dllimport)>" 
	$<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_WARNINGS> 
	$<$<PLATFORM_ID:Linux>:JUCE_DISABLE_NATIVE_FILECHOOSERS=1>
	$<$<CONFIG:Debug>:DEBUG=1>
	$<$<CONFIG:Debug>:_DEBUG=1>
	$<$<CONFIG:Optimized>:NDEBUG=1>
	)
	
set(SOURCE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../Source)
file(GLOB_RECURSE SRC_FILES LIST_DIRECTORIES false "${SOURCE_PATH}/*.cpp" "${SOURCE_PATH}/*.h")

add_library(${PLUGIN_NAME} SHARED ${SRC_FILES})

if(MSVC)
	set(GUI_BIN_DIR ${GUI_BASE_DIR}/Builds/VisualStudio2013)
	if (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(GUI_BIN_DIR ${GUI_BIN_DIR}/x64)
	endif()

	#target_link_directories(${PLUGIN_NAME} PUBLIC $<$<CONFIG:Debug>:${GUI_BIN_DIR}/Debug/bin>)
	#target_link_directories(${PLUGIN_NAME} PUBLIC $<$<CONFIG:Optimized>:${GUI_BIN_DIR}/Debug/bin>)
	link_directories($<$<CONFIG:Debug>:${GUI_BIN_DIR}/Debug/bin> $<$<CONFIG:Optimized>:${GUI_BIN_DIR}/Debug/bin>)
	target_link_libraries(${PLUGIN_NAME} 
		$<$<CONFIG:Debug>:${GUI_BIN_DIR}/Debug/bin/open-ephys.lib>
		$<$<CONFIG:Release>:${GUI_BIN_DIR}/Release/bin/open-ephys.lib>
	)
	target_compile_options(${PLUGIN_NAME} PRIVATE /sdl-)
endif()

#create filters for vs and xcode

foreach( src_file IN ITEMS ${SRC_FILES})
	get_filename_component(src_path "${src_file}" PATH)
	file(RELATIVE_PATH src_path_rel "${SOURCE_PATH}" "${src_path}")
	string(REPLACE "/" "\\" group_name "${src_path_rel}")
	source_group("${group_name}" FILES "${src_file}")
endforeach()

