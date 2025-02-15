set(header_location ${Livestatus_SOURCE_DIR}/include/livestatus)
set(livestatus_headers
    ${header_location}/Aggregator.h
    ${header_location}/AndingFilter.h
    ${header_location}/AttributeBitmaskColumn.h
    ${header_location}/AttributeListColumn.h
    ${header_location}/Attributes.h
    ${header_location}/Average.h
    ${header_location}/BitMask.h
    ${header_location}/BlobColumn.h
    ${header_location}/ChronoUtils.h
    ${header_location}/ColumnFilter.h
    ${header_location}/Column.h
    ${header_location}/CommentRenderer.h
    ${header_location}/CountAggregator.h
    ${header_location}/CrashReport.h
    ${header_location}/data_encoding.h
    ${header_location}/DictColumn.h
    ${header_location}/DictFilter.h
    ${header_location}/DoubleAggregator.h
    ${header_location}/DoubleColumn.h
    ${header_location}/DoubleFilter.h
    ${header_location}/DowntimeRenderer.h
    ${header_location}/DynamicColumn.h
    ${header_location}/DynamicEventConsoleReplicationColumn.h
    ${header_location}/DynamicFileColumn.h
    ${header_location}/EventConsoleConnection.h
    ${header_location}/FileSystemHelper.h
    ${header_location}/Filter.h
    ${header_location}/global_counters.h
    ${header_location}/HostServiceState.h
    ${header_location}/InputBuffer.h
    ${header_location}/IntAggregator.h
    ${header_location}/IntColumn.h
    ${header_location}/Interface.h
    ${header_location}/IntFilter.h
    ${header_location}/ListColumn.h
    ${header_location}/ListFilter.h
    ${header_location}/LogCache.h
    ${header_location}/LogEntry.h
    ${header_location}/Logfile.h
    ${header_location}/Logger.h
    ${header_location}/LogwatchList.h
    ${header_location}/MapUtils.h
    ${header_location}/Metric.h
    ${header_location}/mk_inventory.h
    ${header_location}/mk_logwatch.h
    ${header_location}/MonitoringCore.h
    ${header_location}/NullColumn.h
    ${header_location}/opids.h
    ${header_location}/OringFilter.h
    ${header_location}/OStreamStateSaver.h
    ${header_location}/OutputBuffer.h
    ${header_location}/overload.h
    ${header_location}/PerfdataAggregator.h
    ${header_location}/PnpUtils.h
    ${header_location}/Poller.h
    ${header_location}/POSIXUtils.h
    ${header_location}/Query.h
    ${header_location}/Queue.h
    ${header_location}/RegExp.h
    ${header_location}/RendererBrokenCSV.h
    ${header_location}/RendererCSV.h
    ${header_location}/Renderer.h
    ${header_location}/RendererJSON.h
    ${header_location}/RendererPython3.h
    ${header_location}/Row.h
    ${header_location}/ServiceGroupMembersColumn.h
    ${header_location}/ServiceListRenderer.h
    ${header_location}/StatsColumn.h
    ${header_location}/StringColumn.h
    ${header_location}/StringFilter.h
    ${header_location}/StringUtils.h
    ${header_location}/strutil.h
    ${header_location}/TableColumns.h
    ${header_location}/TableCommands.h
    ${header_location}/TableContactGroups.h
    ${header_location}/TableContacts.h
    ${header_location}/TableCrashReports.h
    ${header_location}/TableEventConsole.h
    ${header_location}/TableEventConsoleReplication.h
    ${header_location}/TableEventConsoleRules.h
    ${header_location}/TableEventConsoleStatus.h
    ${header_location}/Table.h
    ${header_location}/TableLabels.h
    ${header_location}/TableStatus.h
    ${header_location}/TableTimeperiods.h
    ${header_location}/TimeAggregator.h
    ${header_location}/TimeColumn.h
    ${header_location}/TimeFilter.h
    ${header_location}/Triggers.h
    ${header_location}/User.h)
set(livestatus_sources
    AndingFilter.cc
    AttributeListColumn.cc
    Average.cc
    Column.cc
    ColumnFilter.cc
    CommentRenderer.cc
    CountAggregator.cc
    CrashReport.cc
    DictFilter.cc
    DoubleFilter.cc
    DowntimeRenderer.cc
    DynamicEventConsoleReplicationColumn.cc
    EventConsoleConnection.cc
    FileSystemHelper.cc
    Filter.cc
    global_counters.cc
    HostServiceState.cc
    InputBuffer.cc
    IntFilter.cc
    ListFilter.cc
    LogCache.cc
    LogEntry.cc
    Logfile.cc
    Logger.cc
    LogwatchList.cc
    Metric.cc
    mk_inventory.cc
    mk_logwatch.cc
    MonitoringCore.cc
    NullColumn.cc
    opids.cc
    OringFilter.cc
    OutputBuffer.cc
    PerfdataAggregator.cc
    PnpUtils.cc
    POSIXUtils.cc
    Query.cc
    RegExp.cc
    RendererBrokenCSV.cc
    Renderer.cc
    RendererCSV.cc
    RendererJSON.cc
    RendererPython3.cc
    ServiceGroupMembersColumn.cc
    ServiceListRenderer.cc
    StatsColumn.cc
    StringFilter.cc
    StringUtils.cc
    strutil.cc
    Table.cc
    TableColumns.cc
    TableCommands.cc
    TableContactGroups.cc
    TableContacts.cc
    TableCrashReports.cc
    TableEventConsole.cc
    TableEventConsoleReplication.cc
    TableEventConsoleRules.cc
    TableEventConsoleStatus.cc
    TableLabels.cc
    TableStatus.cc
    TableTimeperiods.cc
    TimeFilter.cc
    Triggers.cc
    User.cc)

add_library(livestatus SHARED ${livestatus_headers} ${livestatus_sources})

include(FetchContent)
# Forbids option redefine in subproject: in the case to prevent re2::testing
# build
if(POLICY CMP0777)
  cmake_policy(SET CMP0777 NEW)
endif()

FetchContent_Declare(
  asio-omd
  URL ${root_repo_dir}/third_party/asio/asio-1.24.0-patched.tar.gz
  URL_HASH
    SHA256=09b9fe5c670c7bd47c7ee957cd9c184b4c8f0620d5b08b38ce837a24df971bca
  DOWNLOAD_EXTRACT_TIMESTAMP no)
FetchContent_MakeAvailable(asio-omd)
FetchContent_GetProperties(
  asio-omd
  SOURCE_DIR src_dir
  BINARY_DIR bin_dir
  POPULATED done_var)
set(asio_ext_include ${src_dir}/include)

FetchContent_Declare(
  re2 URL ${root_repo_dir}/third_party/re2/re2-2022-12-01.tar.gz
          DOWNLOAD_EXTRACT_TIMESTAMP no)

# We disable build re2::testing in the recommended manner by re2 because of
# warnings and time. Approach is equal to `cmake ... -DRE2_BUILD_TESTING="OFF"`
option(RE2_BUILD_TESTING "disable testing for RE2" OFF)

FetchContent_MakeAvailable(re2)
# Those pesky c-Wno-unused-parameter will be removed with 'next' re2 iteration.
target_compile_options(re2 PRIVATE -Wno-unused-parameter -DRE2_ON_VALGRIND -O3
                                   -g -fPIC)
FetchContent_GetProperties(re2 SOURCE_DIR re2_src_dir)
set(re2_ext_include ${re2_src_dir})

target_include_directories(livestatus PUBLIC ../include ${asio_ext_include}
                                             ${re2_ext_include})
target_link_libraries(
  livestatus
  PRIVATE pthread
  PUBLIC re2::re2)
target_compile_features(livestatus PUBLIC cxx_std_17)
set_target_properties(livestatus PROPERTIES VERSION ${PROJECT_VERSION}
                                            SOVERSION ${PROJECT_VERSION_MAJOR})

# Black Magic for IDE:
source_group(
  TREE "${PROJECT_SOURCE_DIR}/include"
  PREFIX "Header Files"
  FILES ${HEADER_LIST})
