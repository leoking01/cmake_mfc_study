cmake_minimum_required( VERSION 3.0 )
 
##项目名
#set ( PROJ_NAME  DCMLIB )
#project ( ${PROJ_NAME} )
 
#设置变量
set ( CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS ON )
 
set ( CMAKE_AUTOMOC ON )#set()就是设置变量
set ( CMAKE_AUTORCC ON )
 
# 3) 取得源文件列表
AUX_SOURCE_DIRECTORY( ./       PROJ_SRC_COMMON_LIST )#查询commom文件夹下的所有cpp文件
AUX_SOURCE_DIRECTORY( ./common      PROJ_SRC_COMMON_LIST )#查询commom文件夹下的所有cpp文件
AUX_SOURCE_DIRECTORY( ./LibMy      PROJ_SRC_COMMON_LIST )#查询commom文件夹下的所有cpp文件
#资源文件一般是QT会用到
set ( PROJ_QRC_LIST   )
set ( PROJ_SRC_LIST  ${PROJ_SRC_COMMON_LIST})
 
message("Found source files:")#message就跟c++的printf一样打印出结果
foreach( F ${PROJ_SRC_LIST} )
message(${F})
endforeach(${F})
 
message("Found QRC files:")
foreach( F ${PROJ_QRC_LIST} )
message(${F})
endforeach(${F})
 
 
# 4) 处理
if( MINGW )
  set ( CMAKE_RC_COMPLIER_INIT windres )
  enable_language( RC )
  set ( CMAKE_RC_COMPLIE_OBJECT
        "<CMAKE_RC_COMPLIER> <FLAGS> -o coff <DEFINES> -i <SOURCE> -o <OBJECT>")
endif()
 
# 5) 确定目标
set ( WIN_SYSTEM )
if(WIN32)
  if(MSVC)
     set ( WIN_SYSTEM  "WIN32" ) 
  elseif(CMAKE_COMPLIER_IS_GNUCXX)
     set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mwindows")
  else()
  endif()
elseif(UNIX)
  # nothing to do
else()
  message(SEND_ERROR "This cmake file only used in Windows!")
endif()
 
set(CMAKE_CXX_FLAGS_DEBUG    "${CMAKE_CXX_FLAGS_DEBUG}   -DQLEMR_CFG_DIAG_USE_MEM_CNTR  -DQLEMR_CFG_DIAG_USE_ASSERT -DQLEMR_CFG_DIAG_USE_DBG_OUTPUT ")
set(CMAKE_C_FLAGS_DEBUG      "${CMAKE_C_FLAGS_DEBUG}     -DQLEMR_CFG_DIAG_USE_MEM_CNTR  -DQLEMR_CFG_DIAG_USE_ASSERT -DQLEMR_CFG_DIAG_USE_DBG_OUTPUT ")
set(CMAKE_CXX_FLAGS_RELEASE  "${CMAKE_CXX_FLAGS_RELEASE} -DQLEMR_CFG_DIAG_USE_MEM_CNTR  ")
set(CMAKE_C_FLAGS_RELEASE    "${CMAKE_C_FLAGS_RELEASE}   -DQLEMR_CFG_DIAG_USE_MEM_CNTR  ")
#添加头文件包含目录
include_directories( ../../third-part/include)
#添加lib文件链接目录
link_directories( ${PROJECT_SOURCE_DIR}/../../third-part/lib )
 
#设置预编译项
add_definitions( -DQLEMR_CFG_DLL )
#共享DLL中使用 MFC
ADD_DEFINITIONS(-D_AFXDLL)
SET(CMAKE_MFC_FLAG 2)#设置CMAKE_MFC_FLAG为2,0表示使用标准的MFC，1表示静态MFC,2表示共享



#设置字符集
add_definitions(-DUNICODE -D_UNICODE)

# add_executable(DCMLIB WIN32 ${PROJ_SRC_LIST}) #第一个参数是项目名字，第二个是，win32，第三个是所有的cpp文件列表

# 生成什么库，STATIC静态，SHARED动态库
add_library( libMy SHARED ${PROJ_SRC_LIST} ${PROJ_QRC_LIST} )


#添加链接库 
target_link_libraries( libMy debug      Ws2_32.lib netapi32.lib )
target_link_libraries( libMy optimized  Ws2_32.lib netapi32.lib  )

#设置目标属性 
set_target_properties( libMy PROPERTIES DEBUG_OUTPUT_NAME      libMy )
set_target_properties( libMy PROPERTIES RELEASE_OUTPUT_NAME    libMyd  )
# 6) 复制生成文件
install( FILES       ${PROJECT_SOURCE_DIR}/../../update.txt
         DESTINATION ${CMAKE_INSTALL_PREFIX} )#DESTINATION指定安装路径
 
 

 
# add_executable( myExe WIN32 ${PROJ_SRC_LIST}) #第一个参数是项目名字，第二个是，win32，第三个是所有的cpp文件列表
