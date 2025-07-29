# Tolk C# Example Application

# Find .NET compiler
find_program(CSC_EXECUTABLE csc)

if(NOT (CSC_EXECUTABLE))
    message(FATAL_ERROR "csc not found. Cannot build C# example.")
endif()

# Set directories
set(EXAMPLE_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(EXAMPLE_OUTPUT_DIR ${CMAKE_BINARY_DIR}/examples/csharp)
set(TOLKDOTNET_DLL ${CMAKE_BINARY_DIR}/TolkDotNet.dll)

# Create output directory
file(MAKE_DIRECTORY ${EXAMPLE_OUTPUT_DIR})

# C# source files
set(CSHARP_SOURCES
    ${EXAMPLE_SRC_DIR}/ConsoleApp.cs
)

# Build C# example executable
set(EXAMPLE_EXE ${EXAMPLE_OUTPUT_DIR}/ExampleCSharpApp.exe)

if(CSC_EXECUTABLE)
    # Use traditional csc compiler
    add_custom_command(
        OUTPUT ${EXAMPLE_EXE}
        COMMAND ${CSC_EXECUTABLE} /nologo 
                /reference:${TOLKDOTNET_DLL}
                /out:${EXAMPLE_EXE}
                ${CSHARP_SOURCES}
        DEPENDS ${CSHARP_SOURCES} ${TOLKDOTNET_DLL}
        COMMENT "Building C# example with csc"
        VERBATIM
    )
else()
    # dotnet CLI
endif()

# Create custom target
add_custom_target(ExampleAppCSharp ALL
    DEPENDS ${EXAMPLE_EXE}
    COMMENT "Building C# example application"
)

# Make sure this depends on TolkDotNet
add_dependencies(ExampleAppCSharp TolkDotNet)

# Install example executable
install(FILES ${EXAMPLE_EXE} DESTINATION lib)

# Install example source for reference
install(FILES ${CSHARP_SOURCES} DESTINATION examples/dotnet)
