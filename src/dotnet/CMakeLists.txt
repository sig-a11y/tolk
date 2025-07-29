# TolkDotNet .NET wrapper library

# Find .NET compiler
find_program(DOTNET_EXECUTABLE dotnet)
find_program(CSC_EXECUTABLE csc)
find_program(RC_EXECUTABLE rc)

if(NOT RC_EXECUTABLE)
    message(FATAL_ERROR "Resource compiler (rc.exe) not found. Make sure Visual Studio is properly installed.")
endif()

# Resource file compilation
set(RC_SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/TolkDotNet.rc)
set(RC_OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/TolkDotNet.res)

add_custom_command(
    OUTPUT ${RC_OUTPUT}
    COMMAND ${RC_EXECUTABLE} /nologo /fo ${RC_OUTPUT} ${RC_SOURCE}
    DEPENDS ${RC_SOURCE} ${CMAKE_SOURCE_DIR}/src/TolkVersion.h
    COMMENT "Compiling .NET resource file"
    VERBATIM
)

# C# source files
set(CS_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Tolk.cs)

# TolkDotNet.dll compilation
set(DOTNET_DLL ${CMAKE_BINARY_DIR}/TolkDotNet.dll)

if(CSC_EXECUTABLE)
    # Use traditional csc compiler
    add_custom_command(
        OUTPUT ${DOTNET_DLL}
        COMMAND ${CSC_EXECUTABLE} /nologo /optimize /target:library /warn:4 
                /win32res:${RC_OUTPUT} /out:${DOTNET_DLL} ${CS_SOURCES}
        DEPENDS ${CS_SOURCES} ${RC_OUTPUT}
        COMMENT "Building TolkDotNet.dll with csc"
        VERBATIM
    )
elseif(DOTNET_EXECUTABLE)
    # Use modern dotnet CLI (requires creating a .csproj file)
    configure_file(
        ${CMAKE_CURRENT_SOURCE_DIR}/TolkDotNet.csproj.in
        ${CMAKE_CURRENT_BINARY_DIR}/TolkDotNet.csproj
        @ONLY
    )
    
    add_custom_command(
        OUTPUT ${DOTNET_DLL}
        COMMAND ${DOTNET_EXECUTABLE} build ${CMAKE_CURRENT_BINARY_DIR}/TolkDotNet.csproj 
                -o ${CMAKE_BINARY_DIR} -c Release
        DEPENDS ${CS_SOURCES} ${RC_OUTPUT}
        COMMENT "Building TolkDotNet.dll with dotnet CLI"
        VERBATIM
    )
endif()

# Create custom target
add_custom_target(TolkDotNet ALL
    DEPENDS ${DOTNET_DLL}
    COMMENT "Building .NET wrapper"
)

# Make TolkDotNet depend on main Tolk library
add_dependencies(TolkDotNet Tolk)

# Install TolkDotNet.dll
install(FILES ${DOTNET_DLL} DESTINATION lib)

# Install .NET source for reference
install(FILES ${CS_SOURCES} DESTINATION src/dotnet)
