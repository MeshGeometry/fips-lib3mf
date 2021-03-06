if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    
    set(FIPS_EXCEPTIONS ON CACHE BOOL "Enable C++ exceptions" FORCE)
    set(FIPS_RTTI  ON CACHE BOOL "Enable C++ RTTI" FORCE)

    fips_setup()
    fips_project(fips-lib3mf)
endif()

set(FIPS_EXCEPTIONS ON CACHE BOOL "Enable C++ exceptions" FORCE)
set(FIPS_RTTI ON CACHE BOOL "Enable C++ RTTI" FORCE)

# Set lib3mf options
option(LIB3MF_TESTS "Switch whether the tests of lib3mf should be build" OFF)

# Create lib3mf target
fips_add_subdirectory(lib3mf)

# Disable "treat warnings as errors" (https://github.com/floooh/fips/issues/253)
if(FIPS_MSVC)
    target_compile_options(lib3mf PUBLIC /WX-)
endif()

# Add headers for autogenerated language bindings
set(LIB3MF_AUTOGEN_DIR "${FIPS_PROJECT_BUILD_DIR}/fips-lib3mf_lib3mf/lib3mf/Autogenerated")
target_include_directories(
    lib3mf
    PUBLIC
        "${LIB3MF_AUTOGEN_DIR}/Bindings/C"
        "${LIB3MF_AUTOGEN_DIR}/Bindings/Cpp"
)

# Redirect output to fips deploy dir
set_target_properties(
    lib3mf
    PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${FIPS_PROJECT_DEPLOY_DIR}"
        RUNTIME_OUTPUT_DIRECTORY_DEBUG "${FIPS_PROJECT_DEPLOY_DIR}"
        RUNTIME_OUTPUT_DIRECTORY_RELEASE "${FIPS_PROJECT_DEPLOY_DIR}"
)

if (NOT FIPS_IMPORT)
    fips_finish()
endif()