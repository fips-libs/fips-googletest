#
# project: fips-googletest
#

if (NOT FIPS_IMPORT)
	cmake_minimum_required(VERSION 2.8)
	# include the fips main cmake file
	get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
	include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
	fips_setup()
	fips_project(fips-googletest)
endif()

fips_begin_lib(googlemock)
	fips_dir(gtest/googlemock/src)
	fips_include_directories(gtest/googlemock)
	fips_files(gmock-all.cc)
fips_end_lib()

fips_begin_lib(googletest)
	fips_dir(gtest/googletest/src)
	fips_include_directories(gtest/googletest)
	fips_files(gtest-all.cc)
fips_end_lib()

if (NOT FIPS_EXCEPTIONS)
	set(GOOGLETEST_NO_EXCEPTIONS "-fno-exceptions")
endif()

if (NOT FIPS_RTTI)
	add_definitions(-DGTEST_HAS_RTTI=0)
	set(GOOGLETEST_NO_RTTI "-fno-rtti")
else()
	add_definitions(-DGTEST_HAS_RTTI=1)
endif()

if (FIPS_CLANG OR FIPS_GCC)
	set_target_properties(googletest PROPERTIES COMPILE_FLAGS "${GOOGLETEST_NO_EXCEPTIONS} ${GOOGLETEST_NO_RTTI}")
	set_target_properties(googlemock PROPERTIES COMPILE_FLAGS "${GOOGLETEST_NO_EXCEPTIONS} ${GOOGLETEST_NO_RTTI}")
elseif(FIPS_MSVC)
	if (NOT FIPS_EXCEPTIONS)
		string(REPLACE "/EHsc" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS}) # -fno-exceptions
	endif()

	if (NOT TB_ENABLE_RTTI)
		string(REPLACE "/GR" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS}) # -fno-rtti
	endif()
	set_target_properties(googletest PROPERTIES COMPILE_FLAGS "/wd4530 /wd4577 /WX-")
	set_target_properties(googlemock PROPERTIES COMPILE_FLAGS "/wd4530 /wd4577 /WX-")
endif()

if (NOT FIPS_IMPORT)
	fips_finish()
endif()
