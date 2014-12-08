# mbed Microcontroller Library
# Copyright (c) 2014 ARM Limited
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# this custom build file pulls in different startup assembly based on the
# toolchain being used


# !!! FIXME could also use if(TARGET_LIKE_GCC) / TARGET_LIKE_ARMCC / etc set by
# the yotta targets, maybe that'd be clearer
if(${CMAKE_C_COMPILER_ID} STREQUAL GNU)
    set(STARTUP_ASM "bootstrap_gcc/startup_STM32F40x.s")
    set(STARTUP_SRC "")
elseif(${CMAKE_C_COMPILER_ID} STREQUAL ARMCC)
    set(STARTUP_ASM "bootstrap_armcc/startup_stm32F401xe.s")
    set(STARTUP_SRC "bootstrap_armcc/sys.cpp")
else()
    set(STARTUP_ASM "")
    set(STARTUP_SRC "")
    message(ERROR "Unsupported target toolchain (gcc is supported)")
endif()

enable_language(ASM)
set_property(SOURCE ${STARTUP_ASM} PROPERTY LANGUAGE ASM)

add_library(cmsis-core-nucleo-f401re
    ${STARTUP_ASM}
    "cmsis_nvic.c"
    "hal_tick.c"
    "system_stm32f4xx.c"
)
set_target_properties(cmsis-core-nucleo-f401re PROPERTIES
    LINKER_LANGUAGE C
)

target_link_libraries(cmsis-core-nucleo-f401re
    cmsis-core
)
