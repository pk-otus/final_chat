cmake_minimum_required(VERSION 3.2)

if (DEFINED ENV{TRAVIS_BUILD_NUMBER})
   project(chat_server VERSION 0.0.$ENV{TRAVIS_BUILD_NUMBER})
else()
   project(chat_server VERSION 0.0.1)   
endif()

find_package(GTest REQUIRED)
find_package(Boost COMPONENTS system REQUIRED)

set(INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
file(GLOB HEADER_FILES ${INCLUDE_DIR}/*.h)
add_executable(chat_server ${HEADER_FILES} main.cpp)

set_target_properties(chat_server PROPERTIES
	CXX_STANDARD 14
	CXX_STANDARD_REQUIRED ON
	COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
	INCLUDE_DIRECTORIES "${Boost_INCLUDE_DIR};${INCLUDE_DIR}"
)

enable_testing()
add_executable(chat_server_tests ${HEADER_FILES} test/test_main.cpp)
set_target_properties(chat_server_tests PROPERTIES INCLUDE_DIRECTORIES ${GTEST_INCLUDE_DIRS} )

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
	set_target_properties(chat_server PROPERTIES COMPILE_OPTIONS -W3)
	target_link_libraries(chat_server ${Boost_LIBRARIES} )
	target_link_libraries(chat_server_tests ${GTEST_LIBRARIES} ${GTEST_MAIN_LIBRARIES})
else()
	set_target_properties(chat_server PROPERTIES COMPILE_OPTIONS -Wpedantic -Wall -Wextra)
	target_link_libraries(chat_server pthread ${Boost_LIBRARIES} )	
	target_link_libraries(chat_server_tests ${GTEST_LIBRARIES} ${GTEST_MAIN_LIBRARIES})
	target_link_libraries(chat_server_tests pthread)
endif()

add_test(test1	chat_server_tests)

install(TARGETS chat_server RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT a@a.a)

include (CPack)

