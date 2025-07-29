# Build Tolk

## Compiling

If you want to compile Tolk yourself, here's what you need to build the whole thing:

* Microsoft Visual C++
* Windows Software Development Kit (SDK)
* (Optional) Java Development Kit (JDK)
* (Optional) Microsoft .NET Framework
* (Optional) Pandoc, for doc

The root directory and `examples` directories contain various batch files as a starting point.
They assume the required tools are in your `PATH` and that the JDK include directory is in `INCLUDE`.
For the examples you will also need to copy over any dependency files.

## Build with CMake

Open the **Developer Powershell for VS:

```powershell
# x64; with examples; with dotnet, java
cmake -S . -B build -A x64 -DBUILD_EXAMPLES=ON -DTOLK_WITH_DOENET=ON -DTOLK_WITH_JAVAJNI=ON
cmake --build build --config RelWithDebInfo
cmake --install build --config RelWithDebInfo --prefix tolk-RelWithDebInfo-x64

# Win32; with examples; with dotnet, java
cmake -S . -B build32 -A Win32 -DBUILD_EXAMPLES=ON -DTOLK_WITH_DOENET=ON -DTOLK_WITH_JAVAJNI=ON
cmake --build build32 --config RelWithDebInfo
cmake --install build32 --config RelWithDebInfo --prefix tolk-RelWithDebInfo-Win32
```

See [workflows](.github/workflows) dir for more examples.

## (Old) Build with Makefile

1. Install the dependencies:

    1.1 CLI dependencies to build the C++ `.dll`

    ```bash
    choco install jom pandoc 
    ```

    > **PS:** [jom](https://community.chocolatey.org/packages/jom#install) is a CLI alternative to `nmake.exe` that is installed with Visual Studio. This is great to avoid to add manually `nmake` to the environment PATH variable

    1.2 Install Java JDK and configure `$JAVA_HOME` environment variable to point to their path

2. Install [Visual Studio](https://visualstudio.microsoft.com) and the Workload: **C++ Desktop development**

    Visual Studio is required to use the CLI tools such as: `rc`, `cl`. The IDE is required to use the **Developer Powershell for VS as well**. 

    Besides that, the Windows 10 SDK is required to use some `.dll's` that the CLI tools mentioned above requires.

    2.1 Also, make sure that you installed the Windows 10 SDK (`10.0.19041.0`)

    > **Warning:** For now, the version `10.0.2+` doesn't works to compile and generate the dll

3. Put the path below into you PATH environment variable

    This is required to use the `cl` C++ compiler and set some dependencies into the INCLUDE path: (e.g `windows.h`)

    ```bash
    C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\[latest-version]\bin\Hostx86\arm64
    ```

4. Open the **Developer Powershell for VS [version]** CLI as a administrator user

5. Go to the directory of this cloned repository, and run  [jom](https://community.chocolatey.org/packages/jom#install) (or `nmake.exe`, from Visual Studio folder) CLI to compiles the `.dll`

    ```powershell
    cd tolk
    jom /f .\Makefile
    ```

    **Output**

    ```powershell
    jom x.x.x - empower your cores

        cd tolk\src && C:\ProgramData\chocolatey\lib\jom\tools\jom.exe /
        cd tolk\src\dotnet && C:\ProgramData\chocolatey\lib\jom\tools\jom.exe /
        cd tolk\src\java && C:\ProgramData\chocolatey\lib\jom\tools\jom.exe /
        cd tolk\docs && C:\ProgramData\chocolatey\lib\jom\tools\jom.exe /
        pandoc -s --toc -M title="Tolk" -M author="Davy Kager" -r markdown -w html5 -o .\README.html README.md
    ```

6. Use the generated `src/Tolk.dll` lib together with any language wrapper that you wish (dotnet, java...)

    This command above will run the all `Makefile` on this project. You should see the generated C++ dll into `src/Tolk.dll` and the language wrappers as well:  `src/dotnet/TolkDotNet.dll`, `src/java/Tolk.jar`...
