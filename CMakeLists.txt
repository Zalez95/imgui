cmake_minimum_required(VERSION 3.0.2 FATAL_ERROR)

###############################################################################
# 								ImGui
###############################################################################
# Project name and version
project(ImGui LANGUAGES CXX)
set(IMGUI_VERSION_MAJOR "1")
set(IMGUI_VERSION_MINOR "82")
set(IMGUI_VERSION "${GLFW_VERSION_MAJOR}.${GLFW_VERSION_MINOR}")

# Project options
option(IMGUI_BUILD_EXAMPLES "Build the ImGui examples" OFF)

# Find the library files
file(GLOB IMGUI_SOURCES "*.cpp")
file(GLOB IMGUI_HEADERS "*.h")

# Create the library
add_library(ImGui ${IMGUI_SOURCES})

target_include_directories(ImGui PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
	$<INSTALL_INTERFACE:include>
)

# Add the compiler options
set_target_properties(ImGui PROPERTIES
	CXX_STANDARD			11
	CXX_STANDARD_REQUIRED	On
	DEBUG_POSTFIX			"d"
)
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
	target_compile_options(ImGui PRIVATE -Wall -Werror -Wpedantic -Wno-sign-compare)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
	target_compile_options(ImGui PRIVATE /W4 -D_CRT_SECURE_NO_WARNINGS)

	option(FORCE_STATIC_VCRT "Force /MT for static VC runtimes" OFF)
	if(FORCE_STATIC_VCRT)
		foreach(flagVar
			CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
			CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO
		)
			if(${flagVar} MATCHES "/MD")
				string(REGEX REPLACE "/MD" "/MT" ${flagVar} "${${flagVar}}")
			endif()
		endforeach()
	endif()
endif()

# Install the library
install(TARGETS ImGui EXPORT ImGuiTargets
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
	RUNTIME DESTINATION bin
	INCLUDES DESTINATION include
)
install(EXPORT ImGuiTargets
	FILE ImGuiTargets.cmake
	NAMESPACE ImGui::
	DESTINATION lib/cmake/ImGui
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file("ImGuiConfigVersion.cmake"
	VERSION ${IMGUI_VERSION}
	COMPATIBILITY SameMajorVersion
)
install(FILES "ImGuiConfig.cmake" "ImGuiConfigVersion.cmake"
	DESTINATION lib/cmake/ImGui
)

# Create the examples
if(IMGUI_BUILD_EXAMPLES)
	message(FATAL_ERROR "Examples not implemented yet")
endif()
