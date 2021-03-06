message("Checking cmake version...")
cmake_minimum_required(VERSION 3.0)

message("")
message("################################################################################")
message("##################################### GCC ######################################")
message("################################################################################")
message("")

# Type: "make" in order to build the project.

message("Setting LINUX variable, according to an existing LINUX (but not APPLE) OS...")
if(UNIX AND NOT APPLE)
  set(LINUX TRUE)
endif()

message("Setting project name...")
project (rope)

message("Creating a SOURCES variable with a link to all cpp files to compile...")
set(SOURCES
$ENV{NEUTRINO_PATH}/Code/src/main.cpp
$ENV{NEUTRINO_PATH}/Code/src/openclgl.cpp
$ENV{NEUTRINO_PATH}/Code/src/linear_algebra.cpp
$ENV{NEUTRINO_PATH}/Code/src/projective_geometry.cpp
$ENV{NEUTRINO_PATH}/Code/src/utilities.cpp
${CMAKE_HOME_DIRECTORY}/src/program.cpp)

message("Setting directory for the Mac executable...")
if(APPLE)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_HOME_DIRECTORY}/build/mac)
endif(APPLE)

message("Setting directory for the Linux executable...")
if(LINUX)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_HOME_DIRECTORY}/build/linux)
endif(LINUX)

message("Adding an executable with the above sources...")
add_executable(rope ${SOURCES})

message("Setting the directories that should be included in the build command...")
target_include_directories(rope PRIVATE $ENV{NEUTRINO_PATH}/Code/include)

message("Setting the Mac libraries that should be linked in the build command...")
if(APPLE)
  message("Setting the libraries that should be linked in the build command...")
  target_link_libraries(rope "-framework OpenGL" "-framework OpenCL" "-lGLEW" "-lglfw" "-lm")
endif(APPLE)

message("Setting the Linux libraries that should be linked in the build command...")
if(LINUX)
  message("Setting the libraries that should be linked in the build command...")
  target_link_libraries(rope "-lOpenGL" "-lOpenCL" "-lGLEW" "-lglfw" "-lm")
endif(LINUX)

# Type: "make run" to run the executable.
add_custom_target(run
    WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}
    COMMAND rope
    VERBATIM )

message("DONE configuring GCC!")

message("")
message("################################################################################")
message("#################################### DOXYGEN ###################################")
message("################################################################################")
message("")

# Type: "make doc" in order to build the Doxygen documentation of the project.

message("Checking if Doxygen is installed...")
find_package(Doxygen)
if (DOXYGEN_FOUND)
    message("Setting input and output files...")
    set(DOXYGEN_IN ${CMAKE_HOME_DIRECTORY}/../Docs/Doxyfile.in)
    set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

    message("Requesting to configure the file...")
    configure_file(${DOXYGEN_IN} ${DOXYGEN_OUT} @ONLY)
    message("DONE configuring Doxygen!")

    add_custom_target(doc
        COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        COMMENT "Generating API documentation with Doxygen"
        VERBATIM )
else (DOXYGEN_FOUND)
  message("Doxygen need to be installed to generate the doxygen documentation!")
endif (DOXYGEN_FOUND)
