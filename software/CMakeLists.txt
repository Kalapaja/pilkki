cmake_minimum_required(VERSION 3.20)


project(pilkki LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/deps/argparse/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/deps/magic_enum/include)

if (WIN32)
  add_library(serial_impl OBJECT src/serial_win.cpp)
elseif(APPLE)
  add_library(serial_impl OBJECT src/serial_mac.cpp)
elseif(UNIX)
  add_library(serial_impl OBJECT src/serial_linux.cpp)
  target_link_libraries(serial_impl PRIVATE udev)
else()
  message(FATAL "There is no implementation of serial for that platform")
endif()

add_executable(
  pilkki
  src/pilkki.cpp
)


target_link_libraries(pilkki PRIVATE serial_impl)

install(TARGETS pilkki DESTINATION bin)
