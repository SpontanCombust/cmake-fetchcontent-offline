# cmake-fetchcontent-offline
CMake module that lets you automatically disable FetchContent updates if you're currently not connected to the internet.


## Why?
Every time a cmake project that is using the FetchContent module is configured it fails critically if you for some reason don't have access to the internet at the moment. That is unless you configure FETCHCONTENT_FULLY_DISCONNECTED and/or FETCHCONTENT_UPDATES_DISCONNECTED variables. Using them isn't the best however as if you wanted to actually update your dependencies you'd have to temporarily set these variables back to false.
This module ensures that when you're online dependency checking and populating process happens as usual and when you're offline dependency downloads and updates are disabled and don't cause the configuration to fail by themselves.


## How-To

### Method A.
1. Download the module file paste it in your project tree, for example into `project_root/cmake/modules/`
2. Make the module discoverable by your CMakeLists.txt
```cmake
#add the module to the module path
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules") 
# include the module
include(fetch-content-offline) 
```
3. Call FetchContent_DisconnectedIfOffline()
```cmake
# make sure to have the FetchContent module included before that
include(FetchContent)
# invoke the function supplied by the module, place it before any calls to FetchContent_MakeAvailable and such
FetchContent_DisconnectedIfOffline() 
```

### Method B.
Use FetchContent itself to download the module once and then use it.
```cmake
include(FetchContent)

# declare the dependency
FetchContent_Declare(
    FetchContentOffline
    GIT_REPOSITORY https://github.com/SpontanCombust/cmake-fetchcontent-offline
)
# make sure it will be downloaded once and never updated
set(FETCHCONTENT_UPDATES_DISCONNECTED_FETCHCONTENTOFFLINE ON)
# fetch the dependency
FetchContent_MakeAvailable(FetchContentOffline)

# make the module discoverble, use dependency's cached path
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${fetchcontentoffline_SOURCE_DIR}")
include(fetch-content-offline)

FetchContent_DisconnectedIfOffline()
```


## Module contents
Functions:
- FetchContent_DisconnectedIfOffline()
  
Cache variables:
- FETCHCONTENT_OFFLINE_MODE


After calling FetchContent_DisconnectedIfOffline() a FETCHCONTENT_OFFLINE_MODE variable is set. You can explicitly check its value to perform some arbitrary operation.
```cmake
FetchContent_DisconnectedIfOffline()

if(FETCHCONTENT_OFFLINE_MODE)
    message("Hello offline mode!")
else()
    message("Hello online mode!")
endif()
```
