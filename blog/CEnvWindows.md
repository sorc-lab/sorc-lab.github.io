[![Sorc Lab](/SorcLabLogo_White.png)](https://sorclab.com/)

# C Programming Environment for Windows

## Install MSYS2 MINGW64
Start by installing MSYS2 command line environment for Windows: https://www.msys2.org/
Install via running the executable installer: https://github.com/msys2/msys2-installer/releases/download/2025-08-30/msys2-x86_64-20250830.exe

## Install GCC Compiler
MSYS2 comes with the `pacman` package manager. Use pacman to install GCC compiler:
```
pacman -Syu
pacman -S mingw-w64-x86_64-gcc
```
NOTE: Make sure you run the MINGW64 binary to open the correct shell.

Test installation:
```
gcc --version
```

## Install VS Code
This is the simplest text editor that still has some decent plugins and features without being a full blown IDE.

### Setup GCC for VS Code
Install extension: ms-vscode.cpptools
Configure VS Code to use GCC via:
  - Ctrl+Shift+P → C/C++: Edit Configurations (UI)
  - Choose compiler path: C:\msys64\mingw64\bin\gcc.exe

### Configure Build Task
- Open Command Pallete: Ctrl+Shift+P
- Configure Default Build Task
- Choose: C/C++: gcc build active file

NOTE: This should auto-generate a task.json file with pre-defined settings, which should be all you need for a basic setup.

Now you can run your C code via Ctrl+Shift+B. You can tweak any extra settings in the `task.json` file.
By default, this will compile your code into a .exe binary named after whatever your source code `.c` file is named.

## Setup GDB Debugger
Install GDB:
```
pacman -S mingw-w64-x86_64-gdb
```

If you stuck with the default task.json settings, you should be compiling with the GCC `-g` flag.
Open the Command Pallete in VS Code again via: Ctrl+Shift+P

Type:
```
Add Debug Configuration
```

Choose:
```
C/C++: (gdb) Launch
```

Copy/paste the following `launch.json` and save the file:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug C Program",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "C:\\msys64\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

You should now be able to set break points and press `F5` to run the debugger and step over and into via `F10` and `F11` etc.
