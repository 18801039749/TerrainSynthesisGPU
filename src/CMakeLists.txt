#FIND_PACKAGE(Qt4)
FIND_PACKAGE(CUDA)

SET(QtApp_MOCS )
SET(QtApp_RCCS application.qrc)
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR})

SET(COMMON_SOURCES dtts_2dmorph.cpp dtts_ppa.cpp dtts_wavelet.cpp external/noise/noise.cpp  external/maxflow/graph.cpp external/maxflow/maxflow.cpp)

if(CUDA_FOUND)
  SET(SOURCES ${COMMON_SOURCES} dtts_patchsynthesis.cu dtts_image.cu dtts_match.cu dtts_merging.cu external/SpeedIT_Classic/si_classic.cu)
else()
  SET(SOURCES ${COMMON_SOURCES} dtts_patchsynthesis.cpp dtts_image.cpp dtts_match.cpp dtts_merging.cpp external/SpeedIT_Classic/si_classic_seq.cpp)
endif()

if (QT_FOUND)
  SET(SOURCES ${SOURCES} dtts_main_gui.cpp)
else()
  SET(SOURCES ${SOURCES} dtts_main.cpp)
endif()

if (CUDA_FOUND)
  SET(EXECFILE TerrainSynthesisGPU)
else()
  SET(EXECFILE TerrainSynthesisCPU)
endif()

if (QT_FOUND)
  INCLUDE(${QT_USE_FILE})
  ADD_DEFINITIONS(${QT_DEFINITIONS})
  QT4_WRAP_CPP(QtApp_MOC_SRCS ${QtApp_MOCS})
  QT4_ADD_RESOURCES(QtApp_RCC_SRCS ${QtApp_RCCS})
  SET(EXECFILE ${EXECFILE}QT)
endif()

ADD_EXECUTABLE(${EXECFILE} ${SOURCES} ${QtApp_MOC_SRCS} ${QtApp_RCC_SRCS})
if (QT_FOUND)
  TARGET_LINK_LIBRARIES(${EXECFILE} ${QT_LIBRARIES} ${QT_QTDECLARATIVE_LIBRARY})
endif()
