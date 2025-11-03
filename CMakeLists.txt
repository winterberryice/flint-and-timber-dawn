cmake_minimum_required(VERSION 3.28)
project(DawnPrebuilt)

# ============ WebGPU Configuration ============
# Set WebGPU backend to Dawn
set(WEBGPU_BACKEND "DAWN" CACHE STRING "WebGPU backend to use")

# Build Dawn from source
set(WEBGPU_BUILD_FROM_SOURCE ON CACHE BOOL "Build Dawn from source")

# Platform-specific settings
if(UNIX AND NOT APPLE)
    set(DAWN_ENABLE_WAYLAND ON CACHE BOOL "Enable Wayland support")
    set(DAWN_USE_WAYLAND ON CACHE BOOL "Use Wayland platform")
    set(DAWN_ENABLE_X11 ON CACHE BOOL "Enable X11 support")
endif()

set(DAWN_FETCH_DEPENDENCIES ON CACHE BOOL "Let Dawn fetch dependencies")

# Speed up build by disabling unnecessary Dawn features
set(TINT_BUILD_TESTS OFF CACHE BOOL "Skip Tint tests")
set(DAWN_BUILD_SAMPLES OFF CACHE BOOL "Skip Dawn samples")
set(DAWN_USE_GLFW OFF CACHE BOOL "Skip GLFW in Dawn")

# Add WebGPU-distribution (Dawn)
add_subdirectory(third_party/webgpu)

# Create a dummy executable just to trigger the build
# (We only care about the Dawn libraries that get built)
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/dummy.cpp "int main() { return 0; }")
add_executable(dawn-dummy ${CMAKE_CURRENT_BINARY_DIR}/dummy.cpp)

# Link to webgpu to ensure it gets built
target_link_libraries(dawn-dummy PRIVATE webgpu)

# Include directories (needed for successful linking)
target_include_directories(dawn-dummy PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/third_party/webgpu/include
)