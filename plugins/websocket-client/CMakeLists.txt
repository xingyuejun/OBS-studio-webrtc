project(websocketclient)

# -----------------------------------------------------------------------
# Global set up
#
set(CMAKE_CXX_STANDARD 11)
if( WIN32 )
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4503 /wd4267 /wd4244 /wd4996")
endif()
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/restclient-cpp/include)

find_package(LibObs REQUIRED)
include_directories(${LIBOBS_INCLUDE_DIRS})

#------------------------------------------------------------------------
# OpenSSL included in libwebrtc
#

#------------------------------------------------------------------------
# Asio
#
include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/third_party/asio/include )

include_directories( ${CURL_INCLUDE_DIR} )

# WebSocket
#
set( websocketclient_SOURCES 
  JanusWebsocketClientImpl.cpp
  SpankChainWebsocketClientImpl.cpp
  MillicastWebsocketClientImpl.cpp
  WebsocketClient.cpp
  restclient-cpp/source/connection.cc
  restclient-cpp/source/helpers.cc
  restclient-cpp/source/restclient.cc
)
if( WIN32 )
  set( websocketclient_SOURCES ${websocketclient_SOURCES} dllmain.cpp )
  add_definitions( -DWEBSOCKETCLIENT_EXPORTS )
elseif( APPLE )
  add_definitions( -DWEBSOCKETCLIENT_EXPORTS )
endif()

set( websocketclient_HEADERS
  json.hpp
  JanusWebsocketClientImpl.h
  SpankChainWebsocketClientImpl.h
  MillicastWebsocketClientImpl.h
  WebsocketClient.h
  )

add_library( websocketclient SHARED
  ${websocketclient_SOURCES}
  ${websocketclient_HEADERS}
  )

if (WIN32)
  set (curl_path "${CURL_INCLUDE_DIR}/../bin/libcurl.lib")
elseif (UNIX AND NOT APPLE)
  set (curl_path "${CURL_INCLUDE_DIR}/../lib/libcurl.a")    
elseif (APPLE)
  set (curl_path "/usr/lib/libcurl.dylib")
endif() 
   
target_link_libraries(websocketclient ${curl_path})


find_package(OpenSSL REQUIRED)
target_link_libraries(websocketclient OpenSSL::SSL)



target_link_libraries(websocketclient ${OPENSSL_LIBRARIES})

install_obs_plugin(websocketclient)
