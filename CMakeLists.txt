cmake_minimum_required(VERSION 3.1)

project(Hiberlite)

# get hiberlite sources
file(GLOB Hiberlite_SOURCES LIST_DIRECTORIES false "${CMAKE_CURRENT_SOURCE_DIR}/src/*")
file(GLOB hiberlite_HEADERS LIST_DIRECTORIES false "${CMAKE_CURRENT_SOURCE_DIR}/include/*")

# find sqlite or create the library
#find_package(sqlite3)
# cmake -Dsqlite3_LIBRARIES=/Users/rtoepfer/Scandy/scandy-core/dependencies/sqlite/Prebuilt/sqlite-apple-3.18.0/lib/libsqlite3.dylib -Dsqlite3_INCLUDE_DIRS=/Users/rtoepfer/Scandy/scandy-core/dependencies/sqlite/Prebuilt/sqlite-apple-3.18.0/include ../
if(EXISTS ${sqlite3_LIBRARIES})
  include_directories(${sqlite3_INCLUDE_DIRS})
elseif()
  include_directories("sqlite-amalgamation")
  set(Hiberlite_SOURCES "${Hiberlite_SOURCES};sqlite-amalgamation/sqlite3.c")
endif()

# create hiberlite static library
include_directories("include")
add_library(hiberlite STATIC ${Hiberlite_SOURCES})

# link sqlite if necessary
if(EXISTS ${sqlite3_LIBRARIES})
  target_link_libraries(hiberlite ${sqlite3_LIBRARIES})
endif()

set_property(TARGET hiberlite PROPERTY CXX_STANDARD 11)

# add pthreads
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
target_link_libraries(hiberlite Threads::Threads)

install(TARGETS hiberlite DESTINATION lib)
install(FILES ${hiberlite_HEADERS} DESTINATION include)

# create tests
add_executable(tests "tests.cpp")
target_link_libraries(tests hiberlite)

# create sample
add_executable(sample "sample.cpp")
target_link_libraries(sample hiberlite)
