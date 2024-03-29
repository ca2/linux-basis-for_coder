cmake_minimum_required(VERSION 3.16)
project(basis)


set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS OFF)
message(STATUS "CMAKE_SYSTEM_NAME is ${CMAKE_SYSTEM_NAME}")

if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")

   SET(CMAKE_CXX_FLAGS "-fPIC -fexceptions -fnon-call-exceptions -frtti")

endif()

if($ENV{XDG_CURRENT_DESKTOP} STREQUAL "KDE")
   set(KDE_DESKTOP TRUE)
   message(STATUS "System is KDE")
   set(DESKTOP_ENVIRONMENT_NAME "kde")
elseif($ENV{XDG_CURRENT_DESKTOP} STREQUAL "ubuntu:GNOME")
   set(GNOME_DESKTOP TRUE)
   message(STATUS "System is GNOME")
   set(DESKTOP_ENVIRONMENT_NAME "gnome")
elseif($ENV{XDG_CURRENT_DESKTOP} STREQUAL "GNOME")
   set(GNOME_DESKTOP TRUE)
   message(STATUS "System is GNOME")
   set(DESKTOP_ENVIRONMENT_NAME "gnome")
elseif($ENV{XDG_CURRENT_DESKTOP} STREQUAL "LXDE")
   set(LXDE_DESKTOP TRUE)
   message(STATUS "System is LXDE")
   set(DESKTOP_ENVIRONMENT_NAME "lxde")
endif()

message(STATUS "DESKTOP_ENVIRONMENT_NAME is ${DESKTOP_ENVIRONMENT_NAME}")

add_compile_options("$<$<CONFIG:DEBUG>:-DDEBUG>")

set(UNDERSCORE_OPERATING_SYSTEM $ENV{__SYSTEM_UNDERSCORE_OPERATING_SYSTEM})
set(SLASHED_OPERATING_SYSTEM $ENV{__SYSTEM_SLASHED_OPERATING_SYSTEM})
set(DISTRO $ENV{__SYSTEM_DISTRO})
set(DISTRO_RELEASE $ENV{__SYSTEM_DISTRO_RELEASE})


set(ROOT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})


set(default_gpu "gpu_opengl")


if(${CMAKE_SYSTEM_NAME} STREQUAL "macOS")


   set(CMAKE_INSTALL_RPATH "@loader_path")


elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")


   SET(CMAKE_SKIP_BUILD_RPATH  FALSE)
   SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
   set(CMAKE_INSTALL_RPATH $ORIGIN)
   #set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

   set(LINUX TRUE)
   set(OPERATING_SYSTEM_NAME "linux")
   set(OPERATING_SYSTEM_POSIX TRUE)
   set(FILE_SYSTEM_INOTIFY TRUE)
   set(POSIX_SPAWN TRUE)
   set(POSIX_LIST_SERIAL_PORTS TRUE)
   set(WITH_X11 TRUE)
   set(USE_OPENSSL TRUE)
   set(PTHREAD TRUE)


   message(STATUS "DISTRO is ${DISTRO}")

   if(${DISTRO} STREQUAL "ubuntu")

      set(UBUNTU TRUE)

      message(STATUS "UBUNTU has been set TRUE")

      set(APPINDICATOR_PKG_MODULE "appindicator3-0.1")

      set(MPG123_PKG_MODULE "mpg123")

   elseif(${DISTRO} STREQUAL "debian")

      set(DEBIAN TRUE)

      add_compile_definitions(DEBIAN_LINUX)

      message(STATUS "DEBIAN has been set TRUE")

      set(APPINDICATOR_PKG_MODULE "ayatana-appindicator3-0.1")

      set(MPG123_PKG_MODULE "libmpg123")

   elseif("${DISTRO}" STREQUAL "raspbian")

      set(RASPBIAN TRUE)

      set(DEBIAN_LIKE TRUE)

      set(DONT_USE_PKG_CONFIG FALSE)

      add_compile_definitions(RASPBIAN)

  else()

      set(APPINDICATOR_PKG_MODULE "appindicator3-0.1")

      set(MPG123_PKG_MODULE "mpg123")

   endif()

   message(STATUS "DISTRO_RELEASE is ${DISTRO_RELEASE}")


   set(ALSA_MIDI TRUE)
   set(INTERPROCESS_COMMUNICATION_SYSTEM_5 TRUE)

   add_compile_definitions(WITH_X11)
   link_libraries(pthread)
   include(FindPkgConfig)

   if(EXISTS $ENV{HOME}/__config/xfce.txt)

      set(LINUX_XFCE TRUE)
      message(STATUS "Adding Xfce/X11 dependency.")

   endif()

   if(GNOME_DESKTOP)

      message(STATUS "Adding GNOME/X11 dependency.")

   endif()

   if(KDE_DESKTOP)

      set(WITH_XCB TRUE)
      add_compile_definitions(WITH_XCB=1)

      set(QT_MIN_VERSION "5.3.0")
      set(KF5_MIN_VERSION "5.2.0")

      # apt install extra-cmake-modules
      # dnf install extra-cmake-modules
      find_package(ECM 1.0.0 REQUIRED NO_MODULE)
      set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH} ${ECM_KDE_MODULE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

      # apt install libkf5notifications-dev
      # dnf install kf5-knotifications-devel

      #include(KDEInstallDirs)
      #include(KDECMakeSettings)
      #include(KDECompilerSettings NO_POLICY_SCOPE)
      #    find_package(KF5 ${KF5_MIN_VERSION} REQUIRED COMPONENTS
      # CoreAddons      # KAboutData
      #          I18n            # KLocalizedString
      #         WidgetsAddons   # KMessageBox
      #      Notifications
      #     )
      #include(FeatureSummary)

      # Find Qt modules
      #find_package(Qt5 ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS
      #  Core    # QCommandLineParser, QStringLiteral
      #  Widgets # QApplication
      #  )
      find_package(KF5 ${KF5_MIN_VERSION} REQUIRED COMPONENTS
         # CoreAddons      # KAboutData
         #          I18n            # KLocalizedString
         #         WidgetsAddons   # KMessageBox
         CoreAddons
         Notifications
         ConfigWidgets
         KIO
         IconThemes
         )
      find_package(LibKWorkspace CONFIG REQUIRED)

      # Find KDE modules

      #feature_summary(WHAT ALL INCLUDE_QUIET_PACKAGES FATAL_ON_MISSING_REQUIRED_PACKAGES)
      #        find_package(KDE5 REQUIRED)
      message(STATUS "Adding KDE/xcb dependency.")
      #        file (STRINGS $ENV{HOME}/__config/knotifications/cflags.txt knotifications_cflags)
      #        file (STRINGS $ENV{HOME}/__config/knotifications/libs.txt knotifications_libs)
      #        if(knotifications_cflags STREQUAL "")
      #            set(knotifications_cflags -I/usr/include/KF5/KNotifications)
      #        endif()
      #        if(knotifications_libs STREQUAL "")
      #            set(knotifications_cflags -I/usr/include/KF5/KNotifications)
      #        endif()
   endif()

   set(default_draw2d "draw2d_cairo")
   set(default_imaging "imaging_freeimage")
   set(default_write_text "write_text_pango")
   set(default_audio "audio_alsa")
   set(default_music_midi "music_midi_alsa")
   set(default_windowing "windowing_win32")
   set(default_node "node_linux")
   add_compile_definitions(default_draw2d=draw2d_cairo)
   add_compile_definitions(default_imaging=imaging_freeimage)
   add_compile_definitions(default_write_text=write_text_pango)
   add_compile_definitions(default_audio=audio_alsa)
   add_compile_definitions(default_music_midi=music_midi_alsa)
   add_compile_definitions(default_node=node_linux)


elseif(WIN32)


   set(OPERATING_SYSTEM_NAME "windows")
   set(USE_OPENSSL TRUE)
   set(default_draw2d "draw2d_gdiplus")
   set(default_imaging "imaging_wic")
   set(default_write_text "write_text_win32")
   set(default_audio "audio_mmsystem")
   set(default_music_midi "music_midi_mmsystem")
   set(default_windowing "windowing_x11")
   set(default_node "node_windows")


endif()


if (LINUX)
   set(LINUX TRUE)
elseif (WIN32)
elseif (UWP)
   set(WINDOWS_TSF TRUE)
endif ()


if(LINUX)


   if(LXDE_DESKTOP)

      list(APPEND app_common_dependencies
         desktop_environment_gnome)

      set(default_windowing "windowing_x11")

      add_compile_definitions(DESKTOP_ENVIRONMENT_GNOME)

      add_compile_definitions(default_windowing=windowing_x11)

   endif()


   if(XFCE_DESKTOP)

      list(APPEND app_common_dependencies
         desktop_environment_xfce)

      set(default_windowing "windowing_x11")

      add_compile_definitions(DESKTOP_ENVIRONMENT_XFCE)

      add_compile_definitions(default_windowing=windowing_x11)

   endif()


   if(GNOME_DESKTOP)

      list(APPEND app_common_dependencies
         desktop_environment_gnome)

      set(default_windowing "windowing_x11")

      add_compile_definitions(DESKTOP_ENVIRONMENT_GNOME)

   endif()


   if(KDE_DESKTOP)

      list(APPEND app_common_dependencies
         desktop_environment_kde)

      set(default_windowing "windowing_xcb")

      add_compile_definitions(DESKTOP_ENVIRONMENT_KDE)

      add_compile_definitions(default_windowing=windowing_xcb)

   endif()


elseif(WIN32)

   set(app_common_dependencies)

endif()


if (MSVC)
   set(LIBCXX_TARGETING_MSVC ON)
else ()
   set(LIBCXX_TARGETING_MSVC OFF)
endif ()


add_compile_definitions(UNICODE)
add_compile_definitions(_UNICODE)


list(APPEND app_common_dependencies
   _console_application_build_helper)



#set(LIBRARY_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/time-${OPERATING_SYSTEM_NAME}/x64/basis)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/output)
#set(EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/time-${OPERATING_SYSTEM_NAME}/x64/basis)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/output)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/library)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/output)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/output)



set(APPLICATION_BUILD_HELPER $ENV{HOME}/bin/application_build_helper)


link_directories(${LIBRARY_OUTPUT_PATH})
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/storage-${OPERATING_SYSTEM_NAME}/library/x64/basis)
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/storage-${OPERATING_SYSTEM_NAME}/third/library/x64/basis)


include_directories(.)
include_directories($ENV{HOME}/__config)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source/app)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source/app/_include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source/port/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/source/port)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system)
if(OPERATING_SYSTEM_POSIX)
   include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/operating-system-posix)
   include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/operating-system-posix/_include)
endif()
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/operating-system-${OPERATING_SYSTEM_NAME})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/operating-system-${OPERATING_SYSTEM_NAME}/_include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/third-${OPERATING_SYSTEM_NAME}/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/third-${OPERATING_SYSTEM_NAME}/third/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/operating-system/third-${OPERATING_SYSTEM_NAME}/third/include/include)



add_subdirectory(source/app/acme)
add_subdirectory(operating-system/operating-system-linux/node_linux)
add_subdirectory(operating-system/operating-system-posix/acme_posix)
add_subdirectory(operating-system/operating-system-linux/acme_linux)
#add_subdirectory(operating-system/operating-system-linux/node_linux)
#add_subdirectory(source/console/application_build_helper)
#add_subdirectory(source/console/build)
#add_subdirectory(source/console/hello)
#add_subdirectory(source/console/helloearth)
add_subdirectory(source/app/apex)
#add_subdirectory(operating-system/operating-system-posix/apex_posix)
#add_subdirectory(operating-system/operating-system-linux/apex_linux)
#add_subdirectory(source/app/build)
#add_subdirectory(source/console/build)
add_subdirectory(source/app/aqua)
#add_subdirectory(source/console/hellosolarsystem)
#add_subdirectory(source/console/readlines)
#add_subdirectory(source/third/sn)
add_subdirectory(source/app/aura)
add_subdirectory(operating-system/operating-system-posix/aura_posix)
add_subdirectory(operating-system/operating-system-linux/aura_linux)
if(LXDE_DESKTOP)
   add_subdirectory(operating-system/operating-system-posix/windowing_x11)
   add_subdirectory(operating-system/operating-system-posix/node_gtk)
   add_subdirectory(operating-system/operating-system-posix/node_gnome)
   add_subdirectory(operating-system/operating-system-linux/desktop_environment_gnome)
endif()
if(XFCE_DESKTOP)
   add_subdirectory(operating-system/operating-system-posix/windowing_x11)
   add_subdirectory(operating-system/operating-system-posix/node_gtk)
   add_subdirectory(operating-system/operating-system-posix/node_xfce)
   add_subdirectory(operating-system/operating-system-linux/desktop_environment_xfce)
endif()
if(GNOME_DESKTOP)
    add_subdirectory(operating-system/operating-system-posix/windowing_x11)
    add_subdirectory(operating-system/operating-system-posix/node_gtk)
    add_subdirectory(operating-system/operating-system-posix/node_gnome)
    add_subdirectory(operating-system/operating-system-linux/desktop_environment_gnome)
endif()
if(KDE_DESKTOP)
    add_subdirectory(operating-system/operating-system-posix/windowing_xcb)
    add_subdirectory(operating-system/operating-system-posix/node_kde)
    add_subdirectory(operating-system/operating-system-linux/desktop_environment_kde)
endif()
add_subdirectory(source/app/draw2d_cairo)
#add_subdirectory(source/app/app)
#add_subdirectory(source/app/just_message_box)
#add_subdirectory(source/app/message_box)
#add_subdirectory(source/app/gpu_opengl)
#add_subdirectory(source/app/shader)
add_subdirectory(source/app/axis)
add_subdirectory(source/app/crypto_openssl)
add_subdirectory(source/app/compress_bzip2)
add_subdirectory(source/app/compress_zlib)
add_subdirectory(source/app/folder_zip)
add_subdirectory(source/app/database_sqlite3)
add_subdirectory(source/app/regular_expression_pcre2)
add_subdirectory(source/app/base)
add_subdirectory(source/app/experience_core)
add_subdirectory(source/app/experience_anthill)
add_subdirectory(source/app/experience_tranquillum)
add_subdirectory(source/app/imaging_freeimage)
add_subdirectory(operating-system/operating-system-posix/write_text_pango)
add_subdirectory(source/app/bred)
add_subdirectory(source/app/core)
add_subdirectory(source/for_coder/coder)



