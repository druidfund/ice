if(NOT ${GENERATED_SLICES_DIR}/IceLocatorDiscovery)
	file(MAKE_DIRECTORY ${GENERATED_SLICES_DIR}/IceLocatorDiscovery)
endif()

set(GENERATED_ICELOCATORDISCOVERY_HEADERS
	${GENERATED_SLICES_DIR}/IceLocatorDiscovery/IceLocatorDiscovery.h
)

set(GENERATED_ICELOCATORDISCOVERY_SOURCES
	${GENERATED_SLICES_DIR}/IceLocatorDiscovery/IceLocatorDiscovery.cpp
)

set(ICELOCATORDISCOVERY_SOURCES
	Plugin.h
	PluginI.cpp
)

list(APPEND ICELOCATORDISCOVERY_SLICE_DIRS "-I${SLICE_DIR}")
list(APPEND ICELOCATORDISCOVERY_SLICE_PARAMS ${COMPILE_SLICES_CPP_PARAMETERS} "IceLocatorDiscovery")

foreach(slice IN LISTS ICELOCATORDISCOVERY_SLICES)
	#function(CompileSlice slice2bin_params slice_include_paths ice_file output_dir)
	CompileSlice("${ICELOCATORDISCOVERY_SLICE_PARAMS}"
		"${ICELOCATORDISCOVERY_SLICE_DIRS}"
		${slice}
		${GENERATED_SLICES_DIR}/IceLocatorDiscovery
	)
endforeach()

install(DIRECTORY ${GENERATED_SLICES_DIR}/IceLocatorDiscovery 
	DESTINATION ${GENERATED_H_INSTALL_DIR}
	FILES_MATCHING PATTERN "*.h"
)



if(BUILD_ICE_CPP11)
	set(ICELOCATOR_DISCOVERY_TARGET icelocatordiscovery++11)

	add_library(${ICELOCATOR_DISCOVERY_TARGET})

	if(MSVC)
		set_target_properties(${ICELOCATOR_DISCOVERY_TARGET} 
			PROPERTIES
				OUTPUT_NAME IceLocatorDiscovery${zeroc-ice_VERSION_MAJOR}${zeroc-ice_VERSION_MINOR}++11
				CXX_STANDARD 11
		)
	else()
		set_target_properties(${ICELOCATOR_DISCOVERY_TARGET} 
			PROPERTIES
				OUTPUT_NAME IceLocatorDiscovery++11
				CXX_STANDARD 11
		)
	endif()

	target_link_libraries(${ICELOCATOR_DISCOVERY_TARGET}
		PRIVATE
			ice++11
	)
else()
	set(ICELOCATOR_DISCOVERY_TARGET icelocatordiscovery)

	add_library(${ICELOCATOR_DISCOVERY_TARGET})

	if(MSVC)
		set_target_properties(${ICELOCATOR_DISCOVERY_TARGET} 
			PROPERTIES
				OUTPUT_NAME IceLocatorDiscovery${zeroc-ice_VERSION_MAJOR}${zeroc-ice_VERSION_MINOR}
		)
	else()
		set_target_properties(${ICELOCATOR_DISCOVERY_TARGET} 
			PROPERTIES
				OUTPUT_NAME IceLocatorDiscovery
		)
	endif()

	target_link_libraries(${ICELOCATOR_DISCOVERY_TARGET}
		PRIVATE
			ice
	)
endif()

target_sources(${ICELOCATOR_DISCOVERY_TARGET}
	PRIVATE
		${ICELOCATORDISCOVERY_SOURCES}
		${GENERATED_ICELOCATORDISCOVERY_HEADERS}
		${GENERATED_ICELOCATORDISCOVERY_SOURCES}
		${ICELOCATORDISCOVERY_SLICES}
)

target_include_directories(${ICELOCATOR_DISCOVERY_TARGET}
	PRIVATE 
		${GENERATED_SLICES_DIR}
		${PROJECT_SOURCE_DIR}/cpp/include
		${PROJECT_SOURCE_DIR}/cpp/src    
)

target_compile_definitions(${ICELOCATOR_DISCOVERY_TARGET}
	PRIVATE
		${ICE_COMPILE_DEFS}
		"ICE_LOCATOR_DISCOVERY_API_EXPORTS"
)

if(MSVC)
	source_group(
		TREE ${CMAKE_CURRENT_SOURCE_DIR}
		PREFIX "Resource Files"
		FILES
			IceLocatorDiscovery.rc
	)
	
	target_sources(${ICELOCATOR_DISCOVERY_TARGET}
		PRIVATE
			IceLocatorDiscovery.rc
	)

	source_group(
		TREE ${GENERATED_SLICES_DIR}/IceLocatorDiscovery
		PREFIX "Header Files/Generated"
		FILES
			${GENERATED_ICELOCATORDISCOVERY_HEADERS}
	)

	source_group(
		TREE ${GENERATED_SLICES_DIR}/IceLocatorDiscovery
		PREFIX "Source Files/Generated"
		FILES
			${GENERATED_ICELOCATORDISCOVERY_SOURCES}
	)

	source_group(
		TREE ${SLICE_DIR}/IceLocatorDiscovery
		PREFIX "Slice Files"
		FILES
			${ICELOCATORDISCOVERY_SLICES}
	)

	set_property(TARGET ${ICELOCATOR_DISCOVERY_TARGET} PROPERTY LINK_FLAGS ${ICE_MSVC_DLL_LINK_OPTIONS})
	target_compile_options(${ICELOCATOR_DISCOVERY_TARGET} 
		PRIVATE
			${ICE_MSVC_COMPILE_OPTIONS}   
	)
endif()

if(BUILD_SHARED_LIBS)
	install(TARGETS ${ICELOCATOR_DISCOVERY_TARGET} DESTINATION bin)
else()
	install(TARGETS ${ICELOCATOR_DISCOVERY_TARGET} DESTINATION lib)
endif()
