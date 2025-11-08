cmake_minimum_required(VERSION 3.10)
project(gish LANGUAGES C)
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

include(FetchContent)

cmake_policy(SET CMP0072 OLD)

find_package(Math)
find_package(Ogg REQUIRED)
find_package(OpenAL REQUIRED)
find_package(OpenGL REQUIRED)
find_package(SDL CONFIG COMPONENTS SDL)
if(NOT SDL_FOUND)
	FetchContent_Declare(
		sdl12-compat
		GIT_REPOSITORY https://github.com/libsdl-org/sdl12-compat.git
		GIT_TAG release-1.2.70
		EXCLUDE_FROM_ALL
	)
	FetchContent_MakeAvailable(sdl12-compat)
endif()

add_executable(gish WIN32
	${PROJECT_SOURCE_DIR}/main.c
)

target_link_libraries(gish PRIVATE
	SDL::SDL
	OpenGL::GL
	OpenAL::OpenAL
	$<TARGET_NAME_IF_EXISTS:Math::Math>
	Ogg::ogg
	vorbisfile
)

if(WIN32 OR MSVC OR CMAKE_SYSTEM_NAME STREQUAL "Windows")
	target_compile_definitions(gish PRIVATE WINDOZE=1)
elseif(APPLE OR CMAKE_SYSTEM_NAME STREQUAL "Darwin")
	target_compile_definitions(gish PRIVATE THINKSTUPID=1)
elseif(UNIX OR CMAKE_SYSTEM_NAME STREQUAL "Linux")
	target_compile_definitions(gish PRIVATE DETLEF=1)
endif()
