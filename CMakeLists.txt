# Project: Tesseract-OCR
# License:  Apache 2.0
# Project URL: http://code.google.com/p/tesseract-ocr/
# CMAKE files by: zdenop@gmail.com

# mkdir build && cd build
# cmake .. -G "MinGW Makefiles"
# mingw32-make VERBOSE=1

PROJECT ( tesseract-ocr )
SET(TESSERACT_SOVERSION 3)
SET(VERSION_SERIES 0)
SET(VERSION_MAJOR 1)
SET(VERSION_MINOR 0)
SET(PROJECT_VERSION ${TESSERACT_SOVERSION}.${VERSION_SERIES}.${VERSION_MAJOR})

CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

add_definitions(-DHAVE_CONFIG_H)

if ( CMAKE_COMPILER_IS_GNUCC )
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-ignored-qualifiers")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-sign-compare")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unused-parameter")
    add_definitions("-fPIC")
endif ( CMAKE_COMPILER_IS_GNUCC )


#Pretty colors
SET(CMAKE_COLOR_MAKEFILE ON)
#Dont force verbose
SET(CMAKE_VERBOSE_MAKEFILE OFF)
#Include current dir
SET(CMAKE_INCLUDE_CURRENT_DIR TRUE)

#CMAKE_POLICY(SET CMP0003 OLD)
SET(CPACK_GENERATOR "TBZ2")
INCLUDE(CPack) 

#FIND_PACKAGE(Leptonica REQUIRED)
#INCLUDE(PkgConfigGetVar)
#INCLUDE(GNUInstallDirs)

#https://github.com/siraj/plexydesk/blob/master/CMakeLists.txt
#CONFIGURE_FILE(config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )

# CONFIGURE_FILE(
    # "${CMAKE_CURRENT_SOURCE_DIR}/cmakemodules/cmake_uninstall.cmake.in"
    # "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    # IMMEDIATE @ONLY)

# ADD_CUSTOM_TARGET(uninstall
    # "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake")
 
SET (LEPT_INC /usr/local/include/leptonica/)
SET (LEPT_LIB lept)
#http://www.vtk.org/Wiki/CMake:How_To_Find_Libraries
#find_package(LibXML++ REQUIRED)
#include_directories(${LibXML++_INCLUDE_DIRS})

#find_package(leptonica REQUIRED)



if(WIN32)
    add_definitions( -DWIN32 -DMINGW32 -c -O2 -frtti -fexceptions -mthreads -enable-stdcall-fixup -Wl,-enable-auto-import -Wl,-enable-runtime-pseudo-reloc)
    # SET (LEPT_INC "c:/usr/projects/BuildFolder/include/leptonica")
    # SET (LEPT_LIB_DIR "c:/usr/projects/BuildFolder/lib/")
    # SET (LIBS ${LIBS} -L"c:/usr/projects/BuildFolder/lib/")
    SET (LEPT_INC "vs2008/include/leptonica")
    SET (LEPT_LIB_DIR "vs2008/lib/")
    SET (LIBS ${LIBS} -L"vs2008/lib/")
    SET (LEPT_LIB lept168)
    # it is not possible to link static library created by Visual Studion to mingw...
    #SET (LEPT_LIB liblept-static-mtdll.lib)
    #add_definitions( -DMINGW32)
else(WIN32)
    SET (LEPT_INC "/usr/include/leptonica")
    SET (LEPT_LIB_DIR "/usr/lib/")
    #SET (LIBS ${LIBS} -L"vs2008/lib/")
    #SET (LEPT_LIB lept168)
endif(WIN32)

# if (LINUX)
    # set(INSTALL_TARGETS_DEFAULT_ARGS
        # RUNTIME DESTINATION bin
        # LIBRARY DESTINATION lib/myapp)
    # set(INSTALL_PLUGINS_DEFAULT_ARGS
        # RUNTIME DESTINATION bin
        # LIBRARY DESTINATION lib/myapp/plugins)
# endif(LINUX)

if (WIN32)
    SET(INSTALL_TARGETS_DEFAULT_ARGS
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
endif(WIN32)

SET(INSTALL_TARGETS_DEFAULT_ARGS
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)

#INCLUDE (${PROJECT_BINARY_DIR}/UserCacheSettings.txt OPTIONAL)

include_directories( . api ccmain ccstruct ccutil classify cube cutil dict
    image neural_networks/runtime textord training viewer wordrec vs2008/port opencl
    ${LEPT_INC})

#add_definitions ( -DHAVE_CONFIG_H )
add_definitions ( -DNDEBUG -D__MSW32__ -DUSE_STD_NAMESPACE -DHAVE_LIBLEPT)
#add_definitions ( -DUSE_STD_NAMESPACE )
#add_definitions ( -DHAVE_LIBLEPT )
#add_definitions ( -D__MSW32__ )
add_definitions ( -D__BLOB_T_DEFINED )

FILE(GLOB tesseract_api_LIB_SRCS
    api/baseapi.cpp
    api/pageiterator.cpp
    api/resultiterator.cpp
    api/renderer.cpp
#   api/tesseractmain.cpp
    ccmain/*.cpp
    ccstruct/*.cpp
#   ccutil/*.cpp
    classify/*.cpp
    cube/*.cpp
    cutil/*.cpp
    dict/*.cpp
    image/*.cpp
    neural_networks/runtime/*.cpp
    textord/*.cpp
    #training/
    viewer/*.cpp
    wordrec/*.cpp)

if(MINGW)
    FILE(GLOB tesseract_ccutils_SRC ccutil/*.cpp vs2008/port/strtok_r.cpp)
ELSE(MINGW)
    FILE(GLOB tesseract_ccutils_SRC ccutil/*.cpp)
endif(MINGW)

FILE(GLOB tesseract_ccutils_HEADERS ccutil/*.h)

FILE(GLOB tesseract_api_LIB_HEADERS api/*.h ccmain/*.h ccstruct/*.h 
    ccutil/*.h classify/*.h cube/*.h cutil/*.h dict/*.h image/*.h
    neural_networks/runtime/*.h textord/*.h training/*.h viewer/*.h
    wordrec/*.h vs2008/include/version.h)

FILE(GLOB LANGUAGE_DATA 
    tessdata/*.traineddata
    tessdata/*.cube.*
    tessdata/configs/inter
    tessdata/configs/makebox
    tessdata/configs/box.train
    tessdata/configs/unlv
    tessdata/configs/ambigs.train
    tessdata/configs/api_config
    tessdata/configs/kannada
    tessdata/configs/box.train.stderr
    tessdata/configs/logfile
    tessdata/configs/digits
    tessdata/configs/hocr
    tessdata/configs/linebox
    tessdata/configs/rebox
    tessdata/configs/strokewidth
    tessdata/tessconfigs/batch
    tessdata/tessconfigs/batch.nochop
    tessdata/tessconfigs/nobatch
    tessdata/tessconfigs/matdemo
    tessdata/tessconfigs/segdemo
    tessdata/tessconfigs/msdemo)

# IF(WIN32)
    # SET(CMAKE_FIND_LIBRARY_SUFFIXES .lib .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
    # SET(CMAKE_EXE_LINKER_FLAGS -static)
# ELSE(WIN32)
    # SET(CMAKE_FIND_LIBRARY_SUFFIXES .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
# ENDIF(WIN32)

set(CMAKE_CXX_FLAGS "-g -O2")

ADD_LIBRARY(tesseract_ccutils-static STATIC ${tesseract_ccutils_SRC})
ADD_DEPENDENCIES( tesseract_ccutils-static  ${tesseract_ccutils_HEADERS})
TARGET_LINK_LIBRARIES(tesseract_ccutils-static)

ADD_LIBRARY(tesseract_api-static STATIC ${tesseract_api_LIB_SRCS})
ADD_DEPENDENCIES( tesseract_api-static  ${tesseract_api_LIB_HEADERS})
LINK_DIRECTORIES( ${CMAKE_CURRENT_SOURCE_DIR}/${LEPT_LIB_DIR})
IF(WIN32)
    TARGET_LINK_LIBRARIES(tesseract_api-static tesseract_ccutils-static ${LEPT_LIB} ws2_32)
ELSE(WIN32)
    TARGET_LINK_LIBRARIES(tesseract_api-static tesseract_ccutils-static ${LEPT_LIB})
ENDIF(WIN32)
INSTALL(TARGETS tesseract_api-static ${INSTALL_TARGETS_DEFAULT_ARGS})

# IF(MINGW)
    # ADD_CUSTOM_COMMAND(
        # OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/wally_rc.o
        # COMMAND windres.exe
                  # -I ${CMAKE_CURRENT_SOURCE_DIR}/dist/windows
                  # -o ${CMAKE_CURRENT_BINARY_DIR}/dist/windows/${APPLICATION_EXE_NAME}_rc.o
                  # -i ${CMAKE_CURRENT_BINARY_DIR}/dist/windows/windres.rc
        # WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/dist/windows
        # COMMENT "Generating windows RC file ...")
# ENDIF(MINGW)

ADD_LIBRARY(tesseract_api SHARED ${tesseract_api_LIB_SRCS})
ADD_DEPENDENCIES( tesseract_api ${tesseract_api_LIB_HEADERS})
IF(WIN32)
    ADD_CUSTOM_COMMAND(TARGET tesseract_api PRE_BUILD  COMMAND windres -i ${CMAKE_CURRENT_SOURCE_DIR}/vs2008/port/tesseract_api.rc -o tesseract_api.o COMMENT "Windows DLL version")
    SET_TARGET_PROPERTIES(tesseract_api PROPERTIES LINK_FLAGS "tesseract_api.o")
    SET_TARGET_PROPERTIES(tesseract_api PROPERTIES COMPILE_FLAGS "-DMAKE_DLL")
    TARGET_LINK_LIBRARIES(tesseract_api tesseract_ccutils-static ${LEPT_LIB} ws2_32)
ELSE(WIN32)
    TARGET_LINK_LIBRARIES(tesseract_api pthread tesseract_ccutils-static ${LEPT_LIB})
ENDIF(WIN32)
INSTALL(TARGETS tesseract_api RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)

#SET(CMAKE_EXE_LINKER_FLAGS -static)
ADD_EXECUTABLE(tesseract api/tesseractmain.cpp)
SET_TARGET_PROPERTIES(tesseract PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
SET_TARGET_PROPERTIES(tesseract PROPERTIES OUTPUT_NAME "tesseract")
TARGET_LINK_LIBRARIES(tesseract tesseract_api-static pthread rt)
INSTALL(TARGETS tesseract RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)
                                 
ADD_EXECUTABLE(cntraining  training/cntraining.cpp training/commontraining.cpp training/tessopt.cpp training/commandlineflags.cpp)
SET_TARGET_PROPERTIES(cntraining PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
#SET_TARGET_PROPERTIES(cntraining PROPERTIES OUTPUT_NAME "cntraining")
TARGET_LINK_LIBRARIES(cntraining tesseract_api-static pthread)
INSTALL(TARGETS cntraining RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)

ADD_EXECUTABLE(mftraining  training/mftraining.cpp training/mergenf.cpp training/commontraining.cpp training/tessopt.cpp training/commandlineflags.cpp)
SET_TARGET_PROPERTIES(mftraining PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
SET_TARGET_PROPERTIES(mftraining PROPERTIES OUTPUT_NAME "mftraining")
TARGET_LINK_LIBRARIES(mftraining tesseract_api-static pthread)
INSTALL(TARGETS mftraining RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)
                                 
ADD_EXECUTABLE(unicharset_extractor  training/unicharset_extractor.cpp training/tessopt.cpp)
SET_TARGET_PROPERTIES(unicharset_extractor PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
SET_TARGET_PROPERTIES(unicharset_extractor PROPERTIES OUTPUT_NAME "unicharset_extractor")
TARGET_LINK_LIBRARIES(unicharset_extractor tesseract_api-static)
INSTALL(TARGETS unicharset_extractor RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)

ADD_EXECUTABLE(wordlist2dawg  training/wordlist2dawg.cpp training/tessopt.cpp)
SET_TARGET_PROPERTIES(wordlist2dawg PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
SET_TARGET_PROPERTIES(wordlist2dawg PROPERTIES OUTPUT_NAME "wordlist2dawg")
#TARGET_LINK_LIBRARIES(wordlist2dawg tesseract_api-static)
TARGET_LINK_LIBRARIES(wordlist2dawg tesseract_api rt)
INSTALL(TARGETS wordlist2dawg RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)
                                 
ADD_EXECUTABLE(combine_tessdata  training/combine_tessdata.cpp)
SET_TARGET_PROPERTIES(combine_tessdata PROPERTIES COMPILE_FLAGS "-DMAKE_EXE")
SET_TARGET_PROPERTIES(combine_tessdata PROPERTIES OUTPUT_NAME "combine_tessdata")
TARGET_LINK_LIBRARIES(combine_tessdata tesseract_api-static)
INSTALL(TARGETS combine_tessdata RUNTIME DESTINATION bin
                                 LIBRARY DESTINATION lib
                                 ARCHIVE DESTINATION lib)
    
SET_TARGET_PROPERTIES(tesseract_api-static tesseract_api
    PROPERTIES VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION}
    LINK_FLAGS "-L${LEPT_LIB_DIR}"
    PREFIX "lib"
    OUTPUT_NAME "tesseract_api")

SET_TARGET_PROPERTIES(tesseract cntraining mftraining unicharset_extractor wordlist2dawg combine_tessdata
    PROPERTIES VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION}
    LINK_FLAGS "-L${LEPT_LIB_DIR}")

INSTALL(FILES
  ${LANGUAGE_DATA}
  DESTINATION tessdata
)
# TODO:
# strip *.exe
# upx *.exe
# install header files
#install(FILES ${tesseract_api_LIB_HEADERS} DESTINATION include)

#install(TARGETS tesseract_main ${INSTALL_TARGETS_DEFAULT_ARGS})

########### install files ###############

#install(FILES  control.h cube_reco_context.h  )
