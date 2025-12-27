cmake_minimum_required(VERSION 3.24)

project(cuda_reaction_diffusion_lab
  VERSION 0.1.0
  LANGUAGES CXX CUDA
)

# ---- C++ / CUDA standards ----
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_CUDA_STANDARD 17)
set(CMAKE_CUDA_STANDARD_REQUIRED ON)

# Qt auto tools
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# Export compile_commands.json (nice for clangd)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# ---- Dependencies ----
find_package(Qt6 REQUIRED COMPONENTS Widgets OpenGL OpenGLWidgets)

# ---- Target ----
add_executable(${PROJECT_NAME}
  src/main.cpp
  src/app/MainWindow.h
  src/app/MainWindow.cpp
  src/app/GLView.h
  src/app/GLView.cpp
  src/sim/cuda_stub.cuh
  src/sim/cuda_stub.cu
)

target_include_directories(${PROJECT_NAME} PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_link_libraries(${PROJECT_NAME} PRIVATE
  Qt6::Widgets
  Qt6::OpenGL
  Qt6::OpenGLWidgets
)

# ---- CUDA specifics ----
set_target_properties(${PROJECT_NAME} PROPERTIES
  CUDA_SEPARABLE_COMPILATION ON
)

# Reasonable defaults; adjust as needed
if (MSVC)
  target_compile_options(${PROJECT_NAME} PRIVATE /W4)
else()
  target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -Wpedantic)
endif()

# NOTE:
# You may need to set CUDAToolkit_ROOT or CUDA toolkit path if CMake can't find it.
# Example:
# cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DCUDAToolkit_ROOT="C:/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v12.4"
