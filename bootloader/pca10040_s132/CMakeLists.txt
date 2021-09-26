CMAKE_MINIMUM_REQUIRED(VERSION 3.17)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)
#set(CMAKE_OSX_SYSROOT ${CMAKE_CURRENT_SOURCE_DIR})

project(bootloader C CXX ASM)

set(SDK_ROOT ../../sdk)
set(PROJ_DIR ../)
set(EXECUTABLE ${PROJECT_NAME}.out)

# linker scripts abs path
set(LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/secure_bootloader_gcc_nrf52.ld)
set(LINKER_INCLUDE ${SDK_ROOT}/modules/nrfx/mdk)
get_filename_component(LINKER_INCLUDE_ABS "${LINKER_INCLUDE}" REALPATH BASE_DIR)

SET(OPT -Os -g3)

list(APPEND CFLAGS ${OPT})
list(APPEND CFLAGS -D__HEAP_SIZE=0)
list(APPEND CFLAGS -DBLE_STACK_SUPPORT_REQD)
list(APPEND CFLAGS -DBOARD_PCA10040)
list(APPEND CFLAGS -DCONFIG_GPIO_AS_PINRESET)
list(APPEND CFLAGS -DFLOAT_ABI_HARD)
list(APPEND CFLAGS -DNRF52)
list(APPEND CFLAGS -DNRF52832_XXAA)
list(APPEND CFLAGS -DNRF52_PAN_74)
list(APPEND CFLAGS -DNRF_DFU_SETTINGS_VERSION=2)
list(APPEND CFLAGS -DNRF_DFU_SVCI_ENABLED)
list(APPEND CFLAGS -DNRF_SD_BLE_API_VERSION=7)
list(APPEND CFLAGS -DS132)
list(APPEND CFLAGS -DSOFTDEVICE_PRESENT)
list(APPEND CFLAGS -DSVC_INTERFACE_CALL_AS_NORMAL_FUNCTION)
list(APPEND CFLAGS -DuECC_ENABLE_VLI_API=0)
list(APPEND CFLAGS -DuECC_OPTIMIZATION_LEVEL=3)
list(APPEND CFLAGS -DuECC_SQUARE_FUNC=0)
list(APPEND CFLAGS -DuECC_SUPPORT_COMPRESSED_POINT=0)
list(APPEND CFLAGS -DuECC_VLI_NATIVE_LITTLE_ENDIAN=1)
list(APPEND CFLAGS -mcpu=cortex-m4)
list(APPEND CFLAGS -mthumb -mabi=aapcs)
list(APPEND CFLAGS -Wall -Werror)
list(APPEND CFLAGS -mfloat-abi=hard -mfpu=fpv4-sp-d16)
# keep every function in a separate section, this allows linker to discard unused ones
list(APPEND CFLAGS -ffunction-sections -fdata-sections -fno-strict-aliasing)
list(APPEND CFLAGS -fno-builtin -fshort-enums)
list(JOIN CFLAGS " " CFLAGS)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  ${CFLAGS}")

list(APPEND ASMFLAGS -g3)
list(APPEND ASMFLAGS -D__HEAP_SIZE=0)
list(APPEND ASMFLAGS -mcpu=cortex-m4)
list(APPEND ASMFLAGS -mthumb -mabi=aapcs)
list(APPEND ASMFLAGS -mfloat-abi=hard -mfpu=fpv4-sp-d16)
list(APPEND ASMFLAGS -DBLE_STACK_SUPPORT_REQD)
list(APPEND ASMFLAGS -DBOARD_PCA10040)
list(APPEND ASMFLAGS -DCONFIG_GPIO_AS_PINRESET)
list(APPEND ASMFLAGS -DFLOAT_ABI_HARD)
list(APPEND ASMFLAGS -DNRF52)
list(APPEND ASMFLAGS -DNRF52832_XXAA)
list(APPEND ASMFLAGS -DNRF52_PAN_74)
list(APPEND ASMFLAGS -DNRF_DFU_SETTINGS_VERSION=2)
list(APPEND ASMFLAGS -DNRF_DFU_SVCI_ENABLED)
list(APPEND ASMFLAGS -DNRF_SD_BLE_API_VERSION=7)
list(APPEND ASMFLAGS -DS132)
list(APPEND ASMFLAGS -DSOFTDEVICE_PRESENT)
list(APPEND ASMFLAGS -DSVC_INTERFACE_CALL_AS_NORMAL_FUNCTION)
list(APPEND ASMFLAGS -DuECC_ENABLE_VLI_API=0)
list(APPEND ASMFLAGS -DuECC_OPTIMIZATION_LEVEL=3)
list(APPEND ASMFLAGS -DuECC_SQUARE_FUNC=0)
list(APPEND ASMFLAGS -DuECC_SUPPORT_COMPRESSED_POINT=0)
list(APPEND ASMFLAGS -DuECC_VLI_NATIVE_LITTLE_ENDIAN=1)
list(JOIN ASMFLAGS " " ASMFLAGS)

set(CMAKE_ASM_FLAGS "${CMAKE_ASM_FLAGS}  ${ASMFLAGS}")

list(APPEND LDFLAGS ${OPT})
list(APPEND LDFLAGS -mthumb)
list(APPEND LDFLAGS -mabi=aapcs)
list(APPEND LDFLAGS -L${LINKER_INCLUDE_ABS})
list(APPEND LDFLAGS -T${LINKER_SCRIPT})
list(APPEND LDFLAGS -mcpu=cortex-m4)
list(APPEND LDFLAGS -mfloat-abi=hard -mfpu=fpv4-sp-d16)
# let linker dump unused sections
list(APPEND LDFLAGS -Wl,--gc-sections)
# use newlib in nano version
list(APPEND LDFLAGS --specs=nano.specs)
list(APPEND LDFLAGS -Wl,--print-memory-usage)
list(JOIN LDFLAGS " " LDFLAGS)

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS}  ${LDFLAGS}")

include_directories(${SDK_ROOT}/components/libraries/crypto/backend/micro_ecc)
include_directories(${SDK_ROOT}/components/softdevice/s132/headers)
include_directories(${SDK_ROOT}/components/libraries/memobj)
include_directories(${SDK_ROOT}/components/libraries/sha256)
include_directories(${SDK_ROOT}/components/libraries/crc32)
include_directories(${SDK_ROOT}/components/libraries/experimental_section_vars)
include_directories(${SDK_ROOT}/components/libraries/mem_manager)
include_directories(${SDK_ROOT}/components/libraries/fstorage)
include_directories(${SDK_ROOT}/components/libraries/util)
include_directories(${SDK_ROOT}/modules/nrfx)
include_directories(${SDK_ROOT}/external/nrf_oberon/include)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/oberon)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/cifra)
include_directories(${SDK_ROOT}/components/libraries/atomic)
include_directories(${SDK_ROOT}/integration/nrfx)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/cc310_bl)
include_directories(${SDK_ROOT}/components/softdevice/s132/headers/nrf52)
include_directories(${SDK_ROOT}/components/libraries/log/src)
include_directories(${SDK_ROOT}/components/libraries/bootloader/dfu)
include_directories(${SDK_ROOT}/components/ble/common)
include_directories(${SDK_ROOT}/components/libraries/delay)
include_directories(${SDK_ROOT}/components/libraries/svc)
include_directories(${SDK_ROOT}/components/libraries/stack_info)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/nrf_hw)
include_directories(${SDK_ROOT}/components/libraries/log)
include_directories(${SDK_ROOT}/external/nrf_oberon)
include_directories(${SDK_ROOT}/components/libraries/strerror)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/mbedtls)
include_directories(${SDK_ROOT}/components/boards)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/cc310)
include_directories(${SDK_ROOT}/components/libraries/bootloader)
include_directories(${SDK_ROOT}/external/fprintf)
include_directories(${SDK_ROOT}/components/libraries/crypto)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/config)
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/optiga)
include_directories(${SDK_ROOT}/components/libraries/scheduler)
include_directories(${SDK_ROOT}/modules/nrfx/hal)
include_directories(${SDK_ROOT}/components/toolchain/cmsis/include)
include_directories(${SDK_ROOT}/components/libraries/balloc)
include_directories(${SDK_ROOT}/components/libraries/atomic_fifo)
include_directories(${SDK_ROOT}/external/micro-ecc/micro-ecc)
include_directories(${PROJ_DIR})
include_directories(${SDK_ROOT}/components/libraries/crypto/backend/nrf_sw)
include_directories(${SDK_ROOT}/modules/nrfx/mdk)
include_directories(${SDK_ROOT}/components/libraries/bootloader/ble_dfu)
include_directories(${SDK_ROOT}/components/softdevice/common)
include_directories(${SDK_ROOT}/external/nano-pb)
include_directories(${SDK_ROOT}/components/libraries/queue)
include_directories(${SDK_ROOT}/components/libraries/ringbuf)

add_executable(${EXECUTABLE}
        ${SDK_ROOT}/modules/nrfx/mdk/gcc_startup_nrf52.S
        ${SDK_ROOT}/modules/nrfx/mdk/system_nrf52.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_frontend.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_str_formatter.c
        ${SDK_ROOT}/components/libraries/util/app_error_weak.c
        ${SDK_ROOT}/components/libraries/scheduler/app_scheduler.c
        ${SDK_ROOT}/components/libraries/util/app_util_platform.c
        ${SDK_ROOT}/components/libraries/crc32/crc32.c
        ${SDK_ROOT}/components/libraries/mem_manager/mem_manager.c
        ${SDK_ROOT}/components/libraries/util/nrf_assert.c
        ${SDK_ROOT}/components/libraries/atomic_fifo/nrf_atfifo.c
        ${SDK_ROOT}/components/libraries/atomic/nrf_atomic.c
        ${SDK_ROOT}/components/libraries/balloc/nrf_balloc.c
        ${SDK_ROOT}/external/fprintf/nrf_fprintf.c
        ${SDK_ROOT}/external/fprintf/nrf_fprintf_format.c
        ${SDK_ROOT}/components/libraries/fstorage/nrf_fstorage.c
        ${SDK_ROOT}/components/libraries/fstorage/nrf_fstorage_nvmc.c
        ${SDK_ROOT}/components/libraries/fstorage/nrf_fstorage_sd.c
        ${SDK_ROOT}/components/libraries/memobj/nrf_memobj.c
        ${SDK_ROOT}/components/libraries/queue/nrf_queue.c
        ${SDK_ROOT}/components/libraries/ringbuf/nrf_ringbuf.c
        ${SDK_ROOT}/components/libraries/experimental_section_vars/nrf_section_iter.c
        ${SDK_ROOT}/components/libraries/strerror/nrf_strerror.c
        ${SDK_ROOT}/components/libraries/sha256/sha256.c
        ${SDK_ROOT}/components/libraries/crypto/backend/micro_ecc/micro_ecc_backend_ecc.c
        ${SDK_ROOT}/components/libraries/crypto/backend/micro_ecc/micro_ecc_backend_ecdh.c
        ${SDK_ROOT}/components/libraries/crypto/backend/micro_ecc/micro_ecc_backend_ecdsa.c
        ${SDK_ROOT}/components/boards/boards.c
        ${SDK_ROOT}/modules/nrfx/hal/nrf_nvmc.c
        ${SDK_ROOT}/modules/nrfx/soc/nrfx_atomic.c
        ${SDK_ROOT}/components/libraries/crypto/nrf_crypto_ecc.c
        ${SDK_ROOT}/components/libraries/crypto/nrf_crypto_ecdsa.c
        ${SDK_ROOT}/components/libraries/crypto/nrf_crypto_hash.c
        ${SDK_ROOT}/components/libraries/crypto/nrf_crypto_init.c
        ${SDK_ROOT}/components/libraries/crypto/nrf_crypto_shared.c
        ${PROJ_DIR}/dfu_public_key.c
        ${PROJ_DIR}/main.c
        ${SDK_ROOT}/components/ble/common/ble_srv_common.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_app_start.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_app_start_final.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_dfu_timers.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_fw_activation.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_info.c
        ${SDK_ROOT}/components/libraries/bootloader/nrf_bootloader_wdt.c
        ${SDK_ROOT}/external/nano-pb/pb_common.c
        ${SDK_ROOT}/external/nano-pb/pb_decode.c
        ${SDK_ROOT}/components/libraries/crypto/backend/nrf_sw/nrf_sw_backend_hash.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/dfu-cc.pb.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu.c
        ${SDK_ROOT}/components/libraries/bootloader/ble_dfu/nrf_dfu_ble.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_flash.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_handling_error.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_mbr.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_req_handler.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_settings.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_settings_svci.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_transport.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_utils.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_validation.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_ver_validation.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_svci.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_svci_handler.c
        ${SDK_ROOT}/components/libraries/svc/nrf_svc_handler.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh_ble.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh_soc.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_chacha_poly_aead.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_ecc.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_ecdh.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_ecdsa.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_eddsa.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_hash.c
        ${SDK_ROOT}/components/libraries/crypto/backend/oberon/oberon_backend_hmac.c
        )

get_filename_component(MICROECC ${SDK_ROOT}/external/micro-ecc/nrf52hf_armgcc/armgcc/micro_ecc_lib_nrf52.a REALPATH BASE_DIR)
target_link_libraries(${EXECUTABLE} PUBLIC ${MICROECC})

add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-size ${EXECUTABLE})

add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-objcopy -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
        COMMAND arm-none-eabi-objcopy -O binary ${EXECUTABLE} ${PROJECT_NAME}.bin)