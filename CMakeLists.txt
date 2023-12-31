cmake_minimum_required(VERSION 3.22.1)

project(CMAKETUT1)

add_executable(${PROJECT_NAME} main.cpp)

add_subdirectory(Adder)

# DOWNLOAD ALL THE SUBMODULES
find_package(GIT QUIET)
if(GIT_FOUND AND EXIST "${PROJECT_SOURCE_DIR}/.git")
#Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update)
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FAT1AL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout)
        endif()
    endif()

# CHECK ALL THE SUBMODULES
if(NOT EXISTS "${PROJECT_SOURCE_DIR}/external/glfw/CMakeLists.txt")
    message(FAT1AL_ERROR "The glfw submodules was not downloaded! GIT_SUBMODULE was turned off or failed. Please checkout")
endif()

add_subdirectory(external/glfw)
target_include_directories(${PROJECT_NAME} 
    PUBLIC Adder
    PUBLIC external/glfw/include
)
target_link_directories(${PROJECT_NAME}
    PRIVATE Adder
    PRIVATE external/glfw/src
)
target_link_libraries(${PROJECT_NAME}
    adder
    glfw
)