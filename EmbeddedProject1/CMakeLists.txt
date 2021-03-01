cmake_minimum_required(VERSION 3.15)
 

 ###################################################
#setting needed variables for this visualgsb project
###################################################
if(NOT DEFINED SET_ALL_NEEDED_VARS_MANUALLY) 
option(SET_ALL_NEEDED_VARS_MANUALLY   "set all needed variables from script" ON)# ON default value
endif()

 if(NOT SET_ALL_NEEDED_VARS_MANUALLY)
message("setting needed vars")
set( CMAKE_BUILD_TYPE DEBUG )
set(  TOOLCHAIN_ROOT "C:/SysGCC/arm-eabi"  )
set(  CMAKE_MAKE_PROGRAM "C:/Program Files (x86)/Sysprogs/VisualGDB/ninja.exe"  ) 
set(  CMAKE_TOOLCHAIN_FILE "${CMAKE_CURRENT_SOURCE_DIR}/build/VisualGDB/Debug/toolchain.cmake" )
set(  PLATFORM VisualGDB  )
set(  LOCALAPPDATA "C:/Users/Hadi/AppData/Local")

endif()
message("SET_ALL_NEEDED_VARS_MANUALLY-------------${SET_ALL_NEEDED_VARS_MANUALLY}")
#unset(SET_ALL_NEEDED_VARS_MANUALLY CACHE) # <---- this is the important!!
 ###################################################



project(EmbeddedProject1 LANGUAGES C CXX ASM)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

###################################################
#getting bsp libraries needed to link in add_bsp_based_executable
###################################################

find_bsp(ID com.sysprogs.arm.stm32
	VERSION 2021.02
	MCU STM32F401RE
	FRAMEWORKS com.sysprogs.arm.stm32.hal com.sysprogs.arm.stm32.ll
	HWREGISTER_LIST_FILE STM32F4xxxx/DeviceDefinitions/stm32f401xe.xml)


###################################################
#Fetching content example
###################################################
include(FetchContent) 
FetchContent_Declare(child
  GIT_REPOSITORY https://github.com/haditj66/Lib_A.git
  GIT_TAG 837e6fbd9f4401076453815ea8ab2b6482712f56   # it's much better to use a specific Git revision or Git tag for reproducibility
) 
FetchContent_MakeAvailable(child)
###################################################





###################################################
#adding library and executable targets
###################################################
add_bsp_based_library(NAME LibraryTest  
	SOURCES  system_stm32f4xx.c stm32f4xx_hal_conf.h MyAdder.h MyAdder.cpp)
#this is needed for if other projects try to link against this LibraryTest one via fetchcontent.
target_include_directories(LibraryTest PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
	
add_bsp_based_executable(NAME EmbeddedProject1
	SOURCES EmbeddedProject1.cpp system_stm32f4xx.c stm32f4xx_hal_conf.h
	GENERATE_BIN
	GENERATE_MAP)

	#add_executable(EmbeddedProject2 mainTest.cpp)

	#add_executable(EmbeddedProject2
	 #EmbeddedProject1.cpp system_stm32f4xx.c stm32f4xx_hal_conf.h
	 #mainTest.cpp)
	 
	 #target_link_libraries(EmbeddedProject2 PRIVATE BSP
	 #PRIVATE BSP_com.sysprogs.arm.stm32.hal
	 #PRIVATE BSP_com.sysprogs.arm.stm32.ll)

#target_link_libraries(EmbeddedProject1 PRIVATE LibA)  # mylib is from "child"

target_link_libraries(EmbeddedProject1 PRIVATE LibraryTest)  
###################################################


set(blabla ${CMAKE_CURRENT_SOURCE_DIR})
message("blabla*********************${CMAKE_CURRENT_SOURCE_DIR}")




	 		message("_exe_SOURCES*********************${_exe_SOURCES}")
		message("_extra_sources*********************${_extra_sources}")
		message("_exe_BSP_ALIAS_LIBRARIES*********************${${_exe_BSP_ALIAS}_LIBRARIES}")	

install(TARGETS LibraryTest 
	 LIBRARY
	 DESTINATION "C:/Users/Hadi/source/repos/EmbeddedProject1/EmbeddedProject1/build/install")