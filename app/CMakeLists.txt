# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.4.1)

set(CMAKE_EXE_LINKER_FLAGS  "${CMAKE_EXE_LINKER_FLAGS} -lm")
add_definitions(-DHAVE_SYS_UIO_H -DUSE_FILE32API)

set(LIB_NAME NativeLib)
set(${LIB_NAME}_SRCS
        src/main/cpp/src/native-lib.cpp
)

set(THIRD_PARTY_PATH "${CMAKE_CURRENT_SOURCE_DIR}/src/main/cpp/third_party/")
set(THIRD_PARTY_BOOST "${THIRD_PARTY_PATH}/boost-1.55/")
set(THIRD_PARTY_CERES "${THIRD_PARTY_PATH}/ceres-solver-2.0.0/")

set(OPENCV_PATH "${THIRD_PARTY_PATH}/OpenCV-3.4.2-android-sdk-c++_static//")

include_directories(
    ${OPENCV_PATH}/sdk/native/jni/include
#    ${THIRD_PARTY_PATH}/eigen3/
    ${THIRD_PARTY_BOOST}/include
)


set(OpenCV_DIR ${OPENCV_PATH}/sdk/native/jni)
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

#set(ceresPath   ${THIRD_PARTY_CERES}/include)
#include_directories(${ceresPath}/include)
#
#add_library(ceres_lib STATIC IMPORTED)
#set_target_properties(ceres_lib PROPERTIES IMPORTED_LOCATION ${THIRD_PARTY_CERES}/libs/android/arm64-v8a/libceres.a)

add_subdirectory(libs/VINS-Mobile-master)


add_subdirectory(src/main/cpp/src)


# Gradle automatically packages shared libraries with your APK.
add_library( 
    ${LIB_NAME}
    SHARED
    ${${LIB_NAME}_SRCS}
)

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( 
    # Sets the name of the path variable.
    log-lib
    
    # Specifies the name of the NDK library that
    # you want CMake to locate.
    log 
)

#add_dependencies(nativelib VINS_Android)

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.


target_link_libraries( 
    # Specifies the target library.
    NativeLib
    VINS_Android
    log
    ${OpenCV_LIBS}
    # Links the target library to the log library
    # included in the NDK.
    #${log-lib}
)