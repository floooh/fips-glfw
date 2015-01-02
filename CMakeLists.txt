if (FIPS_MACOS OR FIPS_WINDOWS OR FIPS_LINUX)

    fips_begin_lib(glfw3)
        
        include_directories(. glfw/src glfw/include)
        add_definitions(-D_GLFW_USE_CONFIG_H)
        
        fips_files(glfw_config.h)
        fips_dir(glfw/include/GLFW)
        fips_files(glfw3.h glfw3native.h)
        fips_dir(glfw/src)
        fips_files(
            internal.h
            context.c init.c input.c monitor.c window.c
        )
        if (FIPS_MACOS)
            add_definitions(-DFIPS_GLFW_MACOS=1)
            fips_files(
                cocoa_platform.h iokit_joystick.h posix_tls.h
                cocoa_init.m cocoa_monitor.m cocoa_window.m
                iokit_joystick.m mach_time.c posix_tls.c
                nsgl_context.h nsgl_context.m
            )
        elseif (FIPS_WINDOWS)
            add_definitions(-DFIPS_GLFW_WINDOWS=1)
            fips_files(
                win32_platform.h win32_tls.h winmm_joystick.h
                win32_init.c win32_monitor.c win32_time.c
                win32_tls.c win32_window.c winmm_joystick.c
                wgl_context.h wgl_context.c
            )
        elseif (FIPS_LINUX)
            add_definitions(-DFIPS_GLFW_LINUX=1)
            fips_files(
                x11_platform.h xkb_unicode.h linux_joystick.h
                posix_time.h posix_tls.h
                x11_init.c x11_monitor.c x11_window.c
                xkd_unicode.c linux_joystick.c posix_time.c posix_tlc.c
                glx_context.h glx_context.c
            )
        endif()
    fips_end_lib()

    # hack to silence some warnings in GLFW code
    if (FIPS_MACOS)
        set_target_properties(glfw3 PROPERTIES COMPILE_FLAGS "-Wno-sign-compare -Wno-deprecated-declarations")
    endif()
endif()

