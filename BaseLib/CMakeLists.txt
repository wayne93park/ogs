GET_SOURCE_FILES(SOURCES)
APPEND_SOURCE_FILES(SOURCES IO)

APPEND_SOURCE_FILES(SOURCES IO/XmlIO)

if(Qt5XmlPatterns_FOUND)
    APPEND_SOURCE_FILES(SOURCES IO/XmlIO/Qt)
endif()

list(APPEND SOURCES "${CMAKE_CURRENT_BINARY_DIR}/BuildInfo.cpp" BuildInfo.h)

# Create the library
add_library(BaseLib ${SOURCES})
if(BUILD_SHARED_LIBS)
    install(TARGETS BaseLib LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()

include(GenerateExportHeader)
generate_export_header(BaseLib)
target_include_directories(BaseLib PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

target_link_libraries(BaseLib PUBLIC logog)

if(MSVC)
    target_link_libraries(BaseLib PUBLIC WinMM) # needed for timeGetTime
endif()

if(Qt5XmlPatterns_FOUND)
    target_link_libraries(BaseLib PUBLIC Qt5::Xml Qt5::XmlPatterns)
    if(WIN32 AND CMAKE_CROSSCOMPILING AND OPENSSL_FOUND)
        target_link_libraries(BaseLib PUBLIC Qt5::Network ${OPENSSL_LIBRARIES} ws2_32)
    endif()
endif()

if(OGS_USE_PCH)
    cotire(BaseLib)
endif()
