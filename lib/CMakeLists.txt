# Copyright 2011,2012,2016,2018,2019 Free Software Foundation, Inc.
#
# This file was generated by gr_modtool, a tool from the GNU Radio framework
# This file is a part of gr-dvbgse
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

list(APPEND dvbgse_sources
    bbheader_source_impl.cc
    bbheader_sink_impl.cc
)

set(dvbgse_sources "${dvbgse_sources}" PARENT_SCOPE)
if(NOT dvbgse_sources)
    MESSAGE(STATUS "No C++ sources... skipping lib/")
    return()
endif(NOT dvbgse_sources)

add_library(gnuradio-dvbgse SHARED ${dvbgse_sources})
target_link_libraries(gnuradio-dvbgse gnuradio::gnuradio-runtime ${PCAP_LIBRARIES})
target_include_directories(gnuradio-dvbgse
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    PUBLIC $<INSTALL_INTERFACE:include>
  )
set_target_properties(gnuradio-dvbgse PROPERTIES DEFINE_SYMBOL "gnuradio_dvbgse_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-dvbgse PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-dvbgse)

########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

# If your unit tests require special include paths, add them here
#include_directories()
# List all files that contain Boost.UTF unit tests here
list(APPEND test_dvbgse_sources
qa_bbheader_sink.cc
qa_bbheader_source.cc
)
# Anything we need to link to for the unit tests go here
list(APPEND GR_TEST_TARGET_DEPS gnuradio-dvbgse)

if(NOT test_dvbgse_sources)
    MESSAGE(STATUS "No C++ unit tests... skipping")
    return()
endif(NOT test_dvbgse_sources)

foreach(qa_file ${test_dvbgse_sources})
    GR_ADD_CPP_TEST("dvbgse_${qa_file}"
        ${CMAKE_CURRENT_SOURCE_DIR}/${qa_file}
    )
endforeach(qa_file)
