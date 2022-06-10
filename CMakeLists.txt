# CMakeLists.txt
cmake_minimum_required(VERSION 3.0)
project(test)

# Find dependencies provided by vcpkg (via vcpkg.cmake)
find_package(unofficial-sqlite3 CONFIG REQUIRED)
find_package(fmt CONFIG REQUIRED)

# ============================================================================================
# Env var BOOST_ROOT should point to local Boost installation
message(STATUS "")
message(STATUS "=== Boost ==================================================================")
find_package(Boost 1.72.0 REQUIRED COMPONENTS
        atomic
        chrono
        date_time
        filesystem
        iostreams
        program_options
        regex
        system
        thread
        )
message(STATUS "Boost_VERSION:      ${Boost_VERSION}")
message(STATUS "Boost_INCLUDE_DIRS: ${Boost_INCLUDE_DIRS}")
message(STATUS "Boost_LIBRARY_DIRS: ${Boost_LIBRARY_DIRS}")
message(STATUS "Boost_LIBRARIES:    ${Boost_LIBRARIES}")

# ============================================================================================
# Requires lib and include dir at once, separated by ";":
#     -DOPENSSL_ROOT_DIR=${BUILD_DIR}/usr/local/lib;${BUILD_DIR}/usr/local/include
message(STATUS "")
message(STATUS "=== OpenSSL ================================================================")
set(OPENSSL_USE_STATIC_LIBS TRUE)
find_package(OpenSSL)
message(STATUS "OPENSSL_FOUND: ${OPENSSL_FOUND}")
message(STATUS "OPENSSL_INCLUDE_DIR: ${OPENSSL_INCLUDE_DIR}")
message(STATUS "OPENSSL_CRYPTO_LIBRARY: ${OPENSSL_CRYPTO_LIBRARY}")
message(STATUS "OPENSSL_SSL_LIBRARY: ${OPENSSL_SSL_LIBRARY}")
message(STATUS "OPENSSL_LIBRARIES: ${OPENSSL_LIBRARIES}")
message(STATUS "OPENSSL_VERSION: ${OPENSSL_VERSION}")

# ============================================================================================
# Requires:
#     -DBerkeleyDB_ROOT_DIR=${BUILD_DIR}/libdb/libdb-install
#     -DBERKELEYDB_INCLUDE_DIR=${BUILD_DIR}/libdb/libdb-install/include
message(STATUS "")
message(STATUS "=== Berkeley DB ============================================================")
find_package(BerkeleyDB 4)

# ============================================================================================
# Requires:
#     -Dleveldb_DIR=${BUILD_DIR}/usr/local/lib/cmake/leveldb
message(STATUS "")
message(STATUS "=== LevelDB ================================================================")
find_package(leveldb)
message(STATUS "LevelDB directory: ${leveldb_DIR}")
message(STATUS "LevelDB version: ${leveldb_VERSION}")

# main target
add_executable(main)
target_sources(main PRIVATE src/main.cpp)
target_link_libraries(main PRIVATE unofficial::sqlite3::sqlite3 fmt::fmt)
set_property(TARGET main PROPERTY CXX_STANDARD 20)

# tests target
add_executable(tests)
target_sources(tests PRIVATE src/main.cpp)
target_link_libraries(tests PRIVATE unofficial::sqlite3::sqlite3 fmt::fmt)
set_property(TARGET tests PROPERTY CXX_STANDARD 20)
enable_testing()
add_test(tests tests)