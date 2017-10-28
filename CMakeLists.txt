#
# project: fips-enkits
#

if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup(PROJECT fips-enkits)
endif()

option(ENKITS_BUILD_C_INTERFACE "Build C interface" ON)

fips_begin_lib(enkits)
    find_package(Threads REQUIRED)
    include_directories(enkiTS/src)
    fips_dir(enkiTS/src)
    fips_files(
        Atomics.h
        LockLessMultiReadPipe.h
        Threads.h
        TaskScheduler.h
        TaskScheduler.cpp
    )
    if (ENKITS_BUILD_C_INTERFACE)
        fips_files(
            TaskScheduler_c.h
            TaskScheduler_c.cpp
        )
    endif()
fips_end_lib()

if (NOT FIPS_IMPORT)
    fips_begin_app(ParallelSum cmdline)
        fips_dir(enkiTS/example)
        fips_files(
            ParallelSum.cpp
            Timer.h
        )
        fips_deps(enkits)
    fips_end_app()

    fips_begin_app(TaskThroughput cmdline)
    fips_dir(enkiTS/example)
    fips_files(
        TaskThroughput.cpp
        Timer.h
    )
    fips_deps(enkits)
    fips_end_app()

    if (ENKITS_BUILD_C_INTERFACE)
        fips_begin_app(ParallelSum_c cmdline)
        fips_dir(enkiTS/example)
        fips_files(
            ParallelSum_c.c
        )
        fips_deps(enkits)
        fips_end_app()
    endif()

    fips_finish()
endif()
