cmake_minimum_required (VERSION 3.5 FATAL_ERROR)

# Release Service version, managed by release script
set (RELEASE_SERVICE_VERSION_MAJOR "21")
set (RELEASE_SERVICE_VERSION_MINOR "08")
set (RELEASE_SERVICE_VERSION_MICRO "0")
set (RELEASE_SERVICE_COMPACT_VERSION "${RELEASE_SERVICE_VERSION_MAJOR}${RELEASE_SERVICE_VERSION_MINOR}${RELEASE_SERVICE_VERSION_MICRO}")

# Bump KNIGHTS_BASE_VERSION once new features are added
set(KNIGHTS_BASE_VERSION "2.6")
set(KNIGHTS_VERSION "${KNIGHTS_BASE_VERSION}.${RELEASE_SERVICE_COMPACT_VERSION}")

project(Knights VERSION ${KNIGHTS_VERSION})

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set (QT_MIN_VERSION "5.12.0")
set (KF5_MIN_VERSION "5.68.0")

find_package(ECM ${KF5_MIN_VERSION} REQUIRED CONFIG)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${ECM_MODULE_PATH})

if(CMAKE_COMPILER_IS_GNUCXX)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wpointer-arith -Wunreachable-code -fno-omit-frame-pointer -fstack-protector") 
endif(CMAKE_COMPILER_IS_GNUCXX)

#set(CMAKE_CXX_FLAGS_PROFILE "-fprofile-arcs -ftest-coverage -pg")

include(FeatureSummary)
find_package(Qt5 ${QT_MIN_VERSION} REQUIRED NO_MODULE COMPONENTS
   Concurrent
   Core
   Gui
   Widgets
   Svg
)

find_package(Qt5TextToSpeech ${QT_REQUIRED_VERSION} QUIET)
set_package_properties(Qt5TextToSpeech PROPERTIES
   PURPOSE "Speaking of opponents moves")
if (Qt5TextToSpeech_FOUND)
   add_definitions(-DHAVE_SPEECH)
endif ()

find_package(KF5 ${KF5_MIN_VERSION} REQUIRED COMPONENTS
  DBusAddons
  ConfigWidgets
  Crash
  TextWidgets
  XmlGui
  KIO
  Plotting
  DocTools
  Wallet
  Plasma
)

find_package(KF5KDEGames 4.9.0 REQUIRED)

include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDECompilerSettings NO_POLICY_SCOPE)

include(FeatureSummary)
include(ECMAddAppIcon)
include(ECMInstallIcons)
include(ECMQtDeclareLoggingCategory)
include(ECMSetupVersion)

add_definitions(
    -DQT_NO_FOREACH
    -DQT_NO_KEYWORDS
    -DQT_NO_CAST_TO_ASCII
    -DQT_NO_CAST_FROM_ASCII
    -DQT_NO_URL_CAST_FROM_STRING
    -DQT_NO_CAST_FROM_BYTEARRAY
    -DQT_USE_QSTRINGBUILDER
    -DQT_NO_NARROWING_CONVERSIONS_IN_CONNECT
    -DQT_STRICT_ITERATORS
    -DQT_DISABLE_DEPRECATED_BEFORE=0x050A00
    -DQT_DEPRECATED_WARNINGS_SINCE=0x060000
    -DKF_DISABLE_DEPRECATED_BEFORE_AND_AT=0x053900
    -DKF_DEPRECATED_WARNINGS_SINCE=0x060000
)

add_subdirectory(doc)
add_subdirectory(icons)
add_subdirectory(sounds)
add_subdirectory(src)
add_subdirectory(themes)

ecm_qt_install_logging_categories(EXPORT KNIGHTS FILE knights.categories DESTINATION ${KDE_INSTALL_LOGGINGCATEGORIESDIR})

ki18n_install(po)
kdoctools_install(po)

feature_summary(WHAT ALL
                INCLUDE_QUIET_PACKAGES
                FATAL_ON_MISSING_REQUIRED_PACKAGES)

