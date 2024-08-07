if (APPLE AND LLVM_ENABLE_LOCAL_SUBMODULE_VISIBILITY)
  # Work around an issue with the Python headers, which have a modular include
  # inside an extern "C" block.
  remove_module_flags()
endif()

if(NOT LLDB_PYTHON_RELATIVE_PATH)
  message(FATAL_ERROR "LLDB_PYTHON_RELATIVE_PATH is not set.")
endif()
add_definitions(-DLLDB_PYTHON_RELATIVE_LIBDIR="${LLDB_PYTHON_RELATIVE_PATH}")

if(NOT LLDB_PYTHON_EXE_RELATIVE_PATH)
  message(FATAL_ERROR "LLDB_PYTHON_EXE_RELATIVE_PATH is not set.")
endif()
add_definitions(-DLLDB_PYTHON_EXE_RELATIVE_PATH="${LLDB_PYTHON_EXE_RELATIVE_PATH}")


if (LLDB_ENABLE_LIBEDIT)
  list(APPEND LLDB_LIBEDIT_LIBS LibEdit::LibEdit)
endif()

add_lldb_library(lldbPluginScriptInterpreterPythonInterfaces
  ScriptedPythonInterface.cpp
  ScriptedThreadPythonInterface.cpp

  LINK_LIBS
    lldbCore
    lldbHost
    lldbInterpreter
    lldbTarget
    ${Python3_LIBRARIES}
    ${LLDB_LIBEDIT_LIBS}

  LINK_COMPONENTS
    Support
  )

add_subdirectory(OperatingSystemPythonInterface)
add_subdirectory(ScriptedPlatformPythonInterface)
if (WIN32)
  set(ORIGINAL_CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
  set(CMAKE_CXX_FLAGS "${ORIGINAL_CMAKE_CXX_FLAGS} /H")
endif()
add_subdirectory(ScriptedProcessPythonInterface)
if (WIN32)
  set(CMAKE_CXX_FLAGS "${ORIGINAL_CMAKE_CXX_FLAGS}")
endif()
add_subdirectory(ScriptedThreadPlanPythonInterface)

