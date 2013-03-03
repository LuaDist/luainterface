# Copyright (C) 2007-2013 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( luainterface C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
include ( lua )

set ( CSHARP_PLATFORM "x86")

find_package ( CSharp REQUIRED )
find_package ( Lua REQUIRED )

set ( ASSEMBLY_SRC 
  AssemblyInfo.cs
  LuaException.cs
  LuaTable.cs
  CheckType.cs
  LuaFunction.cs
  LuaUserData.cs
  GenerateEventAssembly.cs  
  LuaGlobalAttribute.cs
  Metatables.cs
  LuaBase.cs
  LuaHideAttribute.cs
  MethodWrapper.cs
  Lua.cs  
  LuaRegistrationHelper.cs  
  ObjectTranslator.cs
  LuaDLL.cs
  LuaScriptException.cs
  ProxyType.cs
)

# Istall STUB
include_directories ( ${LUA_INCLUDE_DIR} )
add_library ( luanet SHARED src/luastdcall.c )
target_link_libraries ( luanet ${LUA_LIBRARIES} )

# Use CSharp compiler using custom targets
set ( ASSEMBLY ${CMAKE_CURRENT_BINARY_DIR}/luainterface.dll )
set ( RUNNER ${CMAKE_CURRENT_BINARY_DIR}/luai.exe )

add_custom_target ( luainterface ALL
  COMMAND ${CSHARP_COMPILER} /platform:${CSHARP_PLATFORM} /define:__Windows__ /define:__lib__ /define:__novs__ -out:${ASSEMBLY} -t:library ${ASSEMBLY_SRC}
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src
  COMMENT "[CSharp] Compiling luainterface.dll"
)

add_custom_target ( luai ALL
  DEPENDS luainterface
  COMMAND ${CSHARP_COMPILER} /platform:${CSHARP_PLATFORM} -out:${RUNNER} -r:${ASSEMBLY} LuaNetRunner.cs
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src
  COMMENT "[CSharp] Compiling luai.exe"
)

install ( TARGETS luanet LIBRARY DESTINATION ${INSTALL_LIB} RUNTIME DESTINATION ${INSTALL_BIN} )
install ( PROGRAMS ${RUNNER} ${ASSEMBLY} DESTINATION ${INSTALL_BIN} COMPONENT Runtime )

install_doc ( doc/ )
install_example ( samples/ )
install_test ( tests/ )
install_data ( README.md )
