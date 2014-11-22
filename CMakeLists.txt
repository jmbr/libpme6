project(particle-mesh-ewald CXX)

cmake_minimum_required(VERSION 2.8)

find_package(Armadillo REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(FFTW REQUIRED fftw3)

find_package(OpenMP)

include_directories(${ARMADILLO_INCLUDE_DIRS} ${FFTW_INCLUDE_DIRS})
add_definitions(${FFTW_CFLAGS})
# set(CXX_WARNING_FLAGS "-W -Wall -Wextra -Wconversion -Wshadow -Wpointer-arith -Wcast-qual -Wwrite-strings")
set(CXX_WARNING_FLAGS "-W -Wall")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -Wall ${OpenMP_CXX_FLAGS} ${CXX_WARNING_FLAGS} -std=c++11")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -DNDEBUG -O3 -finline-functions -funroll-loops")

add_library(pme pme.cpp pme.h cube.cpp cube.h linear-algebra.cpp linear-algebra.h spline.cpp spline.h)

add_executable(make-box make-box.cpp)
target_link_libraries(make-box ${ARMADILLO_LIBRARIES})

add_executable(dispersive dispersive.cpp)
target_link_libraries(dispersive pme ${ARMADILLO_LIBRARIES} ${FFTW_LDFLAGS} ${FFTW_LDFLAGS_OTHER} -lfftw3_omp)

add_executable(run-pme run-pme.cpp)
target_link_libraries(run-pme pme ${ARMADILLO_LIBRARIES} ${FFTW_LDFLAGS} ${FFTW_LDFLAGS_OTHER} -lfftw3_omp)
