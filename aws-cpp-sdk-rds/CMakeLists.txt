cmake_minimum_required(VERSION 2.6)
project(aws-cpp-sdk-rds)

  file(GLOB AWS_RDS_HEADERS
    "include/aws/rds/*.h"
  )

  file(GLOB AWS_RDS_MODEL_HEADERS
    "include/aws/rds/model/*.h"
  )

  file(GLOB AWS_RDS_SOURCE
    "source/*.cpp"
  )

  file(GLOB AWS_RDS_MODEL_SOURCE
    "source/model/*.cpp"
  )

  file(GLOB RDS_UNIFIED_HEADERS
    ${AWS_RDS_HEADERS}
    ${AWS_RDS_MODEL_HEADERS}
  )

  file(GLOB RDS_UNITY_SRC
    ${AWS_RDS_SOURCE}
    ${AWS_RDS_MODEL_SOURCE}
  )

  if(ENABLE_UNITY_BUILD)
    enable_unity_build("RDS" RDS_UNITY_SRC)
  endif()

  file(GLOB RDS_SRC
    ${RDS_UNIFIED_HEADERS}
    ${RDS_UNITY_SRC}
  )

  if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
      source_group("Header Files\\aws\\rds" FILES ${AWS_RDS_HEADERS})
      source_group("Header Files\\aws\\rds\\model" FILES ${AWS_RDS_MODEL_HEADERS})
      source_group("Source Files" FILES ${AWS_RDS_SOURCE})
      source_group("Source Files\\model" FILES ${AWS_RDS_MODEL_SOURCE})
    endif(MSVC)
  endif()

  set(RDS_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
    "${CORE_DIR}/include/"
  )

  include_directories(${RDS_INCLUDES})

  if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    add_definitions("-DAWS_RDS_EXPORTS")
  endif()

  add_library(aws-cpp-sdk-rds ${LIBTYPE} ${RDS_SRC})

  target_include_directories(aws-cpp-sdk-rds PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)
  
  target_link_libraries(aws-cpp-sdk-rds aws-cpp-sdk-core)

  if(SIMPLE_INSTALL)
      install (TARGETS aws-cpp-sdk-rds
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}
               LIBRARY DESTINATION lib
               RUNTIME DESTINATION bin)
  else()
    if(PLATFORM_CUSTOM)
      install_custom_library(aws-cpp-sdk-rds)
    else()
      install (TARGETS aws-cpp-sdk-rds
               ARCHIVE DESTINATION ${ARCHIVE_DIRECTORY}/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               LIBRARY DESTINATION lib/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME}
               RUNTIME DESTINATION bin/${SDK_INSTALL_BINARY_PREFIX}/${PLATFORM_INSTALL_QUALIFIER}/\${CMAKE_INSTALL_CONFIG_NAME})
    endif()
  endif()

  install (FILES ${AWS_RDS_HEADERS} DESTINATION include/aws/rds)
  install (FILES ${AWS_RDS_MODEL_HEADERS} DESTINATION include/aws/rds/model)

  if(MSVC)
    install (FILES nuget/aws-cpp-sdk-rds.autopkg DESTINATION nuget)
  endif()



