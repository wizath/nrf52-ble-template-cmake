CMAKE_MINIMUM_REQUIRED(VERSION 3.17)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

project(application C CXX ASM)

set(SDK_ROOT ../../sdk)
set(PROJ_DIR ../)
set(EXECUTABLE ${PROJECT_NAME}.out)

# linker scripts abs path
set(LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/ble_app_buttonless_dfu_gcc_nrf52.ld)
set(LINKER_INCLUDE ${SDK_ROOT}/modules/nrfx/mdk)
get_filename_component(LINKER_INCLUDE_ABS "${LINKER_INCLUDE}" REALPATH BASE_DIR)

SET(OPT -Os -g3)

list(APPEND CFLAGS "${OPT}")
list(APPEND CFLAGS -DAPP_TIMER_V2)
list(APPEND CFLAGS -DAPP_TIMER_V2_RTC1_ENABLED)
list(APPEND CFLAGS -DBL_SETTINGS_ACCESS_ONLY)
list(APPEND CFLAGS -DBOARD_PCA10040)
list(APPEND CFLAGS -DCONFIG_GPIO_AS_PINRESET)
list(APPEND CFLAGS -DFLOAT_ABI_HARD)
list(APPEND CFLAGS -DNRF52)
list(APPEND CFLAGS -DNRF52832_XXAA)
list(APPEND CFLAGS -DNRF52_PAN_74)
list(APPEND CFLAGS -DNRF_DFU_SVCI_ENABLED)
list(APPEND CFLAGS -DNRF_DFU_TRANSPORT_BLE=1)
list(APPEND CFLAGS -DNRF_SD_BLE_API_VERSION=7)
list(APPEND CFLAGS -DS132)
list(APPEND CFLAGS -DSOFTDEVICE_PRESENT)
list(APPEND CFLAGS -mcpu=cortex-m4)
list(APPEND CFLAGS -mthumb -mabi=aapcs)
list(APPEND CFLAGS -Wall -Werror)
list(APPEND CFLAGS -mfloat-abi=hard -mfpu=fpv4-sp-d16)
# keep every function in a separate section, this allows linker to discard unused ones
list(APPEND CFLAGS -ffunction-sections -fdata-sections -fno-strict-aliasing)
list(APPEND CFLAGS -fno-builtin -fshort-enums)
list(JOIN CFLAGS " " CFLAGS)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  ${CFLAGS}")

list(APPEND ASMFLAGS "-g3")
list(APPEND ASMFLAGS -mcpu=cortex-m4)
list(APPEND ASMFLAGS -mthumb -mabi=aapcs)
list(APPEND ASMFLAGS -mfloat-abi=hard -mfpu=fpv4-sp-d16)
list(APPEND ASMFLAGS -DAPP_TIMER_V2)
list(APPEND ASMFLAGS -DAPP_TIMER_V2_RTC1_ENABLED)
list(APPEND ASMFLAGS -DBL_SETTINGS_ACCESS_ONLY)
list(APPEND ASMFLAGS -DBOARD_PCA10040)
list(APPEND ASMFLAGS -DCONFIG_GPIO_AS_PINRESET)
list(APPEND ASMFLAGS -DFLOAT_ABI_HARD)
list(APPEND ASMFLAGS -DNRF52)
list(APPEND ASMFLAGS -DNRF52832_XXAA)
list(APPEND ASMFLAGS -DNRF52_PAN_74)
list(APPEND ASMFLAGS -DNRF_DFU_SVCI_ENABLED)
list(APPEND ASMFLAGS -DNRF_DFU_TRANSPORT_BLE=1)
list(APPEND ASMFLAGS -DNRF_SD_BLE_API_VERSION=7)
list(APPEND ASMFLAGS -DS132)
list(APPEND ASMFLAGS -DSOFTDEVICE_PRESENT)
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

include_directories(${SDK_ROOT}/components)
include_directories(${SDK_ROOT}/modules/nrfx/mdk)
include_directories(${SDK_ROOT}/components/libraries/scheduler)
include_directories(${SDK_ROOT}/modules/nrfx)
include_directories(${SDK_ROOT}/components/toolchain/cmsis/include)
include_directories(${SDK_ROOT}/components/libraries/pwr_mgmt)
include_directories(${SDK_ROOT}/components/libraries/sortlist)
include_directories(${SDK_ROOT}/components/libraries/strerror)
include_directories(${SDK_ROOT}/components/softdevice/common)
include_directories(${SDK_ROOT}/components/libraries/crc16)
include_directories(${SDK_ROOT}/components/libraries/bootloader/dfu)
include_directories(${SDK_ROOT}/components/softdevice/s132/headers/nrf52)
include_directories(${SDK_ROOT}/components/libraries/util)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/config)
include_directories(${SDK_ROOT}/components/ble/common)
include_directories(${SDK_ROOT}/components/libraries/balloc)
include_directories(${SDK_ROOT}/components/ble/peer_manager)
include_directories(${SDK_ROOT}/components/libraries/ringbuf)
include_directories(${SDK_ROOT}/modules/nrfx/hal)
include_directories(${SDK_ROOT}/components/libraries/bsp)
include_directories(${SDK_ROOT}/components/libraries/timer)
include_directories(${SDK_ROOT}/external/segger_rtt)
include_directories(${SDK_ROOT}/components/libraries/log)
include_directories(${SDK_ROOT}/components/ble/nrf_ble_gatt)
include_directories(${SDK_ROOT}/components/ble/nrf_ble_qwr)
include_directories(${SDK_ROOT}/components/libraries/button)
include_directories(${SDK_ROOT}/components/libraries/bootloader)
include_directories(${SDK_ROOT}/components/libraries/fstorage)
include_directories(${SDK_ROOT}/components/libraries/experimental_section_vars)
include_directories(${SDK_ROOT}/components/softdevice/s132/headers)
include_directories(${SDK_ROOT}/integration/nrfx/legacy)
include_directories(${SDK_ROOT}/components/libraries/mutex)
include_directories(${SDK_ROOT}/components/libraries/delay)
include_directories(${SDK_ROOT}/components/libraries/bootloader/ble_dfu)
include_directories(${SDK_ROOT}/components/libraries/atomic_fifo)
include_directories(${SDK_ROOT}/components/libraries/atomic)
include_directories(${SDK_ROOT}/components/boards)
include_directories(${SDK_ROOT}/components/libraries/memobj)
include_directories(${SDK_ROOT}/integration/nrfx)
include_directories(${SDK_ROOT}/components/libraries/fds)
include_directories(${SDK_ROOT}/components/ble/ble_advertising)
include_directories(${SDK_ROOT}/components/libraries/atomic_flags)
include_directories(${SDK_ROOT}/modules/nrfx/drivers/include)
include_directories(${SDK_ROOT}/components/ble/ble_services/ble_dfu)
include_directories(${SDK_ROOT}/external/fprintf)
include_directories(${SDK_ROOT}/components/libraries/svc)
include_directories(${SDK_ROOT}/components/libraries/log/src)
include_directories(${PROJ_DIR})

add_executable(${EXECUTABLE}
        ${SDK_ROOT}/modules/nrfx/mdk/gcc_startup_nrf52.S
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_backend_rtt.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_backend_serial.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_backend_uart.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_default_backends.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_frontend.c
        ${SDK_ROOT}/components/libraries/log/src/nrf_log_str_formatter.c
        ${SDK_ROOT}/components/boards/boards.c
        ${SDK_ROOT}/modules/nrfx/mdk/system_nrf52.c
        ${SDK_ROOT}/components/libraries/button/app_button.c
        ${SDK_ROOT}/components/libraries/util/app_error.c
        ${SDK_ROOT}/components/libraries/util/app_error_handler_gcc.c
        ${SDK_ROOT}/components/libraries/util/app_error_weak.c
        ${SDK_ROOT}/components/libraries/scheduler/app_scheduler.c
        ${SDK_ROOT}/components/libraries/timer/app_timer2.c
        ${SDK_ROOT}/components/libraries/util/app_util_platform.c
        ${SDK_ROOT}/components/libraries/crc16/crc16.c
        ${SDK_ROOT}/components/libraries/timer/drv_rtc.c
        ${SDK_ROOT}/components/libraries/fds/fds.c
        ${SDK_ROOT}/components/libraries/util/nrf_assert.c
        ${SDK_ROOT}/components/libraries/atomic_fifo/nrf_atfifo.c
        ${SDK_ROOT}/components/libraries/atomic_flags/nrf_atflags.c
        ${SDK_ROOT}/components/libraries/atomic/nrf_atomic.c
        ${SDK_ROOT}/components/libraries/balloc/nrf_balloc.c
        ${SDK_ROOT}/external/fprintf/nrf_fprintf.c
        ${SDK_ROOT}/external/fprintf/nrf_fprintf_format.c
        ${SDK_ROOT}/components/libraries/fstorage/nrf_fstorage.c
        ${SDK_ROOT}/components/libraries/fstorage/nrf_fstorage_sd.c
        ${SDK_ROOT}/components/libraries/memobj/nrf_memobj.c
        ${SDK_ROOT}/components/libraries/pwr_mgmt/nrf_pwr_mgmt.c
        ${SDK_ROOT}/components/libraries/ringbuf/nrf_ringbuf.c
        ${SDK_ROOT}/components/libraries/experimental_section_vars/nrf_section_iter.c
        ${SDK_ROOT}/components/libraries/sortlist/nrf_sortlist.c
        ${SDK_ROOT}/components/libraries/strerror/nrf_strerror.c
        ${SDK_ROOT}/integration/nrfx/legacy/nrf_drv_clock.c
        ${SDK_ROOT}/integration/nrfx/legacy/nrf_drv_uart.c
        ${SDK_ROOT}/modules/nrfx/soc/nrfx_atomic.c
        ${SDK_ROOT}/modules/nrfx/drivers/src/nrfx_clock.c
        ${SDK_ROOT}/modules/nrfx/drivers/src/nrfx_gpiote.c
        ${SDK_ROOT}/modules/nrfx/drivers/src/prs/nrfx_prs.c
        ${SDK_ROOT}/modules/nrfx/drivers/src/nrfx_uart.c
        ${SDK_ROOT}/modules/nrfx/drivers/src/nrfx_uarte.c
        ${SDK_ROOT}/components/libraries/bsp/bsp.c
        ${SDK_ROOT}/components/libraries/bsp/bsp_btn_ble.c
        ${PROJ_DIR}/main.c
        ${SDK_ROOT}/components/libraries/bootloader/dfu/nrf_dfu_svci.c
        ${SDK_ROOT}/external/segger_rtt/SEGGER_RTT.c
        ${SDK_ROOT}/external/segger_rtt/SEGGER_RTT_Syscalls_GCC.c
        ${SDK_ROOT}/external/segger_rtt/SEGGER_RTT_printf.c
        ${SDK_ROOT}/components/ble/peer_manager/auth_status_tracker.c
        ${SDK_ROOT}/components/ble/common/ble_advdata.c
        ${SDK_ROOT}/components/ble/ble_advertising/ble_advertising.c
        ${SDK_ROOT}/components/ble/common/ble_conn_params.c
        ${SDK_ROOT}/components/ble/common/ble_conn_state.c
        ${SDK_ROOT}/components/ble/common/ble_srv_common.c
        ${SDK_ROOT}/components/ble/peer_manager/gatt_cache_manager.c
        ${SDK_ROOT}/components/ble/peer_manager/gatts_cache_manager.c
        ${SDK_ROOT}/components/ble/peer_manager/id_manager.c
        ${SDK_ROOT}/components/ble/nrf_ble_gatt/nrf_ble_gatt.c
        ${SDK_ROOT}/components/ble/nrf_ble_qwr/nrf_ble_qwr.c
        ${SDK_ROOT}/components/ble/peer_manager/peer_data_storage.c
        ${SDK_ROOT}/components/ble/peer_manager/peer_database.c
        ${SDK_ROOT}/components/ble/peer_manager/peer_id.c
        ${SDK_ROOT}/components/ble/peer_manager/peer_manager.c
        ${SDK_ROOT}/components/ble/peer_manager/peer_manager_handler.c
        ${SDK_ROOT}/components/ble/peer_manager/pm_buffer.c
        ${SDK_ROOT}/components/ble/peer_manager/security_dispatcher.c
        ${SDK_ROOT}/components/ble/peer_manager/security_manager.c
        ${SDK_ROOT}/components/ble/ble_services/ble_dfu/ble_dfu.c
        ${SDK_ROOT}/components/ble/ble_services/ble_dfu/ble_dfu_bonded.c
        ${SDK_ROOT}/components/ble/ble_services/ble_dfu/ble_dfu_unbonded.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh_ble.c
        ${SDK_ROOT}/components/softdevice/common/nrf_sdh_soc.c
        )

add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-size ${EXECUTABLE})

add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND arm-none-eabi-objcopy -O ihex ${EXECUTABLE} ${PROJECT_NAME}.hex
        COMMAND arm-none-eabi-objcopy -O binary ${EXECUTABLE} ${PROJECT_NAME}.bin)