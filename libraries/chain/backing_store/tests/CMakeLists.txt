add_executable( test_kv_rocksdb test_kv_rocksdb.cpp )
target_link_libraries( test_kv_rocksdb dcd_chain fc chainbase )
target_include_directories( test_kv_rocksdb PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include" "${CMAKE_CURRENT_SOURCE_DIR}/../include")
add_test(NAME test_kv_rocksdb COMMAND libraries/chain/backing_store/tests/test_kv_rocksdb WORKING_DIRECTORY ${CMAKE_BINARY_DIR})

add_executable( benchmark_kv benchmark_kv.cpp )
target_link_libraries( benchmark_kv dcd_chain fc chainbase Boost::program_options )
target_include_directories( benchmark_kv PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include" "${CMAKE_CURRENT_SOURCE_DIR}/../include")
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/benchmark_kv_batch.py DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/)
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/benchmark_kv_single.py DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/)
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/README DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/)
