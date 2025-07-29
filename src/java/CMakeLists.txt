# Tolk Java JAR build

# Find Java components
find_package(Java COMPONENTS Development Runtime REQUIRED)

# Set Java source directory and output directory
set(JAVA_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(JAVA_OUTPUT_DIR ${CMAKE_BINARY_DIR}/java)
set(JAVA_CLASSES_DIR ${JAVA_OUTPUT_DIR}/classes)

# Create output directories
file(MAKE_DIRECTORY ${JAVA_OUTPUT_DIR})
file(MAKE_DIRECTORY ${JAVA_CLASSES_DIR})

# Java source files
set(JAVA_SOURCES
    ${JAVA_SRC_DIR}/com/davykager/tolk/Tolk.java
)

# Compile Java sources to class files
add_custom_command(
    OUTPUT ${JAVA_CLASSES_DIR}/com/davykager/tolk/Tolk.class
    COMMAND ${Java_JAVAC_EXECUTABLE} 
            -g:none 
            -d ${JAVA_CLASSES_DIR}
            -sourcepath ${JAVA_SRC_DIR}
            ${JAVA_SOURCES}
    DEPENDS ${JAVA_SOURCES}
    COMMENT "Compiling Java sources"
    VERBATIM
)

# Create JAR file
set(JAR_FILE ${JAVA_OUTPUT_DIR}/Tolk.jar)

add_custom_command(
    OUTPUT ${JAR_FILE}
    COMMAND ${Java_JAR_EXECUTABLE} 
            cf ${JAR_FILE}
            -C ${JAVA_CLASSES_DIR} .
    DEPENDS ${JAVA_CLASSES_DIR}/com/davykager/tolk/Tolk.class
    COMMENT "Creating Tolk.jar"
    VERBATIM
)

# Create custom target
add_custom_target(TolkJava ALL
    DEPENDS ${JAR_FILE}
    COMMENT "Building Java JAR"
)

# Make TolkJava depend on main Tolk library (for JNI)
if(TARGET Tolk)
    add_dependencies(TolkJava Tolk)
endif()

# Install JAR file
install(FILES ${JAR_FILE} DESTINATION lib)

# Install Java source for reference
install(FILES ${JAVA_SOURCES} DESTINATION src/java/com/davykager/tolk)
