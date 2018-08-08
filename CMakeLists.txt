cmake_minimum_required (VERSION 3.0)

set(PROJECT_NAME libltc)

project (${PROJECT_NAME} VERSION 1.3.0 LANGUAGES C)

option(BUILD_SHARED_LIBS "Build as shared libraries" OFF)


set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set(LIBLTC_PUBLIC_HEADERS src/ltc.h)
  
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

add_library(${PROJECT_NAME} ${LIBLTC_SOURCES} ${LIBLTC_HEADERS})

target_include_directories(${PROJECT_NAME} PUBLIC  
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>  
    $<INSTALL_INTERFACE:include/>
)

#MSVC compilation fix
if(MSVC) #TODO: "-Dinline=__inline" for old msvc
  target_compile_definitions(${PROJECT_NAME} PUBLIC "-D_CRT_SECURE_NO_WARNINGS")
endif(MSVC)

#Add 'd' letter at the end of the dll/lib name.
if(NOT CMAKE_DEBUG_POSTFIX)
  set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX "d")
endif()

if(UNIX)
  # Avoid CMake to add 'lib' prefix under Linux, as it's already included in the
  # library name.
  set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")
endif(UNIX)

# Installation & exporting.
install(FILES ${LIBLTC_PUBLIC_HEADERS} DESTINATION "include")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Version.cmake COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Version.cmake DESTINATION "share/${PROJECT_NAME}")

#Installation library
install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}Config
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin)

install(EXPORT ${PROJECT_NAME}Config DESTINATION "share/${PROJECT_NAME}")

#Install PDB files.
#if(MSVC)
#  install(FILES $<TARGET_PDB_FILE:${PROJECT_NAME}> DESTINATION "${ARCH_NAME}")
#endif(MSVC)