cmake_minimum_required(VERSION 3.27)
project("clay" VERSION 0.12)


option(BUILD_EXAMPLES "Build examples")

if(BUILD_EXAMPLES)
  add_subdirectory("examples/cpp-project-example")

  # Don't try to compile C99 projects using MSVC
  if(NOT MSVC)
    add_subdirectory("examples/raylib-sidebar-scrolling-container")
    add_subdirectory("examples/cairo-pdf-rendering")
    add_subdirectory("examples/clay-official-website")
  endif()
endif()
       



# add_library("clay" INTERFACE)
# target_include_directories("clay" INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}> $<INSTALL_INTERFACE:include>)


# install(TARGETS clay EXPORT clayTargets)

# include(GNUInstallDirs)
# install(FILES "${CMAKE_CURRENT_LIST_DIR}/clay.h" DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
# # install(FILES "${CMAKE_CURRENT_LIST_DIR}/LICENSE" DESTINATION ${CMAKE_INSTALL_DOCDIR})
# install(FILES "${CMAKE_CURRENT_LIST_DIR}/README.md" DESTINATION ${CMAKE_INSTALL_DOCDIR})

# if(APPLE OR CMAKE_SYSTEM_NAME STREQUAL Linux)
#   # this might be a bit too restrictive, since for other (BSD, ...) this might apply also
#   # but this can be fixed later in extra pull requests from people on the platform
#   install(FILES clay-config.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/clay)
#   install(EXPORT clayTargets DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/clay)
# else()
#   install(FILES clay-config.cmake DESTINATION CMake)
#   install(EXPORT clayTargets DESTINATION CMake)
# endif()

option(CLAY_INSTALL "Include an install target" ON)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

add_library(clay INTERFACE)
add_library(clay::clay ALIAS clay)

# target_compile_features(clay INTERFACE cxx_std_17)
target_include_directories(clay INTERFACE
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>)

if(CLAY_INSTALL)
  install(TARGETS clay EXPORT clayConfig)
  install(EXPORT clayConfig
          NAMESPACE clay::
          DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})
  install(FILES ${CMAKE_CURRENT_LIST_DIR}/clay.h
          DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/clay)


  set(CONFIG_FILE_NAME_WITHOUT_EXT "${PROJECT_NAME}Config")
  set(CMAKE_CONFIG_FILE_BASENAME "${CMAKE_CURRENT_BINARY_DIR}/${CONFIG_FILE_NAME_WITHOUT_EXT}")
  set(CMAKE_CONFIG_VERSION_FILE_NAME "${CMAKE_CONFIG_FILE_BASENAME}-version.cmake")
  set(CMAKE_CONFIG_FILE_NAME "${CMAKE_CONFIG_FILE_BASENAME}.cmake")

  if(${CMAKE_VERSION} VERSION_GREATER "3.14")
  	set(OPTIONAL_ARCH_INDEPENDENT "ARCH_INDEPENDENT")
  endif()

  write_basic_package_version_file("${CMAKE_CONFIG_VERSION_FILE_NAME}"
      COMPATIBILITY SameMajorVersion
      ${OPTIONAL_ARCH_INDEPENDENT}
  )

  export(EXPORT clayConfig
         NAMESPACE clay::)

  install(FILES "${CMAKE_CONFIG_VERSION_FILE_NAME}"
         DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")

  # set(PackagingTemplatesDir "${CMAKE_CURRENT_SOURCE_DIR}/packaging")

  # set(CPACK_PACKAGE_NAME "${PROJECT_NAME}")
  # set(CPACK_PACKAGE_VENDOR "argparse (C++) developers")
  # set(CPACK_PACKAGE_DESCRIPTION "${PROJECT_DESCRIPTION}")
  # set(CPACK_DEBIAN_PACKAGE_NAME "${CPACK_PACKAGE_NAME}")
  # set(CPACK_RPM_PACKAGE_NAME "${CPACK_PACKAGE_NAME}")
  # set(CPACK_PACKAGE_HOMEPAGE_URL "${PROJECT_HOMEPAGE_URL}")
  # set(CPACK_PACKAGE_MAINTAINER "Pranav Srinivas Kumar")
  # set(CPACK_DEBIAN_PACKAGE_MAINTAINER "${CPACK_PACKAGE_MAINTAINER}")
  # set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")
  # set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/README.md")

  # set(CPACK_DEBIAN_PACKAGE_NAME "lib${PROJECT_NAME}-dev")
  # set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6-dev")
  # set(CPACK_DEBIAN_PACKAGE_SUGGESTS "cmake, pkg-config, pkg-conf")

  # set(CPACK_RPM_PACKAGE_NAME "lib${PROJECT_NAME}-devel")
  # set(CPACK_RPM_PACKAGE_SUGGESTS "${CPACK_DEBIAN_PACKAGE_SUGGESTS}")

  # set(CPACK_DEB_COMPONENT_INSTALL ON)
  # set(CPACK_RPM_COMPONENT_INSTALL ON)
  # set(CPACK_NSIS_COMPONENT_INSTALL ON)
  # set(CPACK_DEBIAN_COMPRESSION_TYPE "xz")

  # set(PKG_CONFIG_FILE_NAME "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc")
  # configure_file("${PackagingTemplatesDir}/pkgconfig.pc.in" "${PKG_CONFIG_FILE_NAME}" @ONLY)
  # install(FILES "${PKG_CONFIG_FILE_NAME}"
  #       DESTINATION "${CMAKE_INSTALL_LIBDIR}/pkgconfig"
  # )
endif()
