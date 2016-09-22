cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

INCLUDE_DIRECTORIES(
	SYSTEM ${MYSQL_INCLUDE_DIRS}
)

LINK_DIRECTORIES(${MYSQL_LIB_DIR} ${MYSQL_LIBRARY})

SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

if (MSVC AND mysqlpp_MSVC_STATIC_RUNTIME)
	foreach(flag_var
			CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
			CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
		if(${flag_var} MATCHES "/MD")
			string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
		endif(${flag_var} MATCHES "/MD")
	endforeach(flag_var)
endif (MSVC AND mysqlpp_MSVC_STATIC_RUNTIME)

SET(PROJECT "mysqlpp")
project(${PROJECT})

SET(CMAKE_BUILD_TYPE $ENV{BUILD_TYPE})

#add_definitions(-D_USRDLL)
add_definitions(-DHAVE_MYSQL_SSL_SET)

IF (MSVC)
	MESSAGE("Building for Visual Stuido")
	add_definitions(-D_WIN32)
	set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /LTCG /INCREMENTAL:NO /ignore:4075")
	
	IF($(CMAKE_BUILD_TYPE) STREQUAL DEBUG)
		add_definitions(-D_DEBUG)
		
		set(CMAKE_CXX_FLAGS " /Od /EHsc /Zi /GR /nologo /RTC1")
		set(MSVC_LINK_FLAGS " /DEBUG /INCREMENTAL")
	ELSE()
	  add_definitions(-DNDEBUG)
	  
		set(CMAKE_CXX_FLAGS " /Ox /EHsc /GL /GR /nologo")
		set(MSVC_LINK_FLAGS " /RELEASE /LTCG /INCREMENTAL:NO /NOLOGO")
	ENDIF()
ENDIF()

set(MYSQLPP_VERSION_MAJOR 3)
set(MYSQLPP_VERSION_MINOR 2)
set(MYSQLPP_VERSION_BUGFIX 3)
	
configure_file(
	lib/mysql++.h.in
	${CMAKE_CURRENT_LIST_DIR}/lib/mysql++.h
)

configure_file(
	config.h.in
	${CMAKE_CURRENT_LIST_DIR}/lib/config.h
)

SET( SOURCES_BASE
lib/beemutex.cpp
lib/cmdline.cpp
lib/connection.cpp
lib/cpool.cpp
lib/datetime.cpp
lib/dbdriver.cpp
lib/field_names.cpp
lib/field_types.cpp
lib/manip.cpp
lib/myset.cpp
lib/mysql++.cpp
lib/mystring.cpp
lib/null.cpp
lib/options.cpp
lib/qparms.cpp
lib/query.cpp
lib/result.cpp
lib/row.cpp
lib/scopedconnection.cpp
lib/sql_buffer.cpp
lib/sqlstream.cpp
lib/ssqls2.cpp
lib/stadapter.cpp
lib/tcp_connection.cpp
lib/transaction.cpp
lib/type_info.cpp
lib/uds_connection.cpp
lib/utility.cpp
lib/vallist.cpp
lib/wnp_connection.cpp
lib/autoflag.h
lib/beemutex.h
lib/cmdline.h
lib/common.h
lib/comparable.h
lib/connection.h
lib/cpool.h
lib/datetime.h
lib/dbdriver.h
lib/exceptions.h
lib/field.h
lib/field_names.h
lib/field_types.h
lib/insertpolicy.h
lib/manip.h
lib/myset.h
lib/mystring.h
lib/noexceptions.h
lib/null.h
lib/options.h
lib/qparms.h
lib/querydef.h
lib/query.h
lib/refcounted.h
lib/result.h
lib/row.h
lib/scopedconnection.h
lib/sql_buffer.h
lib/sqlstream.h
lib/sql_types.h
lib/ssqls2.h
lib/ssqls.h
lib/stadapter.h
lib/stream2string.h
lib/tcp_connection.h
lib/tiny_int.h
lib/transaction.h
lib/type_info.h
lib/uds_connection.h
lib/utility.h
lib/vallist.h
lib/wnp_connection.h
lib/mysql++.h
)

if (mysqlpp_STATIC_LIB)
  add_definitions(-DMYSQLPP_NO_DLL)
	add_library(mysqlpp STATIC ${SOURCES_BASE})
else()
  add_definitions(-DMYSQLPP_MAKING_DLL)
  
	#todo add a way to compile as shared lib
	add_library(mysqlpp STATIC ${SOURCES_BASE})
endif()
