cmake_minimum_required(VERSION 2.8.12)

if(NOT MSVC)
    return()
endif()

project(SofaWinDependencies)

option(SOFAWINDEPENDENCIES_INSTALL_HEADERS "Specify wether or not header files will be copied during make install" OFF )
include(GNUInstallDirs)
set(SOFAWINDEPENDENCIES_INSTALL_BIN_DIR     ${CMAKE_INSTALL_BINDIR}     CACHE PATH "Installation directory for binaries relative to CMAKE_INSTALL_PREFIX variable")
set(SOFAWINDEPENDENCIES_INSTALL_LIB_DIR     ${CMAKE_INSTALL_LIBDIR}     CACHE PATH "Installation directory for libraries relative to CMAKE_INSTALL_PREFIX variable")
set(SOFAWINDEPENDENCIES_INSTALL_INCLUDE_DIR ${CMAKE_INSTALL_INCLUDEDIR} CACHE PATH "Installation directory for header files relative to CMAKE_INSTALL_PREFIX variable")

set(SOFAWIN_DLLS "")
set(SOFAWIN_LIBS "")
if(CMAKE_CL_64)
    set(CMAKE_LIBRARY_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib/win64)
    file(GLOB SOFAWIN_DLLS "${CMAKE_CURRENT_SOURCE_DIR}/lib/win64/*.dll")
    file(GLOB SOFAWIN_LIBS "${CMAKE_CURRENT_SOURCE_DIR}/lib/win64/*.lib")
else()
    list(APPEND CMAKE_LIBRARY_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib/win32)
    file(GLOB SOFAWIN_DLLS "${CMAKE_CURRENT_SOURCE_DIR}/lib/win32/*.dll")
    file(GLOB SOFAWIN_LIBS "${CMAKE_CURRENT_SOURCE_DIR}/lib/win32/*.lib")
endif()

foreach(CONFIG ${CMAKE_CONFIGURATION_TYPES})
    foreach(DLL ${SOFAWIN_DLLS})
        file(COPY ${DLL} DESTINATION "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${CONFIG}")
    endforeach()
endforeach()


set(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/include PARENT_SCOPE)
if(CMAKE_CL_64)
  set(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/lib/win64 PARENT_SCOPE)
else()
  set(CMAKE_LIBRARY_PATH ${CMAKE_LIBRARY_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/lib/win32 PARENT_SCOPE)
endif()

if( SOFAWINDEPENDENCIES_INSTALL_HEADERS )
    install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
            DESTINATION "${SOFAWINDEPENDENCIES_INSTALL_INCLUDE_DIR}"
            FILES_MATCHING 
                PATTERN "*.h"
                PATTERN "*.inl"
                PATTERN "*.hxx"
                PATTERN "*.hpp"
                PATTERN "*.ipp"
                PATTERN ".svn" EXCLUDE
            )
endif()

install( FILES ${SOFAWIN_DLLS} DESTINATION  ${SOFAWINDEPENDENCIES_INSTALL_BIN_DIR} )
install( FILES ${SOFAWIN_LIBS} DESTINATION  ${SOFAWINDEPENDENCIES_INSTALL_LIB_DIR} )