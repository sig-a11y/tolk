# Tolk Java Example Application - Build from source

# Find Java components
find_package(Java COMPONENTS Development Runtime REQUIRED)

# Set directories
set(EXAMPLE_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(EXAMPLE_OUTPUT_DIR ${CMAKE_BINARY_DIR}/examples/java)
set(EXAMPLE_CLASSES_DIR ${EXAMPLE_OUTPUT_DIR}/classes)

# Create output directories
file(MAKE_DIRECTORY ${EXAMPLE_OUTPUT_DIR})
file(MAKE_DIRECTORY ${EXAMPLE_CLASSES_DIR})

# Java source files
set(TOLK_JAVA_SOURCE ${CMAKE_SOURCE_DIR}/src/java/com/davykager/tolk/Tolk.java)
set(EXAMPLE_JAVA_SOURCE ${EXAMPLE_SRC_DIR}/ConsoleApp.java)

# Compile Tolk Java class
add_custom_command(
    OUTPUT ${EXAMPLE_CLASSES_DIR}/com/davykager/tolk/Tolk.class
    COMMAND ${Java_JAVAC_EXECUTABLE} 
            -d ${EXAMPLE_CLASSES_DIR}
            ${TOLK_JAVA_SOURCE}
    DEPENDS ${TOLK_JAVA_SOURCE}
    COMMENT "Compiling Tolk Java source"
    VERBATIM
)

# Compile example application
add_custom_command(
    OUTPUT ${EXAMPLE_CLASSES_DIR}/ConsoleApp.class
    COMMAND ${Java_JAVAC_EXECUTABLE} 
            -cp ${EXAMPLE_CLASSES_DIR}
            -d ${EXAMPLE_CLASSES_DIR}
            ${EXAMPLE_JAVA_SOURCE}
    DEPENDS ${EXAMPLE_JAVA_SOURCE} ${EXAMPLE_CLASSES_DIR}/com/davykager/tolk/Tolk.class
    COMMENT "Compiling Java example application"
    VERBATIM
)

# Create example JAR file
set(EXAMPLE_JAR_FILE ${EXAMPLE_OUTPUT_DIR}/ExampleAppJava.jar)

add_custom_command(
    OUTPUT ${EXAMPLE_JAR_FILE}
    COMMAND ${Java_JAR_EXECUTABLE} 
            cfe ${EXAMPLE_JAR_FILE} ConsoleApp
            -C ${EXAMPLE_CLASSES_DIR} .
    DEPENDS ${EXAMPLE_CLASSES_DIR}/ConsoleApp.class ${EXAMPLE_CLASSES_DIR}/com/davykager/tolk/Tolk.class
    COMMENT "Creating ExampleAppJava.jar"
    VERBATIM
)

# Create custom target
add_custom_target(ExampleAppJava ALL
    DEPENDS ${EXAMPLE_JAR_FILE}
    COMMENT "Building Java example application from source"
)

# Make sure this depends on the main Tolk library (for JNI)
add_dependencies(ExampleAppJava Tolk)

# Install example JAR
install(FILES ${EXAMPLE_JAR_FILE} DESTINATION examples/java)

# Install example source for reference
install(FILES ${EXAMPLE_JAVA_SOURCE} DESTINATION examples/java/src)

# Create a run script for the example
set(RUN_SCRIPT ${EXAMPLE_OUTPUT_DIR}/run_example.bat)
file(WRITE ${RUN_SCRIPT} 
"@echo off\n"
"echo Running Tolk Java Example...\n"
"java -Djava.library.path=../../lib -jar ExampleAppJava.jar\n"
"pause\n"
)

install(FILES ${RUN_SCRIPT} DESTINATION examples/java)

message(STATUS "Java example application will be built")
