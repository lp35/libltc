cmake_minimum_required (VERSION 3.0)

set(PROJECT_NAME libltc)
project (${PROJECT_NAME} LANGUAGES C)

if( CMAKE_SIZEOF_VOID_P EQUAL 8 )
  SET(ARCH_X64 ON)
  SET(ARCH_NAME "x64")
  message("64 bits detected.")
else( CMAKE_SIZEOF_VOID_P EQUAL 8 )
  SET(ARCH_X64 OFF)
  SET(ARCH_NAME "x86")
  message("32 bits detected.")
endif( CMAKE_SIZEOF_VOID_P EQUAL 8 )

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set(LIBLTC_PUBLIC_HEADERS "${CMAKE_CURRENT_LIST_DIR}/src/ltc.h")
  
#For visual studio hierarchy
set(LIBLTC_HEADERS
    src/decoder.h
    src/encoder.h
    ${LIBLTC_PUBLIC_HEADERS}
)

#Sources
set(LIBLTC_SOURCES
    src/decoder.c
    src/encoder.c
    src/ltc.c
    src/timecode.c
)

#set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build)

add_library(${PROJECT_NAME}
  ${LIBLTC_SOURCES}
  ${LIBLTC_HEADERS}
  )

#Add header files for final installation process.
target_sources(${PROJECT_NAME} PUBLIC ${LIBLTC_PUBLIC_HEADERS})

target_include_directories(${PROJECT_NAME} PUBLIC 
  $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/src>
  $<INSTALL_INTERFACE:include>
)

#Add 'd' letter at the end of the dll/lib name.
if(NOT CMAKE_DEBUG_POSTFIX)
  set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX "d")
endif()

#Install headers
#get_target_property(LIBLTC_HEADERS ${PROJECT_NAME} INTERFACE_SOURCES)
install(FILES ${LIBLTC_PUBLIC_HEADERS} DESTINATION "include")

#Installation library
install(TARGETS ${PROJECT_NAME} 
  RUNTIME DESTINATION "${ARCH_NAME}"
  ARCHIVE DESTINATION "${ARCH_NAME}"
  LIBRARY DESTINATION "${ARCH_NAME}")

#Install PDB files.
#if(MSVC)
#  install(FILES $<TARGET_PDB_FILE:${PROJECT_NAME}> DESTINATION "${ARCH_NAME}")
#endif(MSVC)