cmake_minimum_required(VERSION 2.8.11)
PROJECT(fidoroute CXX)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

if(CMAKE_BUILD_TYPE MATCHES Debug)
  ADD_DEFINITIONS(-g -DDEBUG -D_DEBUG)
  set(CMAKE_CONFIGURATION_TYPES "Debug" CACHE STRING "My multi config types" FORCE)
else(CMAKE_BUILD_TYPE MATCHES Debug)
  ADD_DEFINITIONS(-DNDEBUG)
  set(CMAKE_CONFIGURATION_TYPES "Release" CACHE STRING "My multi config types" FORCE)
endif(CMAKE_BUILD_TYPE MATCHES Debug)

if (MSVC)
set(CompilerFlags
    CMAKE_CXX_FLAGS
    CMAKE_CXX_FLAGS_DEBUG
    CMAKE_CXX_FLAGS_RELEASE
    CMAKE_C_FLAGS
    CMAKE_C_FLAGS_DEBUG
    CMAKE_C_FLAGS_RELEASE
  )
  foreach(CompilerFlag ${CompilerFlags})
    string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
  endforeach()
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/../out/lib")
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/../out/bin")
  ADD_DEFINITIONS(-DWIN32 -D_WINDOWS -D_CONSOLE -D_CRT_SECURE_NO_WARNINGS -D__MSVC__ -D__NT__ -D_CRT_NONSTDC_NO_DEPRECATE)
  include_directories("${CMAKE_CURRENT_SOURCE_DIR};${CMAKE_CURRENT_SOURCE_DIR}/h;${CMAKE_CURRENT_SOURCE_DIR}/../huskylib;${CMAKE_CURRENT_SOURCE_DIR}/../fidoconf;${CMAKE_CURRENT_SOURCE_DIR}/../smapi;")
  if(CMAKE_BUILD_TYPE MATCHES Debug)
    list( APPEND CMAKE_FIND_ROOT_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../out/lib/Debug" )
  else(CMAKE_BUILD_TYPE MATCHES Debug)
    list( APPEND CMAKE_FIND_ROOT_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../out/lib/Release" )
  endif(CMAKE_BUILD_TYPE MATCHES Debug)
else ()
  ADD_DEFINITIONS(-Wall -DUNIX)
endif (MSVC)

message("-------------------------")
message("System            : ${CMAKE_SYSTEM_NAME} ${CMAKE_SYSTEM_VERSION}")
message("Compiler          : ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}")
message("Prefix            : ${CMAKE_INSTALL_PREFIX} (run \"cmake . -DCMAKE_INSTALL_PREFIX:PATH=/other\" for other prefix)")
message("Build type        : ${CMAKE_CONFIGURATION_TYPES}")
message("-------------------------")

ADD_EXECUTABLE(fidoroute
  fidoroute.cpp
)
  INSTALL(TARGETS fidoroute
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
  )
if (NOT MSVC)
  include(GNUInstallDirs)
  install(FILES "fidoroute.1" DESTINATION "${CMAKE_INSTALL_MANDIR}/man1")
  install(FILES "fidoroute.conf.5" DESTINATION "${CMAKE_INSTALL_MANDIR}/man5")
  install(FILES "fidoroute.conf" DESTINATION "${CMAKE_INSTALL_DOCDIR}/")
  set(CPACK_GENERATOR ${CPACK_GENERATOR} TGZ)
else()
  set(CPACK_GENERATOR ${CPACK_GENERATOR} 7Z)
  install(FILES "fidoroute.conf" DESTINATION "/")
endif (NOT MSVC)

set (CPACK_STRIP_FILES TRUE)
set (CPACK_PACKAGE_VERSION_MAJOR "1")
set (CPACK_PACKAGE_VERSION_MINOR "37")
set (CPACK_PACKAGE_VERSION_PATCH "0")
INCLUDE(CPack)
