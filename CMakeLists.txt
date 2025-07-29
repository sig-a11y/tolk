cmake_minimum_required(VERSION 3.10)
project(Tolk)

option (TOLK_MULTITHREADED "Build Multi-Threaded (/MT) instead of Multi-Threaded DLL (/MD)" OFF)
option (BUILD_EXAMPLES "Build example applications" OFF)

if (TOLK_MULTITHREADED)
  foreach (flag_var
      CMAKE_C_FLAGS CMAKE_C_FLAGS_DEBUG CMAKE_C_FLAGS_RELEASE
      CMAKE_C_FLAGS_MINSIZEREL CMAKE_C_FLAGS_RELWITHDEBINFO
      CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
      CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
    if (${flag_var} MATCHES "/MD")
      STRING(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
    endif()
  endforeach(flag_var)
endif()

set (TOLK_COMPILE_FLAGS -D_EXPORTING -DUNICODE -D_UNICODE)
set (TOLK_LINK_FLAGS user32 ole32 oleaut32 sapi)

# Find Java JDK so we can enable JNI support
option (TOLK_WITH_JAVAJNI "Build Tolk with Java support" OFF)
if (TOLK_WITH_JAVAJNI)
  find_package(JNI)
  if (JNI_FOUND)
    # Enable JNI support in C++ code
    include_directories (${JNI_INCLUDE_DIRS})
    list (APPEND TOLK_COMPILE_FLAGS -D_WITH_JNI)
    list (APPEND TOLK_LINK_FLAGS ${JNI_LIBRARIES})
  else()
    message(WARNING "JNI not found. Specify JAVA_HOME environment variable to help CMake find Java JDK")
  endif(JNI_FOUND)

  # Build Tolk.jar
  find_package(Java COMPONENTS Development Runtime)
  if (Java_FOUND)
    add_subdirectory(src/java)
    message(STATUS "Java JNI support enabled and JAR will be built")

    # Build Java examples if requested
    if (BUILD_EXAMPLES)
      add_subdirectory(examples/java)
    endif()
  else()
    message(WARNING "Java compiler not found. Make sure Java Development Kit (JDK) is installed")
  endif(Java_FOUND)
endif(TOLK_WITH_JAVAJNI)

# Enable .NET support if available (Windows only)
find_program(RC_EXECUTABLE rc)
if(RC_EXECUTABLE)
  find_program(DOTNET_EXECUTABLE dotnet)
  find_program(CSC_EXECUTABLE csc)
  if(DOTNET_EXECUTABLE OR CSC_EXECUTABLE)
    option(BUILD_DOTNET "Build .NET wrapper" ON)
    message(STATUS "Will Build .NET wrapper: TolkDotNet.dll")
  else()
    option(BUILD_DOTNET "Build .NET wrapper" OFF)
    message(WARNING "Neither dotnet nor csc found. .NET wrapper will not be built.")
  endif()
  if(BUILD_DOTNET)
    add_subdirectory(src/dotnet)
  endif()
else()
  message(STATUS ".NET wrapper need rc.exe which is only supported on Windows. Skipping .NET build.")
endif()

add_library(Tolk SHARED src/Tolk.cpp src/Tolk.h src/TolkVersion.h src/TolkJNI.cpp
  src/ScreenReaderDriver.h src/Tolk.rc
  src/ScreenReaderDriverJAWS.cpp src/ScreenReaderDriverJAWS.h
  src/ScreenReaderDriverNVDA.cpp src/ScreenReaderDriverNVDA.h
  src/ScreenReaderDriverSA.cpp src/ScreenReaderDriverSA.h
  src/ScreenReaderDriverSNova.cpp src/ScreenReaderDriverSNova.h
  src/ScreenReaderDriverWE.cpp src/ScreenReaderDriverWE.h
  src/ScreenReaderDriverZDSR.cpp src/ScreenReaderDriverZDSR.h
  src/ScreenReaderDriverZT.cpp src/ScreenReaderDriverZT.h
  src/ScreenReaderDriverSAPI.cpp src/ScreenReaderDriverSAPI.h
  src/fsapi.c src/fsapi.h src/wineyes.c src/wineyes.h src/zt.c src/zt.h)

target_include_directories (Tolk INTERFACE src)
target_link_libraries (Tolk PRIVATE ${TOLK_LINK_FLAGS})
target_compile_options (Tolk PRIVATE ${TOLK_COMPILE_FLAGS})

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
  target_link_libraries(Tolk PUBLIC -static-libgcc;-static-libstdc++)
endif()

## ---- install
# add README
install (FILES docs/README.md DESTINATION docs)
# add LICENSE
install (FILES LICENSE.txt LICENSE-NVDA.txt DESTINATION license)
# add Binay DLL
install (TARGETS Tolk DESTINATION lib)
# Install PDB files for Debug builds
install (FILES Tolk.pdb DESTINATION lib OPTIONAL)
# add header file
install (FILES src/Tolk.h DESTINATION include)
# add Client libraries
if (${CMAKE_GENERATOR_PLATFORM} MATCHES "x64")
  install (FILES libs/x64/nvdaControllerClient64.dll libs/x64/SAAPI64.dll DESTINATION lib)
  install (FILES libs/x64/ZDSRAPI_x64.dll libs/x64/ZDSRAPI.ini DESTINATION lib)
else()
  install (FILES libs/x86/dolapi32.dll libs/x86/nvdaControllerClient32.dll libs/x86/SAAPI32.dll DESTINATION lib)
  install (FILES libs/x86/ZDSRAPI.dll libs/x64/ZDSRAPI.ini DESTINATION lib)
endif()
