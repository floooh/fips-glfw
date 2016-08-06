if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup()
    fips_project(fips-glfw)
endif()

if ((FIPS_MACOS OR FIPS_WINDOWS OR FIPS_LINUX) AND NOT FIPS_OSX_USE_ARC)

    fips_begin_lib(glfw3)

        include_directories(. glfw/src glfw/include)
        add_definitions(-D_GLFW_USE_CONFIG_H)
        if (FIPS_WINDOWS)
            add_definitions(-D_GLFW_USE_DWM_SWAP_INTERVAL)
        endif()

        fips_files(glfw_config.h)
        fips_dir(glfw/include/GLFW GROUP .)
        fips_files(glfw3.h glfw3native.h)
        fips_dir(glfw/src GROUP .)
        fips_files(
            internal.h
            context.c init.c input.c monitor.c vulkan.c window.c
        )
        if (FIPS_MACOS)
            add_definitions(-DFIPS_GLFW_MACOS=1)
            fips_files(
                cocoa_platform.h cocoa_joystick.h posix_tls.h
                cocoa_init.m cocoa_monitor.m cocoa_window.m
                cocoa_joystick.m cocoa_time.c posix_tls.c
                nsgl_context.h nsgl_context.m
            )
            fips_frameworks_osx(Cocoa CoreVideo OpenGL Carbon)
        elseif (FIPS_WINDOWS)
            add_definitions(-DFIPS_GLFW_WINDOWS=1)
            fips_files(
                win32_platform.h win32_joystick.h
                win32_init.c win32_monitor.c win32_time.c
                win32_tls.c win32_window.c win32_joystick.c
                wgl_context.h wgl_context.c
                egl_context.h egl_context.c
            )
            fips_libs(opengl32)
        elseif (FIPS_LINUX)
            add_definitions(-DFIPS_GLFW_LINUX=1)
            fips_files(
                x11_platform.h xkb_unicode.h linux_joystick.h
                posix_time.h posix_tls.h
                x11_init.c x11_monitor.c x11_window.c
                xkb_unicode.c linux_joystick.c posix_time.c posix_tls.c
                glx_context.h glx_context.c
                egl_context.h egl_context.c
            )
            fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL m ${CMAKE_DL_LIBS})
        endif()
    fips_end_lib()

    # hack to silence some warnings in GLFW code
    if (FIPS_CLANG)
        set_target_properties(glfw3 PROPERTIES COMPILE_FLAGS "-Wno-sign-compare -Wno-deprecated-declarations")
    elseif(FIPS_GCC)
        set_target_properties(glfw3 PROPERTIES COMPILE_FLAGS "-Wno-sign-compare")
    elseif(FIPS_MSVC)
        set_target_properties(glfw3 PROPERTIES COMPILE_FLAGS "/wd4152 /wd4204 /wd4242 /wd4244 /wd4668 /wd4996")
    endif()
endif()

if (NOT FIPS_IMPORT)
    fips_finish()
endif()
