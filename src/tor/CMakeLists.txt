## check for dependencies, using built-in cmake files to help us find the libs
## these are in /usr/share/cmake/Modules/Findxxx.cmake
find_package(ZLIB REQUIRED)

## other libs dont have a Findxxx.cmake, so we check on our own (./cmake)
find_package(RT REQUIRED)
find_package(DL REQUIRED)
find_package(M REQUIRED)
find_package(OPENSSL REQUIRED)
find_package(EVENT2 REQUIRED)
find_package(GLIB REQUIRED)

#find_package(TOR REQUIRED)
get_filename_component(TOR_ROOT_DIR "${CMAKE_BINARY_DIR}/../tor" ABSOLUTE)
#set(TOR_ROOT_DIR ${CMAKE_BINARY_DIR}/tor)
#set(TOR_ROOT ${TORPATH})
include_directories(${TOR_ROOT_DIR})
include_directories(${TOR_ROOT_DIR}/src)
include_directories(${TOR_ROOT_DIR}/src/common)
include_directories(${TOR_ROOT_DIR}/src/or)
include_directories(${TOR_ROOT_DIR}/src/ext)
include_directories(${TOR_ROOT_DIR}/src/ext/trunnel)
include_directories(${TOR_ROOT_DIR}/src/trunnel)

set(LIBQUIC_ROOT_DIR ${CMAKE_BINARY_DIR}/../libquic)
include_directories(${LIBQUIC_ROOT_DIR}/src ${LIBQUIC_ROOT_DIR}/src/third_party/modp_b64 ${LIBQUIC_ROOT_DIR}/boringssl/include ${LIBQUIC_ROOT_DIR}/src/third_party/protobuf/src)

include_directories(AFTER ${RT_INCLUDES} ${DL_INCLUDES} ${M_INCLUDES} ${GLIB_INCLUDES} ${EVENT2_INCLUDES} ${OPENSSL_INCLUDES} ${TOR_INCLUDES})

## import shadow libs
#INCLUDE(${CMAKE_INSTALL_PREFIX}/share/shadow/shadow-externals.cmake)

set(TOR_VERSION "${TOR_VERSION_A}.${TOR_VERSION_B}.${TOR_VERSION_C}.${TOR_VERSION_D}")
MESSAGE(STATUS "Found Tor path = ${TOR_ROOT_DIR}")
MESSAGE(STATUS "Found Tor version = ${TOR_VERSION}")

## check env to see if we should allow interposition ion EVP_Cipher
if(SHADOW_ENABLE_EVPCIPHER STREQUAL ON)
    add_definitions(-DSHADOW_ENABLE_EVPCIPHER)
    message(STATUS "Added definition SHADOW_ENABLE_EVPCIPHER")
endif(SHADOW_ENABLE_EVPCIPHER STREQUAL ON)

## Version-specific defines to handle changes to Tor's functionality
# version >= 0.2.2.17, commit a2bb0bfdd5731ef3cebf6e14fa7bebb98d5f2d8f
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 2 AND ${TOR_VERSION_D} GREATER 16) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 2))
    add_definitions(-DSCALLION_NEWIDKEYNAME)
    message(STATUS "Added definition SCALLION_NEWIDKEYNAME")
endif()
# version >= 0.2.3.5
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 3 AND ${TOR_VERSION_D} GREATER 4) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 3))
    add_definitions(-DSCALLION_DOREFILLCALLBACKS)
	message(STATUS "Added definition SCALLION_DOREFILLCALLBACKS")
endif()
# version >= 0.2.3.10, commit 26e789fbfd4e5a7e2789c1577e8eb967de3eba86
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 3 AND ${TOR_VERSION_D} GREATER 9) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 3))
    add_definitions(-DSCALLION_NEWCRYPTODEFS)
    message(STATUS "Added definition SCALLION_NEWCRYPTODEFS")
endif()
# version >= 0.2.4.8
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 4 AND ${TOR_VERSION_D} GREATER 7) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 4))
    add_definitions(-DSCALLION_USEV2CPUWORKER)
	message(STATUS "Added definition SCALLION_USEV2CPUWORKER")
endif()
# version >= 0.2.4.10
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 4 AND ${TOR_VERSION_D} GREATER 9) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 4))
    add_definitions(-DSCALLION_USEV2CPUWORKERTIMING)
    message(STATUS "Added definition SCALLION_USEV2CPUWORKERTIMING")
endif()
# version >= 0.2.4.11
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 4 AND ${TOR_VERSION_D} GREATER 10) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 4))
    add_definitions(-DSCALLION_LOGVWITHSUFFIX)
    message(STATUS "Added definition SCALLION_LOGVWITHSUFFIX")
endif()
# version >= 0.2.5.2
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 5 AND ${TOR_VERSION_D} GREATER 1) OR 
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 5))
    add_definitions(-DSCALLION_NOV2DIR)
    message(STATUS "Added definition SCALLION_NOV2DIR")
endif()
# version >= 0.2.5.5, commit 39d4e67be8283b2a7141a7aa8342d30e27f47e6f
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 5 AND ${TOR_VERSION_D} GREATER 5) OR
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 5))
    add_definitions(-DSCALLION_MEMPOOLOPT)
    message(STATUS "Added definition SCALLION_MEMPOOLOPT")
endif()
# version >= 0.2.6.1, commit 39d4e67be8283b2a7141a7aa8342d30e27f47e6f
if((${TOR_VERSION_A} EQUAL 0 AND ${TOR_VERSION_B} EQUAL 2 AND ${TOR_VERSION_C} EQUAL 6 AND ${TOR_VERSION_D} GREATER 0) OR
    (${TOR_VERSION_A} GREATER -1 AND ${TOR_VERSION_B} GREATER 1 AND ${TOR_VERSION_C} GREATER 6))
    add_definitions(-DSTARTUP_Q_PARAM)
    message(STATUS "Added definition STARTUP_Q_PARAM")
endif()

## get tor source files, but ignore some of them
aux_source_directory(${TOR_ROOT_DIR}/src/common torcommon_sources)
list(REMOVE_ITEM torcommon_sources 
    ${TOR_ROOT_DIR}/src/common/sha256.c
    ${TOR_ROOT_DIR}/src/common/strlcat.c
    ${TOR_ROOT_DIR}/src/common/strlcpy.c
    ${TOR_ROOT_DIR}/src/common/compat_winthreads.c
)

aux_source_directory(${TOR_ROOT_DIR}/src/ext torext_sources)
list(REMOVE_ITEM torext_sources 
    ${TOR_ROOT_DIR}/src/ext/OpenBSD_malloc_Linux.c
    ${TOR_ROOT_DIR}/src/ext/strlcat.c
    ${TOR_ROOT_DIR}/src/ext/strlcpy.c
    ${TOR_ROOT_DIR}/src/ext/tinytest.c
    ${TOR_ROOT_DIR}/src/ext/tinytest_demo.c
)

aux_source_directory(${TOR_ROOT_DIR}/src/ext/curve25519_donna torextcurve_sources)
if(${CMAKE_HOST_SYSTEM_PROCESSOR} STREQUAL "x86_64")
    list(REMOVE_ITEM torextcurve_sources
	    ${TOR_ROOT_DIR}/src/ext/curve25519_donna/curve25519-donna.c
    )
else()
    list(REMOVE_ITEM torextcurve_sources
        ${TOR_ROOT_DIR}/src/ext/curve25519_donna/curve25519-donna-c64.c
    )
endif()

aux_source_directory(${TOR_ROOT_DIR}/src/ext/ed25519/ref10 torextedref_sources)

aux_source_directory(${TOR_ROOT_DIR}/src/ext/ed25519/donna torexteddon_sources)
list(REMOVE_ITEM torexteddon_sources 
     ${TOR_ROOT_DIR}/src/ext/ed25519/donna/ed25519.c
    ${TOR_ROOT_DIR}/src/ext/ed25519/donna/test.c
    ${TOR_ROOT_DIR}/src/ext/ed25519/donna/test-internals.c
)

aux_source_directory(${TOR_ROOT_DIR}/src/ext/trunnel torexttrunnel_sources)

aux_source_directory(${TOR_ROOT_DIR}/src/or toror_sources)
list(REMOVE_ITEM toror_sources 
    ${TOR_ROOT_DIR}/src/or/ntmain.c
    ${TOR_ROOT_DIR}/src/or/tor_main.c
)

aux_source_directory(${TOR_ROOT_DIR}/src/trunnel tortrunnel_sources)

aux_source_directory(${LIBQUIC_ROOT_DIR}/src/quux/server QUUX_DISPATCHER_SOURCES)

add_definitions(-DSHADOW=1 -DQUUX_LOG=1)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

## get tor header files -- we dont need this for now
#file(GLOB tor_headers ${TOR_ROOT_DIR}/*.h)
#file(GLOB toror_headers ${TOR_ROOT_DIR}/src/or/*.h)
#file(GLOB torcommon_headers ${TOR_ROOT_DIR}/src/common/*.h)
#list(APPEND tor_headers ${toror_headers} ${torcommon_headers})
#foreach(headerpath ${tor_headers})
#    get_filename_component(headername ${headerpath} NAME)
#    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -include ${headername}")
#endforeach(headerpath)

## tor needs these defined
add_definitions(-DLOCALSTATEDIR="/usr/local/var" -DSHARE_DATADIR="/usr/local/var" -DBINDIR="/usr/local/bin")
add_definitions(-DED25519_CUSTOMRANDOM -DED25519_SUFFIX=_donna)
remove_definitions(-DNDEBUG)

## disable warnings from tor code, add other options
add_cflags("-w -fPIC -fno-inline -fno-strict-aliasing -U_FORTIFY_SOURCE")

## create and install a shared library that can plug into shadow
add_shadow_plugin(shadow-plugin-tor 
    shadowtor-main.c ${toror_sources} ${torcommon_sources} 
    ${torext_sources} ${torextcurve_sources} ${torextedref_sources} ${torexteddon_sources}
    ${tortrunnel_sources} ${torexttrunnel_sources}


    ${LIBQUIC_ROOT_DIR}/src/quux/api.cc
    ${QUUX_DISPATCHER_SOURCES}


    ${LIBQUIC_ROOT_DIR}/src/net/quic/proto/cached_network_parameters.pb.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/proto/source_address_token.pb.cc


    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_protocol.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_packet_generator.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_flow_controller.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_connection_stats.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_data_writer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_data_reader.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_session.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_spdy_session.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_spdy_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/iovector.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_stream_sequencer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_framer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_sent_packet_manager.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_time.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_headers_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_connection.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_types.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_received_packet_manager.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_write_blocked_list.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_crypto_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_socket_address_coder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_utils.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_header_list.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/spdy_utils.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/aead_base_decrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/aead_base_encrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/chacha20_poly1305_decrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/chacha20_poly1305_encrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/aes_128_gcm_12_decrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/aes_128_gcm_12_encrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/p256_key_exchange.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_compressed_certs_cache.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_crypto_server_config.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_handshake_message.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/cert_compressor.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_secret_boxer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/curve25519_key_exchange.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_decrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/null_decrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_server_config_protobuf.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/scoped_evp_aead_ctx.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_random.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/common_cert_set.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/null_encrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_framer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_handshake.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/channel_id.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/strike_register.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/crypto_utils.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/local_strike_register_client.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_encrypter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/quic_crypto_client_config.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/crypto/proof_source.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_clock.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_alarm.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_sent_entropy_manager.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/pacing_sender.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/general_loss_algorithm.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/send_algorithm_interface.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/cubic.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/cubic_bytes.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/prr_sender.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/hybrid_slow_start.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/rtt_stats.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/tcp_cubic_sender_base.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/tcp_cubic_sender_bytes.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/congestion_control/tcp_cubic_sender_packets.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_config.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_crypto_server_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_flags.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/reliable_quic_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_bandwidth.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_unacked_packet_map.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_packet_creator.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_sustained_bandwidth_recorder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_client_session_base.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_client_promised_info.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_client_push_promise_index.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_crypto_client_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_server_id.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_simple_buffer_allocator.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_stream_sequencer_buffer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/quic/quic_server_session_base.cc

    ${LIBQUIC_ROOT_DIR}/src/net/base/int128.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/escape.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/net_errors.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/ip_address.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/ip_endpoint.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/host_port_pair.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/address_family.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/port_util.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/url_util2.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/parse_number.cc
    ${LIBQUIC_ROOT_DIR}/src/net/base/arena.cc

    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_frame_reader.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_frame_builder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_framer.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_header_block.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_headers_block_parser.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_framer_decoder_adapter.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_protocol.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_flags.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_alt_svc_wire_format.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_pinnable_buffer_piece.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/spdy_prefixed_buffer_reader.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_constants.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_decoder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_encoder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_entry.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_header_table.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_huffman_table.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_huffman_decoder.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_input_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_output_stream.cc
    ${LIBQUIC_ROOT_DIR}/src/net/spdy/hpack/hpack_static_table.cc


    ${LIBQUIC_ROOT_DIR}/src/base/threading/platform_thread_linux.cc


    ${LIBQUIC_ROOT_DIR}/src/base/logging.cc
    ${LIBQUIC_ROOT_DIR}/src/base/posix/safe_strerror.cc
    ${LIBQUIC_ROOT_DIR}/src/base/memory/weak_ptr.cc
    ${LIBQUIC_ROOT_DIR}/src/base/memory/singleton.cc
    ${LIBQUIC_ROOT_DIR}/src/base/memory/ref_counted.cc
    ${LIBQUIC_ROOT_DIR}/src/base/memory/aligned_memory.cc
    ${LIBQUIC_ROOT_DIR}/src/base/rand_util_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/trace_event/heap_profiler_allocation_context_tracker.cc
    ${LIBQUIC_ROOT_DIR}/src/base/trace_event/heap_profiler_allocation_context.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_id_name_manager.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/platform_thread_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/platform_thread_internal_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_restrictions.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_local_storage_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_local_storage.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_collision_warner.cc
    ${LIBQUIC_ROOT_DIR}/src/base/threading/thread_local_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/time/time_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/time/time.cc
    ${LIBQUIC_ROOT_DIR}/src/base/synchronization/lock.cc
    ${LIBQUIC_ROOT_DIR}/src/base/synchronization/lock_impl_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/sys_string_conversions_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string_piece.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string_util.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string_number_conversions.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string_util_constants.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/stringprintf.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string_split.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/string16.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/utf_string_conversion_utils.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/utf_string_conversions.cc
    ${LIBQUIC_ROOT_DIR}/src/base/strings/utf_offset_string_conversions.cc
    ${LIBQUIC_ROOT_DIR}/src/base/pickle.cc
    ${LIBQUIC_ROOT_DIR}/src/base/debug/alias.cc
    ${LIBQUIC_ROOT_DIR}/src/base/debug/stack_trace.cc
    ${LIBQUIC_ROOT_DIR}/src/base/debug/debugger.cc
    ${LIBQUIC_ROOT_DIR}/src/base/callback_helpers.cc
    ${LIBQUIC_ROOT_DIR}/src/base/json/json_string_value_serializer.cc
    ${LIBQUIC_ROOT_DIR}/src/base/json/json_parser.cc
    ${LIBQUIC_ROOT_DIR}/src/base/json/string_escape.cc
    ${LIBQUIC_ROOT_DIR}/src/base/json/json_writer.cc
    ${LIBQUIC_ROOT_DIR}/src/base/json/json_reader.cc
    ${LIBQUIC_ROOT_DIR}/src/base/lazy_instance.cc
    ${LIBQUIC_ROOT_DIR}/src/base/callback_internal.cc
    ${LIBQUIC_ROOT_DIR}/src/base/base_switches.cc
    ${LIBQUIC_ROOT_DIR}/src/base/values.cc
    ${LIBQUIC_ROOT_DIR}/src/base/at_exit.cc
    ${LIBQUIC_ROOT_DIR}/src/base/process/process_handle_posix.cc
    ${LIBQUIC_ROOT_DIR}/src/base/bind_helpers.cc
    ${LIBQUIC_ROOT_DIR}/src/base/vlog.cc
    ${LIBQUIC_ROOT_DIR}/src/base/location.cc
    ${LIBQUIC_ROOT_DIR}/src/base/base64.cc
    ${LIBQUIC_ROOT_DIR}/src/base/md5.cc
    ${LIBQUIC_ROOT_DIR}/src/base/hash.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/statistics_recorder.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/sample_map.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/sample_vector.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/histogram_base.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/histogram.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/histogram_samples.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/sparse_histogram.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/bucket_ranges.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/metrics_hashes.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/persistent_memory_allocator.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/persistent_sample_map.cc
    ${LIBQUIC_ROOT_DIR}/src/base/metrics/persistent_histogram_allocator.cc
    ${LIBQUIC_ROOT_DIR}/src/base/rand_util.cc
    ${LIBQUIC_ROOT_DIR}/src/base/files/file_path_constants.cc
    ${LIBQUIC_ROOT_DIR}/src/base/files/file_path.cc
    ${LIBQUIC_ROOT_DIR}/src/base/third_party/nspr/prtime.cc
    ${LIBQUIC_ROOT_DIR}/src/base/third_party/dmg_fp/dtoa.cc
    ${LIBQUIC_ROOT_DIR}/src/base/third_party/dmg_fp/g_fmt.cc
    ${LIBQUIC_ROOT_DIR}/src/base/third_party/icu/icu_utf.cc
    ${LIBQUIC_ROOT_DIR}/src/base/command_line.cc

    ${LIBQUIC_ROOT_DIR}/src/base/third_party/superfasthash/superfasthash.c


    ${LIBQUIC_ROOT_DIR}/src/crypto/curve25519.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/hkdf.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/hmac.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/openssl_util.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/symmetric_key.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/random.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/secure_util.cc
    ${LIBQUIC_ROOT_DIR}/src/crypto/secure_hash.cc


    ${LIBQUIC_ROOT_DIR}/src/url/url_constants.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_etc.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_filesystemurl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_fileurl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_host.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_internal.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_ip.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_mailtourl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_path.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_pathurl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_query.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_relative.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_stdstring.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_canon_stdurl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_util.cc
    ${LIBQUIC_ROOT_DIR}/src/url/url_parse_file.cc
    ${LIBQUIC_ROOT_DIR}/src/url/gurl.cc
    ${LIBQUIC_ROOT_DIR}/src/url/third_party/mozilla/url_parse.cc


    ${LIBQUIC_ROOT_DIR}/src/third_party/modp_b64/modp_b64.cc
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/gzwrite.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/crc32.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/zutil.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/deflate.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/simd_stub.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/infback.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/inffast.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/inflate.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/inftrees.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/compress.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/gzclose.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/adler32.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/gzlib.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/trees.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/gzread.c
    ${LIBQUIC_ROOT_DIR}/src/third_party/zlib/uncompr.c
)

add_dependencies(shadow-plugin-tor shadow-preload-tor)

target_link_libraries(shadow-plugin-tor shadow-preload-tor
    ${LIBQUIC_ROOT_DIR}/build/protobuf/libprotobuf.a
    ${LIBQUIC_ROOT_DIR}/build/boringssl/crypto/libbsslcrypto.a
    ${TOR_LIBRARIES} ${GLIB_LIBRARIES} ${EVENT2_LIBRARIES} ${OPENSSL_LIBRARIES} 
    ${ZLIB_LIBRARIES} ${M_LIBRARIES} ${RT_LIBRARIES} ${DL_LIBRARIES} -lpthread)

foreach(lib ${CMAKE_TOR_LIBRARIES})
    target_link_libraries(shadow-plugin-tor ${lib})
endforeach(lib ${CMAKE_TOR_LIBRARIES})

install(TARGETS shadow-plugin-tor DESTINATION plugins)
set_target_properties(shadow-plugin-tor PROPERTIES INSTALL_RPATH ${CMAKE_INSTALL_PREFIX}/lib INSTALL_RPATH_USE_LINK_PATH TRUE)

## preload library for intercepting functions
add_library(shadow-preload-tor SHARED shadowtor-preload.c)
target_link_libraries(shadow-preload-tor ${GLIB_LIBRARIES} ${EVENT2_LIBRARIES} ${OPENSSL_LIBRARIES} ${DL_LIBRARIES})
install(TARGETS shadow-preload-tor DESTINATION lib)

## install run script
install(PROGRAMS shadow-tor DESTINATION bin)

install(FILES ${TOR_ROOT_DIR}/src/config/geoip DESTINATION share/)
#install(DIRECTORY resource/ DESTINATION share PATTERN "config/*")
