# nRF-Connect-SDK-Development-Environmet-setup
This is a quick guide on how to setup your development environment(DE) in VS Code for the Nordic Semi nRF SoCs using the nRF Connect SDK  
## Why use this guide  
When I first started developing applications for the nRF SoCs, I followed the official guide on setting up the DE in VS code and my code would compile but it still showed squiggles under certain header files and other errors that were annoying me and I couldn't explain. So after a lot of research, I was able to understand how everything worked and I got rid of the errors. This guide will help you understand the DE while working with the nRF Connect SDK and will show you how to set it up in different ways
## 1- Using the nRF Connect extension  
In VS Code go to the extensions page and download the nRF Connect Extension Pack  
![vscode_nrf_ext_pack](https://user-images.githubusercontent.com/36559536/157930446-0f7c63d0-e4f5-4402-a3b2-3c7c013fdec5.JPG)  
From here go to the nRF Connect page, click on "Create a new application from sample", select "Freestanding", fill out the rest and click on "Create Application".  
Now Create build configurations by clicking the following.  
![Config_tab](https://user-images.githubusercontent.com/36559536/157931884-f44ea814-81d2-4b24-8e3f-5c9c1b1b7e05.png)  

Select you Board and Click "Build Configuration". Now if you go the main.c file you will see squiggles under the include statments.  

![squigles](https://user-images.githubusercontent.com/36559536/157932437-dadc856d-62ca-4c39-baa9-6fb8de95ce0e.JPG)  

To get rid of them make a new folder under the root directory (hello_world) and call it .vscode and add a new file called settings.json.  
In this file add the following
```
{
    "C_Cpp.default.configurationProvider": "nrf-connect"
}
```  
Save, and now the squiggles shoud be gone. If they are not, Run "Pristine build" from the nRF Connect Extension.
If "Pristine build" or "Build" are not working for some reason (which happened to me), open the command prompt and run the following commands.  
> -Note: command formate cmake -B build -GNinja -DBOARD=`<board model>` `<location of CMakeLists.txt>`  
> -Note: location of CMakeLists.txt is not needed if building within the directory.  
```
cmake -B build -GNinja -DBOARD=nrf52833dk_nrf52833 samples/hello_world
```
```
ninja -C build
```
To flash use the "Flash" button in the extension
### Explanation:
  Why does this get rid of the squiggles?  The build process happenes in two stages.  
  1: generating build files using CMake (which generates printk.h).  
  2: running the native buid tool (which generats syscall_list.h needed by the zephyr.h header file)

## 2- Using the Command Prompt(Windows), CMake, Ninja and nRF Command Line Tools
Use this option if you like to work with the Command Prompt!  
> Note: You must have CMake, Ninja and the nRF Command Line Tools downloaded.

First set up IntelliSense in VS Code, to detect code and header files in the following way.  
1- Add .vscode folder under the main directory.  
2- In the folder add a file called c_cpp_properties.json.  
3- In this file add the following
```
{
    "configurations": [
        {
            "name": "Win64",
            "compileCommands": "${workspaceFolder}/build/compile_commands.json",
            "compilerPath": "C:/NordicSemi/v1.9.1/toolchain/opt/bin/arm-none-eabi-gcc.exe",
            "intelliSenseMode": "gcc-arm",
            "cStandard": "c89",
            "cppStandard": "gnu++98"
        }
    ],
    "version": 4
}
```

In the Command Prompt navigate to the root directory of the project.  
Run the following commands.  
```
cmake -B build -GNinja -DBOARD=nrf52833dk_nrf52833 samples/hello_world
```
```
ninja -C build
```
Now the squiggles should be gone.  
To flash, use the nRF Command Line Tools or JLink. In the following command I'm using the nRF tool.  
```
nrfjprog --program build/zephyr/zephyr.hex -f nrf52 --verify
```
Now reset the board using the reset button or the following command
```
nrfjprog -f nrf52 --reset
```
## 3- Using the Command Prompt(Windows) and West Command Line Tool
Use this option also if you like to work with the Command Prompt! 
> Note: You must have the West Command Line tool and its dependencies downloaded.

First set up IntelliSense in VS Code, to detect code and header files in the following way.  
1- Add .vscode folder under the main directory.  
2- In the folder add a file called c_cpp_properties.json.  
3- In this file add the following
```
{
    "configurations": [
        {
            "name": "Win64",
            "compileCommands": "${workspaceFolder}/build/compile_commands.json",
            "compilerPath": "C:/NordicSemi/v1.9.1/toolchain/opt/bin/arm-none-eabi-gcc.exe",
            "intelliSenseMode": "gcc-arm",
            "cStandard": "c89",
            "cppStandard": "gnu++98"
        }
    ],
    "version": 4
}
```

In the Command Prompt navigate to the root directory of the project.  
Run the following commands.  
> Note: you have to set up a West workspace. For that check West's documentation before running the command below  
> Note: command format west build -p auto -b `<your-board-name>` `<location of CMakeLists.txt>`
```
west build -p auto -b nrf52833dk_nrf52833 
```
The final step is to flash the nRF SoC with the following command
```
west flash
```
> Note: west flash invokes CMake and then Ninja
