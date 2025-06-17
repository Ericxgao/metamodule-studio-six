cmake_minimum_required(VERSION 3.24)

if(NOT "${METAMODULE_SDK_DIR}" STREQUAL "")
	message("METAMODULE_SDK_DIR set by CMake variable ${METAMODULE_SDK_DIR}")
elseif (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
	message("METAMODULE_SDK_DIR set by environment variable ${METAMODULE_SDK_DIR}")
else()
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
	message("METAMODULE_SDK_DIR set to default: ${METAMODULE_SDK_DIR}")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(RackModules VERSION 1.0.0 DESCRIPTION "StudioSixPlusOne for Rack" LANGUAGES C CXX)

# Set C++20 standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Add option for virtual build
option(BUILD_VIRTUAL "Build for virtual environment" ON)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/rack-modules)

# Main library target
add_library(RackModules STATIC)

# Compiler definitions
target_compile_definitions(RackModules PRIVATE 
    __V1x 
    __PLUGIN
    $<$<BOOL:${BUILD_VIRTUAL}>:BUILD_VIRTUAL>
    _MB_CAPABLE=1
)

# Compiler options
target_compile_options(RackModules PRIVATE
    -fpermissive
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Linker options
target_link_options(RackModules PRIVATE
    -Wl,--gc-sections
)

# Source files
set(RACKMODULES_SOURCES
    ${SOURCE_DIR}/src/plugin.cpp
    # ${SOURCE_DIR}/src/templates/templateModule.cpp
    # ${SOURCE_DIR}/src/templates/templateTest.cpp
    # ${SOURCE_DIR}/src/templates/templateClassTest.cpp
    ${SOURCE_DIR}/src/modules/Maccomo.cpp
    ${SOURCE_DIR}/src/modules/CombFilter.cpp
    ${SOURCE_DIR}/src/modules/PolyShiftRegister.cpp
    ${SOURCE_DIR}/src/modules/LaLa.cpp
    ${SOURCE_DIR}/src/modules/LalaStereo.cpp
    ${SOURCE_DIR}/src/modules/Bascom.cpp
    ${SOURCE_DIR}/src/modules/PatchNotes.cpp
    ${SOURCE_DIR}/src/modules/Duffy.cpp
    ${SOURCE_DIR}/src/modules/Amburgh.cpp
    ${SOURCE_DIR}/src/modules/BascomExpander.cpp
    ${SOURCE_DIR}/src/modules/Zazel.cpp
    ${SOURCE_DIR}/src/modules/Bose.cpp
    ${SOURCE_DIR}/src/modules/Farini.cpp
    ${SOURCE_DIR}/src/modules/Eva.cpp
    ${SOURCE_DIR}/src/modules/Te.cpp
    ${SOURCE_DIR}/src/modules/Thru.cpp
    ${SOURCE_DIR}/src/modules/Mix.cpp
    ${SOURCE_DIR}/src/modules/Iverson.cpp
    ${SOURCE_DIR}/src/modules/Hula.cpp
    ${SOURCE_DIR}/src/modules/Zilah.cpp
    ${SOURCE_DIR}/src/modules/KSDelay.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Mult.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Vca3.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Vca2.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/k25AttenuvertMinus100.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Vca1.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/DiodeFeedbackRedGreenShape.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/DiodeFeedbackOneAndTwoShape.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/k25AttenuvertMinus50.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/DiodeFeedbackOneAndOneShape.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Mix5050.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Grrr.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/k25AttenuvertPlud100.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/DiodeFeedbackOneAndThreeShape.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Mix50100.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/ThroughShape.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Grrr100.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/k25AttenuvertPlud50.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Grrr50.cpp
    ${SOURCE_DIR}/src/dsp/WaveShapes/Mix100100.cpp
    # ${SOURCE_DIR}/test/third-party/fft/FFT.cpp
    # ${SOURCE_DIR}/test/third-party/fft/FFTData.cpp
)

target_sources(RackModules PRIVATE
    ${RACKMODULES_SOURCES}
)

# Include directories
target_include_directories(RackModules PRIVATE 
    ${SOURCE_DIR}/src
    ${SOURCE_DIR}/src/composites
    ${SOURCE_DIR}/src/composites/framework
    ${SOURCE_DIR}/src/ctrl
    ${SOURCE_DIR}/src/dsp
    ${SOURCE_DIR}/src/dsp/WaveShapes
    ${SOURCE_DIR}/src/templates
    ${SOURCE_DIR}/src/third-party/sqsrc/util
    ${SOURCE_DIR}/test/third-party
    ${SOURCE_DIR}/test/third-party/fft
    ${SOURCE_DIR}/utils

)

# Create the plugin
create_plugin(
    SOURCE_LIB      RackModules
    PLUGIN_NAME     StudioSixPlusOne
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 