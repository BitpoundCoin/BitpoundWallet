cmake_minimum_required(VERSION 2.8)

include(CryptoNoteWallet.cmake)

project(${CN_PROJECT_NAME})

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(CRYPTONOTE_LIB bitpound)

include_directories(${CMAKE_BINARY_DIR}
    src
    bitpound/external
    bitpound/include
    bitpound/src)

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)

set(Boost_USE_STATIC_LIBS ON)
if(WIN32)
  set(Boost_USE_STATIC_RUNTIME OFF)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.55 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)
if ((${Boost_MAJOR_VERSION} EQUAL 1) AND (${Boost_MINOR_VERSION} EQUAL 54))
	message(SEND_ERROR "Boost version 1.54 is unsupported, more details are available here http://goo.gl/RrCFmA")
endif ()

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

set(VERSION "")
configure_file("bitpound/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")

set(CMAKE_AUTOMOC ON)

set(CRYPTONOTE_SOURCES
  bitpound/external/miniupnpc/connecthostport.c
  bitpound/external/miniupnpc/igd_desc_parse.c
  bitpound/external/miniupnpc/minisoap.c
  bitpound/external/miniupnpc/miniupnpc.c
  bitpound/external/miniupnpc/miniwget.c
  bitpound/external/miniupnpc/minixml.c
  bitpound/external/miniupnpc/portlistingparse.c
  bitpound/external/miniupnpc/receivedata.c
  bitpound/external/miniupnpc/upnpcommands.c
  bitpound/external/miniupnpc/upnpreplyparse.c
  bitpound/src/Common/Base58.cpp
  bitpound/src/Common/CommandLine.cpp
  bitpound/src/Common/Util.cpp
  bitpound/src/Common/StringTools.cpp
  bitpound/src/Common/JsonValue.cpp
  bitpound/src/Common/ConsoleTools.cpp
  bitpound/src/Common/MemoryInputStream.cpp
  bitpound/src/Common/PathTools.cpp
  bitpound/src/Common/StdInputStream.cpp
  bitpound/src/Common/StdOutputStream.cpp
  bitpound/src/Common/StreamTools.cpp
  bitpound/src/Common/StringOutputStream.cpp
  bitpound/src/Common/StringView.cpp
  bitpound/src/Common/VectorOutputStream.cpp
  bitpound/src/crypto/blake256.c
  bitpound/src/crypto/chacha8.c
  bitpound/src/crypto/crypto-ops-data.c
  bitpound/src/crypto/crypto-ops.c
  bitpound/src/crypto/crypto.cpp
  bitpound/src/crypto/groestl.c
  bitpound/src/crypto/hash-extra-blake.c
  bitpound/src/crypto/hash-extra-groestl.c
  bitpound/src/crypto/hash-extra-jh.c
  bitpound/src/crypto/hash-extra-skein.c
  bitpound/src/crypto/hash.c
  bitpound/src/crypto/jh.c
  bitpound/src/crypto/keccak.c
  bitpound/src/crypto/oaes_lib.c
  bitpound/src/crypto/random.c
  bitpound/src/crypto/skein.c
  bitpound/src/crypto/slow-hash.c
  bitpound/src/crypto/slow-hash.cpp
  bitpound/src/crypto/tree-hash.c
  bitpound/src/CryptoNoteCore/BlockchainIndices.cpp
  bitpound/src/CryptoNoteCore/BlockchainMessages.cpp
  bitpound/src/CryptoNoteCore/BlockIndex.cpp
  bitpound/src/CryptoNoteCore/CoreConfig.cpp
  bitpound/src/CryptoNoteCore/CryptoNoteBasic.cpp
  bitpound/src/CryptoNoteCore/CryptoNoteTools.cpp
  bitpound/src/CryptoNoteCore/Currency.cpp
  bitpound/src/CryptoNoteCore/MinerConfig.cpp
  bitpound/src/CryptoNoteCore/Transaction.cpp
  bitpound/src/CryptoNoteCore/Account.cpp
  bitpound/src/CryptoNoteCore/Blockchain.cpp
  bitpound/src/CryptoNoteCore/Checkpoints.cpp
  bitpound/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  bitpound/src/CryptoNoteCore/Core.cpp
  bitpound/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  bitpound/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  bitpound/src/CryptoNoteCore/Difficulty.cpp
  bitpound/src/CryptoNoteCore/IBlock.cpp
  bitpound/src/CryptoNoteCore/Miner.cpp
  bitpound/src/CryptoNoteCore/TransactionExtra.cpp
  bitpound/src/CryptoNoteCore/TransactionPool.cpp
  bitpound/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  bitpound/src/CryptoNoteCore/TransactionUtils.cpp
  bitpound/src/InProcessNode/InProcessNode.cpp
  bitpound/src/InProcessNode/InProcessNodeErrors.cpp
  bitpound/src/NodeRpcProxy/NodeErrors.cpp
  bitpound/src/NodeRpcProxy/NodeRpcProxy.cpp
  bitpound/src/P2p/NetNodeConfig.cpp
  bitpound/src/Serialization/BinaryInputStreamSerializer.cpp
  bitpound/src/Serialization/BinaryOutputStreamSerializer.cpp
  bitpound/src/Serialization/JsonInputValueSerializer.cpp
  bitpound/src/Serialization/JsonOutputStreamSerializer.cpp
  bitpound/src/Serialization/KVBinaryInputStreamSerializer.cpp
  bitpound/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  bitpound/src/Serialization/SerializationOverloads.cpp
  bitpound/src/Transfers/BlockchainSynchronizer.cpp
  bitpound/src/Transfers/SynchronizationState.cpp
  bitpound/src/Transfers/TransfersConsumer.cpp
  bitpound/src/Transfers/TransfersContainer.cpp
  bitpound/src/Transfers/TransfersSubscription.cpp
  bitpound/src/Transfers/TransfersSynchronizer.cpp
  bitpound/src/Wallet/LegacyKeysImporter.cpp
  bitpound/src/Wallet/WalletAsyncContextCounter.cpp
  bitpound/src/Wallet/WalletErrors.cpp
  bitpound/src/WalletLegacy/KeysStorage.cpp
  bitpound/src/WalletLegacy/WalletLegacy.cpp
  bitpound/src/WalletLegacy/WalletHelper.cpp
  bitpound/src/WalletLegacy/WalletLegacySerializer.cpp
  bitpound/src/WalletLegacy/WalletLegacySerialization.cpp
  bitpound/src/WalletLegacy/WalletTransactionSender.cpp
  bitpound/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  bitpound/src/WalletLegacy/WalletUserTransactionsCache.cpp
  bitpound/src/System/ContextGroup.cpp
  bitpound/src/System/Event.cpp
  bitpound/src/System/EventLock.cpp
  bitpound/src/System/InterruptedException.cpp
  bitpound/src/System/Ipv4Address.cpp
  bitpound/src/System/TcpStream.cpp
  bitpound/src/HTTP/HttpRequest.cpp
  bitpound/src/HTTP/HttpParser.cpp
  bitpound/src/HTTP/HttpParserErrorCodes.cpp
  bitpound/src/HTTP/HttpResponse.cpp
  bitpound/src/Rpc/HttpClient.cpp
  bitpound/src/Rpc/JsonRpc.cpp
  bitpound/src/P2p/NetNode.cpp
  bitpound/src/P2p/LevinProtocol.cpp
  bitpound/src/P2p/NetNodeConfig.cpp
  bitpound/src/P2p/PeerListManager.cpp
  bitpound/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  bitpound/src/Logging/ILogger.cpp
  bitpound/src/Logging/LoggerGroup.cpp
  bitpound/src/Logging/CommonLogger.cpp
  bitpound/src/Logging/LoggerManager.cpp
  bitpound/src/Logging/FileLogger.cpp
  bitpound/src/Logging/StreamLogger.cpp
  bitpound/src/Logging/ConsoleLogger.cpp
  bitpound/src/Logging/LoggerMessage.cpp
  bitpound/src/Logging/LoggerRef.cpp
  bitpound/src/BlockchainExplorer/BlockchainExplorer.cpp
  bitpound/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  bitpound/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(bitpound/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} bitpound/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "Bitpound GUI wallet")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME BitpoundWallet)
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "$${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE cryptonote.icns)
    set(APPLICATION_ICON src/images/cryptonote.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})
	
    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(bitpound/src/Platform/${PLATFORM_DIR})
file(GLOB PLATFORM_SOURCES bitpound/src/Platform/${PLATFORM_DIR}/System/*)
set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} ${PLATFORM_SOURCES})

add_library(${CRYPTONOTE_LIB} STATIC ${CRYPTONOTE_SOURCES})
set_target_properties(${CRYPTONOTE_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES})

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${CRYPTONOTE_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui)
