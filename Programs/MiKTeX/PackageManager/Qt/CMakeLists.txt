## CMakeLists.txt                                       -*- CMake -*-
##
## Copyright (C) 2008-2018 Christian Schenk
## 
## This file is free software; you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published
## by the Free Software Foundation; either version 2, or (at your
## option) any later version.
## 
## This file is distributed in the hope that it will be useful, but
## WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
## General Public License for more details.
## 
## You should have received a copy of the GNU General Public License
## along with this file; if not, write to the Free Software
## Foundation, 59 Temple Place - Suite 330, Boston, MA 02111-1307,
## USA.

set(MIKTEX_CURRENT_FOLDER "${MIKTEX_IDE_MIKTEX_PACKAGE_MANAGER_FOLDER}/Qt")

if(LINK_EVERYTHING_STATICALLY)
  use_static_crt()
endif()

include_directories(BEFORE
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}
)

set(mpm_qt_sources
  MainWindow.cpp
  MainWindow.h
  PackageProxyModel.cpp
  PackageProxyModel.h
  PackageTableModel.cpp
  PackageTableModel.h
  mpm.cpp
  mpm-version.h
)

if(MIKTEX_NATIVE_WINDOWS)
  list(APPEND mpm_qt_sources
    ${CMAKE_CURRENT_SOURCE_DIR}/mpm.rc
    ${MIKTEX_COMPATIBILITY_MANIFEST}
  )
endif()

set(mpm_qt_moc_sources
  MainWindow.h
  PackageProxyModel.h
  PackageTableModel.h
)

set(mpm_qt_ui_files
  MainWindow.ui
)

set(mpm_qt_rcs_files
  mpm.qrc
)

qt5_wrap_cpp(mpm_qt_mocs ${mpm_qt_moc_sources})

qt5_wrap_ui(mpm_qt_ui_headers ${mpm_qt_ui_files})

qt5_wrap_cpp(mpm_qt_ui_mocs ${mpm_qt_ui_headers})

qt5_add_resources(mpm_qt_rcc_sources ${mpm_qt_rcs_files})

set(sources
  ${mpm_qt_sources}
  ${mpm_qt_mocs}
  ${mpm_qt_ui_mocs}
  ${mpm_qt_rcc_sources}
)

set(libs
  ${core_dll_name}
  ${mpm_dll_name}
  ${ui_qt_dll_name}
  Qt5::Widgets
  miktex-popt-wrapper
)

add_executable(mpm_qt WIN32 ${sources})
set_property(TARGET mpm_qt PROPERTY FOLDER ${MIKTEX_CURRENT_FOLDER})
target_link_libraries(mpm_qt ${libs})  
install(TARGETS mpm_qt
  RUNTIME DESTINATION ${MIKTEX_BINARY_DESTINATION_DIR}
)

if(MIKTEX_NATIVE_WINDOWS)
  add_executable(mpm_qt${MIKTEX_ADMIN_SUFFIX} WIN32 ${sources})
  set_property(TARGET mpm_qt${MIKTEX_ADMIN_SUFFIX} PROPERTY FOLDER ${MIKTEX_CURRENT_FOLDER})
  add_link_flags(mpm_qt${MIKTEX_ADMIN_SUFFIX} "/MANIFESTUAC:level='requireAdministrator'")
  target_link_libraries(mpm_qt${MIKTEX_ADMIN_SUFFIX} ${libs})
  install(TARGETS mpm_qt${MIKTEX_ADMIN_SUFFIX}
    RUNTIME DESTINATION ${MIKTEX_BINARY_DESTINATION_DIR}
  )
endif()
