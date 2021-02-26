cmake_minimum_required(VERSION 3.15)
 
project(EmbeddedProject1 LANGUAGES C CXX ASM)
message("CMAKE_CURRENT_SOURCE-------------${CMAKE_CURRENT_SOURCE_DIR}")
message("CMAKE_MODULE_PATH-------------${CMAKE_MODULE_PATH}")

find_bsp(ID com.sysprogs.arm.stm32
	VERSION 2021.02
	MCU STM32F401RE
	FRAMEWORKS com.sysprogs.arm.stm32.hal com.sysprogs.arm.stm32.ll
	HWREGISTER_LIST_FILE STM32F4xxxx/DeviceDefinitions/stm32f401xe.xml)


include(FetchContent) 
FetchContent_Declare(child
  GIT_REPOSITORY https://github.com/haditj66/Lib_A.git
  GIT_TAG 837e6fbd9f4401076453815ea8ab2b6482712f56   # it's much better to use a specific Git revision or Git tag for reproducibility
) 
FetchContent_MakeAvailable(child)

message("LibA-------------${LibA}")

add_bsp_based_library(NAME LibraryTest  
	SOURCES  system_stm32f4xx.c stm32f4xx_hal_conf.h MyAdder.h MyAdder.cpp)

	
add_bsp_based_executable(NAME EmbeddedProject1
	SOURCES EmbeddedProject1.cpp system_stm32f4xx.c stm32f4xx_hal_conf.h
	GENERATE_BIN
	GENERATE_MAP)
	 

	  
#target_link_libraries(EmbeddedProject1 PRIVATE LibA)  # mylib is from "child"
target_link_libraries(EmbeddedProject1 PRIVATE LibraryTest)  # mylib is from "child"

install(TARGETS LibraryTest 
	 LIBRARY
	 DESTINATION "C:/Users/Hadi/source/repos/EmbeddedProject1/EmbeddedProject1/build/install")