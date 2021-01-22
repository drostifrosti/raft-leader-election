cmake_minimum_required(VERSION 3.19.3)

project(raft-leader-election)
set(CMAKE_CXX_STANDARD 14)

# https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT.html
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
	set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/install" CACHE PATH "..." FORCE)
endif()

# TODO: globbing isn't best practice
file(GLOB TEST_SRC_FILES CONFIGURE_DEPENDS "test/*.cpp")
add_executable(tests ${TEST_SRC_FILES})
target_include_directories(tests PRIVATE include)
target_include_directories(tests PRIVATE third_party/googletest/googletest/include)

add_executable(distributed_lock examples/distributed_lock.cpp)
target_include_directories(distributed_lock PRIVATE include)

add_executable(leader_election examples/leader_election.cpp)
target_include_directories(leader_election PRIVATE include)

find_package(Boost 1.75.0 COMPONENTS date_time regex REQUIRED)
if(Boost_FOUND)
    target_include_directories(tests PRIVATE ${Boost_INCLUDE_DIRS})
	target_link_libraries(tests ${Boost_LIBRARIES})
	
	target_include_directories(distributed_lock PRIVATE ${Boost_INCLUDE_DIRS})
	target_link_libraries(distributed_lock ${Boost_LIBRARIES})
	
	target_include_directories(leader_election PRIVATE ${Boost_INCLUDE_DIRS})
	target_link_libraries(leader_election ${Boost_LIBRARIES})
endif()

add_subdirectory(third_party/googletest)
target_link_libraries(tests gtest)
