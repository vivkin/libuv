cmake_minimum_required(VERSION 2.8.12)
project(libuv)

set(UV_SOURCES
    include/uv.h
    include/tree.h
    include/uv-errno.h
    include/uv-threadpool.h
    include/uv-version.h
    src/fs-poll.c
    src/heap-inl.h
    src/inet.c
    src/queue.h
    src/threadpool.c
    src/uv-common.c
    src/uv-common.h
    src/version.c
)

if(WIN32)
    set(UV_SOURCES ${UV_SOURCES}
        include/uv-win.h
        src/win/async.c
        src/win/atomicops-inl.h
        src/win/core.c
        src/win/dl.c
        src/win/error.c
        src/win/fs.c
        src/win/fs-event.c
        src/win/getaddrinfo.c
        src/win/getnameinfo.c
        src/win/handle.c
        src/win/handle-inl.h
        src/win/internal.h
        src/win/loop-watcher.c
        src/win/pipe.c
        src/win/thread.c
        src/win/poll.c
        src/win/process.c
        src/win/process-stdio.c
        src/win/req.c
        src/win/req-inl.h
        src/win/signal.c
        src/win/stream.c
        src/win/stream-inl.h
        src/win/tcp.c
        src/win/tty.c
        src/win/timer.c
        src/win/udp.c
        src/win/util.c
        src/win/winapi.c
        src/win/winapi.h
        src/win/winsock.c
        src/win/winsock.h
    )
    set(UV_DEFINITIONS _WIN32_WINNT=0x0600 _GNU_SOURCE)
    set(UV_LIBRARIES advapi32 iphlpapi psapi shell32 ws2_32)
endif()

if(UNIX)
    set(UV_SOURCES ${UV_SOURCES}
        include/uv-unix.h
        include/uv-linux.h
        include/uv-sunos.h
        include/uv-darwin.h
        include/uv-bsd.h
        include/uv-aix.h
        src/unix/async.c
        src/unix/atomic-ops.h
        src/unix/core.c
        src/unix/dl.c
        src/unix/fs.c
        src/unix/getaddrinfo.c
        src/unix/getnameinfo.c
        src/unix/internal.h
        src/unix/loop.c
        src/unix/loop-watcher.c
        src/unix/pipe.c
        src/unix/poll.c
        src/unix/process.c
        src/unix/signal.c
        src/unix/spinlock.h
        src/unix/stream.c
        src/unix/tcp.c
        src/unix/thread.c
        src/unix/timer.c
        src/unix/tty.c
        src/unix/udp.c
    )
    set(UV_DEFINITIONS _LARGEFILE_SOURCE _FILE_OFFSET_BITS=64)
    set(UV_LIBRARIES m)

    if(APPLE)
        set(UV_SOURCES ${UV_SOURCES}
            src/unix/kqueue.c
            src/unix/darwin.c
            src/unix/fsevents.c
            src/unix/darwin-proctitle.c
            )
        set(UV_DEFINITIONS ${UV_DEFINITIONS} _DARWIN_USE_64_BIT_INODE=1)
        set(UV_LIBRARIES ${UV_LIBRARIES} pthread)
    elseif(ANDROID)
        set(UV_SOURCES ${UV_SOURCES}
            src/unix/linux-core.c
            src/unix/linux-inotify.c
            src/unix/linux-syscalls.c
            src/unix/linux-syscalls.h
            src/unix/pthread-fixes.c
            src/unix/android-ifaddrs.c
            )
        set(UV_DEFINITIONS ${UV_DEFINITIONS} _GNU_SOURCE)
        set(UV_LIBRARIES ${UV_LIBRARIES} dl)
    else()
        set(UV_SOURCES ${UV_SOURCES}
            src/unix/linux-core.c
            src/unix/linux-inotify.c
            src/unix/linux-syscalls.c
            src/unix/linux-syscalls.h
            )
        set(UV_DEFINITIONS ${UV_DEFINITIONS} _GNU_SOURCE)
        set(UV_LIBRARIES ${UV_LIBRARIES} pthread dl rt)
    endif()
endif()

add_library(uv STATIC ${UV_SOURCES})
target_include_directories(uv PRIVATE src PUBLIC include)
target_compile_definitions(uv PRIVATE ${UV_DEFINITIONS})
target_link_libraries(uv INTERFACE ${UV_LIBRARIES})
