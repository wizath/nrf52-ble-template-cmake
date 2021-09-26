CMAKE_MINIMUM_REQUIRED(VERSION 3.17)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)
# macos specific setting
set(HAVE_FLAG_SEARCH_PATHS_FIRST 0)
set(SDK_ROOT sdk)

project(template C ASM)

# find required programs
find_program(NRFUTIL "nrfutil")
if (NOT NRFUTIL)
    message(FATAL_ERROR "nrfutil is required for key generation")
endif ()

if (NOT EXISTS ${CMAKE_SOURCE_DIR}/private.key)
    MESSAGE(status 'I DONT EXIST')
    # generate private key
    execute_process(
            COMMAND nrfutil keys generate ${CMAKE_SOURCE_DIR}/private.key
    )
    # generate c file
    execute_process(
            COMMAND nrfutil keys display --key pk --format code ${CMAKE_SOURCE_DIR}/private.key --out_file ${CMAKE_SOURCE_DIR}/bootloader/dfu_public_key.c
    )
endif ()

ADD_SUBDIRECTORY(bootloader/pca10040_s132)
ADD_SUBDIRECTORY(application/pca10040_s132)

add_custom_target(dfu)
add_dependencies(dfu application.out)
add_custom_command(TARGET dfu
        POST_BUILD
        COMMAND nrfutil pkg generate --hw-version 52 --application-version 0 --application ${CMAKE_BINARY_DIR}/application/pca10040_s132/application.hex --sd-req 0x0101 --key-file ${CMAKE_SOURCE_DIR}/private.key ${CMAKE_BINARY_DIR}/app_dfu_package.zip
        )

add_custom_target(full_hex)
add_dependencies(full_hex application.out)
add_dependencies(full_hex bootloader.out)

get_filename_component(SOFTDEVICE_PATH ${SDK_ROOT}/components/softdevice/s132/hex/s132_nrf52_7.2.0_softdevice.hex REALPATH BASE_DIR)
add_custom_command(TARGET full_hex
        POST_BUILD
        COMMAND nrfutil settings generate --family NRF52 --application ${CMAKE_BINARY_DIR}/application/pca10040_s132/application.hex --application-version 0 --bootloader-version 0 --bl-settings-version 2 ${CMAKE_BINARY_DIR}/bootloader_setting.hex
        COMMAND mergehex --merge ${CMAKE_BINARY_DIR}/bootloader/pca10040_s132/bootloader.hex ${CMAKE_BINARY_DIR}/bootloader_setting.hex ${SOFTDEVICE_PATH} ${CMAKE_BINARY_DIR}/application/pca10040_s132/application.hex --output ${CMAKE_BINARY_DIR}/app_flash_full.hex
        )

add_custom_target(full_flash)
add_dependencies(full_flash full_hex)
add_custom_command(TARGET full_flash
        POST_BUILD
        COMMAND nrfjprog -f nrf52 --program ${CMAKE_BINARY_DIR}/app_flash_full.hex --sectoranduicrerase
        COMMAND nrfjprog -f nrf52 --reset
        )

# usefull commands
# erase_uicr:
#	nrfjprog -f nrf52 --eraseuicr
# erase:
#	nrfjprog -f nrf52 --eraseall
# recover:
#	nrfjprog -f nrf52 --recover
