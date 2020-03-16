cmake_minimum_required(VERSION 2.8)
PROJECT(indi_pentax CXX C)

LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../cmake_modules/")

include(GNUInstallDirs)

set (INDI_PENTAX_VERSION_MAJOR 1)
set (INDI_PENTAX_VERSION_MINOR 0)

#libmtp needs the runpath, but is an indirect dependency and therefore will only get runpath if we set the following flags
set (CMAKE_EXE_LINKER_FLAGS "-Wl,--disable-new-dtags")

find_package(CFITSIO REQUIRED)
find_package(INDI REQUIRED)
find_package(ZLIB REQUIRED)
find_package(LibRaw REQUIRED)
find_package(JPEG REQUIRED)
find_package(GPHOTO2 REQUIRED)
find_package(PENTAX REQUIRED)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/indi_pentax.xml.cmake ${CMAKE_CURRENT_BINARY_DIR}/indi_pentax.xml )

include_directories( ${CMAKE_CURRENT_BINARY_DIR})
include_directories( ${CMAKE_CURRENT_SOURCE_DIR})
include_directories( ${INDI_INCLUDE_DIR})
include_directories( ${CFITSIO_INCLUDE_DIR})
include_directories( ${LibRaw_INCLUDE_DIR})
include_directories( ${PENTAX_INCLUDE_DIR})
include_directories( ${PKTRIGGERCORD_INCLUDE_DIR})
include_directories( ${GPHOTO2_INCLUDE_DIR})

include(CMakeCommon)

############# PENTAX CCD ###############
set(indipentax_SRCS
        ${CMAKE_CURRENT_SOURCE_DIR}/indi_pentax.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/pentax_ccd.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pentax_event_handler.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pktriggercord_ccd.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/../indi-gphoto/gphoto_readimage.cpp
)

#need to use rpath to find libraries, since they need to be in a subfolder to avoid conflicts with the standard version of libmtp
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

add_executable(indi_pentax ${indipentax_SRCS})

target_link_libraries(indi_pentax pthread ${PENTAX_LIBRARIES} ${INDI_LIBRARIES} ${JPEG_LIBRARIES} ${LibRaw_LIBRARIES} ${CFITSIO_LIBRARIES} ${ZLIB_LIBRARY})

install(TARGETS indi_pentax RUNTIME DESTINATION bin)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/indi_pentax.xml DESTINATION ${INDI_DATA_DIR})