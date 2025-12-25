cmake_minimum_required(VERSION 3.12)

# Pull in SDK (must be before project)
include(pico_sdk_import.cmake)
include(pico_extras_import_optional.cmake)

set(PROJECT felica_reader_pico)
project(${PROJECT} C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# If you want debug output from USB (pass -DPICO_STDIO_USB=1) this ensures you don't lose any debug output while USB is set up
if (NOT DEFINED PICO_STDIO_USB_CONNECT_WAIT_TIMEOUT_MS)
    set(PICO_STDIO_USB_CONNECT_WAIT_TIMEOUT_MS 3000)
endif()

# Initialize the SDK
pico_sdk_init()

add_compile_options(-Wall
        -Wno-format          # int != int32_t as far as the compiler is concerned because gcc has int32_t as long int
        -Wno-unused-function # we have some for the docs that aren't called
        )
if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
    add_compile_options(-Wno-maybe-uninitialized)
endif()

# add_subdirectory(SB1602E)

add_executable(${PROJECT}
    main.cpp
    driver/SB1602E.cpp
    driver/RCS620S.cpp
    driver/AS289R2.cpp
)

target_link_libraries(${PROJECT} pico_stdlib hardware_i2c)

# Set port numbers on the board
if (PICO_BOARD STREQUAL "PIMORONI_TINY2040")
    target_compile_definitions(${PROJECT} PRIVATE
        SB1602E_I2C_SDA=26
        SB1602E_I2C_SCL=27
        RCS620S_UART_TX=0
        RCS620S_UART_RX=1
        AS289R2_UART_TX=4
        AS289R2_UART_RX=5
    )
elseif (PICO_BOARD STREQUAL "PICO" OR PICO_BOARD STREQUAL "PICO_W" OR PICO_BOARD STREQUAL "PICO2")
    target_compile_definitions(${PROJECT} PRIVATE
        SB1602E_I2C_SDA=14
        SB1602E_I2C_SCL=15
        RCS620S_UART_TX=16
        RCS620S_UART_RX=17
        AS289R2_UART_TX=4
        AS289R2_UART_RX=5
    )
else()
    message(FATAL_ERROR "Unknown PICO_BOARD '$ENV{PICO_BOARD}'. Please set LCD_I2C_SDA and LCD_I2C_SCL manually.")
endif()

# enable usb output, disable uart output
pico_enable_stdio_usb(${PROJECT} 1)
pico_enable_stdio_uart(${PROJECT} 0)

# create map/bin/hex/uf2 file etc.
pico_add_extra_outputs(${PROJECT})

