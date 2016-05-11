cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

INCLUDE_DIRECTORIES(
	SYSTEM ${MYSQL_INCLUDE_DIRS}
)

LINK_DIRECTORIES(${MYSQL_LIB_DIR} ${MYSQL_LIBRARY})

SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

SET(PROJECT "mysqlpp")
project(${PROJECT})

SET(CMAKE_BUILD_TYPE $ENV{BUILD_TYPE})

IF (${CMAKE_CXX_COMPILER_ID} STREQUAL MSVC)

	add_definitions(-D_USRDLL)
	add_definitions(-DMYSQLPP_MAKING_DLL)
	add_definitions(-DHAVE_MYSQL_SSL_SET)
	
IF($(CMAKE_BUILD_TYPE) STREQUAL DEBUG)
	add_definitions(-D_DEBUG)
	add_definitions(-D_WIN32)
	
	set(CMAKE_CXX_FLAGS " /Od /EHsc /Zi /GR /nologo /RTC1")
	set(MSVC_LINK_FLAGS " /DEBUG /INCREMENTAL")
ELSE()
    add_definitions(-D_WIN32)
    add_definitions(-DHAVE_MYSQL_SSL_SET)
	
	set(CMAKE_CXX_FLAGS " /Ox /EHsc /GL /GR /nologo")
	set(MSVC_LINK_FLAGS " /RELEASE /LTCG /INCREMENTAL:NO /NOLOGO")
ENDIF()

#todo: check to make sure we want to do this
foreach(flag_var
        CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
        CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
   if(${flag_var} MATCHES "/MD")
      string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
   endif(${flag_var} MATCHES "/MD")
endforeach(flag_var)

ENDIF()

SET( SOURCES_BASE
src/beemutex.cpp
src/cmdline.cpp
src/connection.cpp
src/cpool.cpp
src/datetime.cpp
src/dbdriver.cpp
src/field_names.cpp
src/field_types.cpp
src/manip.cpp
src/myset.cpp
src/mysql++.cpp
src/mystring.cpp
src/null.cpp
src/options.cpp
src/qparms.cpp
src/query.cpp
src/result.cpp
src/row.cpp
src/scopedconnection.cpp
src/sql_buffer.cpp
src/sqlstream.cpp
src/ssqls2.cpp
src/stadapter.cpp
src/tcp_connection.cpp
src/transaction.cpp
src/type_info.cpp
src/uds_connection.cpp
src/utility.cpp
src/vallist.cpp
src/wnp_connection.cpp

src/autoflag.h
src/beemutex.h
src/cmdline.h
src/common.h
src/comparable.h
src/connection.h
src/cpool.h
src/datetime.h
src/dbdriver.h
src/exceptions.h
src/field.h
src/field_names.h
src/field_types.h
src/insertpolicy.h
src/manip.h
src/myset.h
src/mystring.h
src/noexceptions.h
src/null.h
src/options.h
src/qparms.h
src/querydef.h
src/query.h
src/refcounted.h
src/result.h
src/row.h
src/scopedconnection.h
src/sql_buffer.h
src/sqlstream.h
src/sql_types.h
src/ssqls2.h
src/ssqls.h
src/stadapter.h
src/stream2string.h
src/tcp_connection.h
src/tiny_int.h
src/transaction.h
src/type_info.h
src/uds_connection.h
src/utility.h
src/vallist.h
src/wnp_connection.h
src/mysql++.h
)
#todo add a way to compile as shared lib
add_library(mysqlpp STATIC ${SOURCES_BASE})
