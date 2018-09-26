---
layout: post
title: DirectX 12 Tutorial - Hello Triangle
date: 2018-09-26
categories: tutorial
tags: Graphics D3D12
---

This tutorial will teach you how to draw a triangle with DirectX 12 using Visual Studio 2017.

## Requirements
DirectX 12 is Windows 10 exclusive. So the first thing you will need is a windows 10 machine.

### Downloads
You will need an IDE to develop the DirectX 12 app. I use Visual Studio 2015. You can download the community version for free:

https://visualstudio.microsoft.com/vs/

Make sure to select Custom Installation when installing, and select the C++ options, because they are deselected by default. Otherwise you'll only be able to make C# projects with VS2015.
Next you will need the Windows 10 SDK, which you can download from here:

https://dev.windows.com/en-us/downloads/windows-10-sdk

## Setting up DirectX 12 for Visual Studio 2017
### Create an empty Visual C++ project 

Open Vidusl Studio 2017, go to `File` -> `new` -> `Project...`.

In the New Project window, select `Visual C++` -> `Windows Desktop` -> `Windows Desktop Wizard`, give a project name and click "OK".

![Create VC++ Project](\assets\img\d3d12\create-empty-project.png)

It will open up a Windows Desktop Project Wizard. Select "Windows Application (.ext)" as Application type, and check "Empty project" and deselect "Security Development Lifecycle (SDL) checks" in Additional Options, then click "OK".

![Window Desktop Project Wizard](\assets\img\d3d12\window-desktop-project-wizard.png)

The empty project layout show as:
![Project Layout](\assets\img\d3d12\project-layout.png)

### Include Direct 12 Libraries
Now you can either develop a x86 application or a x64 application.

x86 applications have a maximum 2GB of memory (RAM) usage, but will work on both x64 and x86 platforms.

x64 applications can top out at 1TB of memory (RAM) usage, and can perform faster than x86, but will only run on x64 platforms.

Pretty much all newer systems nowdays are 64-bit, and i'm not even really sure if you can buy a x86 system from Best Buy anymore, which is why i suggest we go for the x64 setup.

In the toolbar at the top, click on the dropdown that says "x86" and select "x64"

![Debug X64](\assets\img\d3d12\debug-x64.png)

Now start to include Direct 12 libraries.

Open `Solution Explorer` by clicking `View`-> `Solution Explorer` or pressing "Ctrl+Alt+L" if it is not already open.

![Solution Explorer](\assets\img\d3d12\solution-explorer.png)

Right click the project and select `Properties`. A new window will pop up.

On the left panel, click `VC++ Directories`.

On the right panel, click `Include Directories`, and click on the down arrow on the right side of the row, and click `<Edit...>` a new window will pop up.

![Project Property Page](\assets\img\d3d12\property-page.png)

Click the folder icon at the top, then click the `...` in the line that appears.

![Include Directories](\assets\img\d3d12\include-directories.png)

A file explorer will open up. This is where we will need to include the direct3d 12 headers.

The two locations we will be including are:

```
Windows Kits->10->Include->{version}->shared
Windows Kits->10->Include->{version}->um
```

Visual Studio 2017 comes with Windows 10 SDK, if you cannot find the Windows 10 SDK under Program Files, go back and reinstall Visual Studio 2017 with Windows 10 SDK option.

After installing the latest version of Windows 10 SDK, the includes on my system were located:

```
C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\shared
C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\um
```
the shared and um folders are what you might consider traditional windows sdk header files. 

The shared folder includes "dxgi1_4.h" which we will use for some things like the swapchain. You can find an overview of DXGI here.

The um folder contains the directx 12 headers, such as "d3d12.h, "d3dcompiler.h and "d2d12sdklayers.h" (used for debugging)

Now add the two directories and click "OK"

![Add headers in Include Directories](\assets\img\d3d12\include-directories-done.png)

Click on `Library Directories` and then the down arrow on the right side of the row, then edit to open up the libraries directories.

![Library Directories](\assets\img\d3d12\library-directories.png)

Click the folder icon at the top and then the `...` that appears on the new line to open the file browser again.

Navigate to "Windows Kits->10->Lib->{version}->um->x64" then click "Select Folder"

On my system it was:

```
C:\Program Files (x86)\Windows Kits\10\Lib\10.0.10240.0\um\x64
```

Now click "OK" to close the Library directories window.

On the left panel expand `Linker` and click on `Input`.

![Additional Dependencies](\assets\img\d3d12\linker-input.png)

On the right panel click on `Additional Dependencies` and click the down arrow on the right side of the row.

We need to link the d3d and dxgi static libraries. Enter the following into the box that pops up:

```
d3d12.lib
dxgi.lib
d3dcompiler.lib
```

![Add libs in Additional Dependencies](\assets\img\d3d12\additional-dependencies.png)

Click "OK" again to close the properties window.

## Create Headers

### DirectX Math
We will be using the DirectX Math Library for all the math involved in the tutorials. DirectX Math is what Microsoft recommends us to use.

### d3dx12.h
[Helper Structures and Functions for D3D12](https://msdn.microsoft.com/en-us/library/windows/desktop/dn708058(v=vs.85).aspx)

We will be using the open source d3d12 helper functions and structures provided by Microsoft. This file does not come with the windows sdk, so you will have to download this separately.

Open the "Solution Explorer" and right click on "Header Files", then click "Add->New Item...".

In the pop up, select "Header File (.h)", name it "d3dx12.h" and click "Add".

Go [here](https://github.com/Microsoft/DirectX-Graphics-Samples/blob/master/Samples/Desktop/D3D12HelloWorld/src/HelloTriangle/d3dx12.h), copy the code and paste it into the new "d3dx12.h" file you just created and save it.

### stdafx.h
The stdafx.h file is a common filename for a "grouping" of all headers and code that doesn't change often.

The way i've decided to do these tutorials is how i've done them in the past. Basically without the use of classes and multiple files so there is less jumping around and it's more straight to the point. of course in a real application, you will have your code more organized and modular, but again for the sake of hopeful clarity, i have everything together. I will use the "stdafx.h" file for all includes, declarations of functions, and declarations and definitions of variables and structures. "Main.cpp" will have all the definitions of the functions.

Lets start with the includes we will need throughout the tutorials:
```
#pragma once

#ifndef WIN32_LEAN_AND_MEAN
#define WIN32_LEAN_AND_MEAN  // Exclude rarely-used stuff from Windows headers.
#endif

#include <windows.h>
#include <d3d12.h>
#include <dxgi1_4.h>
#include <D3Dcompiler.h>
#include <DirectXMath.h>
#include "d3dx12.h"
```

## Create Main.cpp file
Open the "Solution Explorer"

Right click on the "Source Files" folder. Select "Add->New Item...". A window will pop up.

On the left side of the window is the different project types. Click "Visual C++".

In the next section is the different file types. Select "C++ File (.cpp)", and name the file "main.cpp", then click "Add"

Now we need to include "stdafx.h" and create the winmain function
```
#include "stdafx.h"

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE, LPSTR, int nCmdShow)
{
    return 0;
}
```
Your project is ready for development!

## To be continued


## More Optional Configures

### Output Directory
In default, the output directory of build is under solution directory, we can change them to project directory.

For Output Directory, change "\$(SolutionDir)\$(Platform)\\$(Configuration)\\" to "__bin\\__$(Platform)\\$(Configuration)\\"

For Intermediate Directory, change "\$(Platform)\\$(Configuration)\\" to "__obj\\__$(Platform)\\$(Configuration)\\"

![Output Directory](\assets\img\d3d12\output-directory.png)

### Copy hlsl file to Output directory

![HLSL Property](\assets\img\d3d12\shaders-hlsl-property.png)

![Custom Build Tool](\assets\img\d3d12\shaders-hlsl-custom-build-tool.png)

### Known build issues
- error C7510: 'Callback': use of dependent template name must be prefixed with 'template'
Fix: `Properties` -> `C/C++` -> `Language`, change "Conformance mode" to "No"

![Conformance mode](\assets\img\d3d12\conformance-mode.png)
