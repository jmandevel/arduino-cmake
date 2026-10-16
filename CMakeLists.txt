cmake_minimum_required(VERSION 4.3.2)

project(arduino)

set(ARDUINO_PROFILE "" CACHE STRING "sketch.yaml profile (empty = use default_profile)")
set(ARDUINO_PORT "/dev/ttyACM0" CACHE STRING "device port")
set(ARDUINO_SKETCH_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/sketch" CACHE STRING "arduino sketch directory")
set(ARDUINO_SKETCH_BUILD_DIR "${CMAKE_CURRENT_BINARY_DIR}/sketch")
find_program(ARDUINO_CLI arduino-cli REQUIRED)

if (ARDUINO_PROFILE)
    set(ARDUINO_PROFILE_ARGS --profile "${ARDUINO_PROFILE}")
else()
    set(ARDUINO_PROFILE_ARGS "")
endif()

add_custom_target(compile-sketch
    COMMAND ${ARDUINO_CLI} compile ${ARDUINO_PROFILE_ARGS} --build-path "${ARDUINO_SKETCH_BUILD_DIR}" "${ARDUINO_SKETCH_SOURCE_DIR}"
    COMMENT "Compiling sketch at \"${ARDUINO_SKETCH_SOURCE_DIR}\" with profile \"${ARDUINO_PROFILE}\" to \"${ARDUINO_SKETCH_BUILD_DIR}\"" 
    VERBATIM
)

add_custom_target(upload-sketch
    COMMAND ${ARDUINO_CLI} upload ${ARDUINO_PROFILE_ARGS} --port "${ARDUINO_PORT}" --input-dir "${ARDUINO_SKETCH_BUILD_DIR}" "${ARDUINO_SKETCH_SOURCE_DIR}"
    DEPENDS compile-sketch
    COMMENT "Uploading sketch with profile \"${ARDUINO_PROFILE}\" at port \"${ARDUINO_PORT}\""
    VERBATIM
)
