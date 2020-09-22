cmake_minimum_required(VERSION 3.8)
project(redis)

set(CMAKE_C_STANDARD 99)

include_directories(deps/geohash-int)
include_directories(deps/hiredis)
include_directories(deps/linenoise)
include_directories(deps/lua/src)
include_directories(deps/jemalloc/include)
# -----lua--------------------
set(LUA_FILES
        deps/lua/src/lua.c
        deps/lua/src/lua_cmsgpack.c
        deps/lua/src/lbaselib.c
        deps/lua/src/lstrlib.c
        deps/lua/src/fpconv.c
        deps/lua/src/lmathlib.c
        deps/lua/src/lcode.c
        deps/lua/src/llex.c
        deps/lua/src/liolib.c
        deps/lua/src/lvm.c
        deps/lua/src/ltm.c
        deps/lua/src/ldump.c
        deps/lua/src/lopcodes.c
        deps/lua/src/lgc.c
        deps/lua/src/lstate.c
        deps/lua/src/lstring.c
        deps/lua/src/lmem.c
        deps/lua/src/ldblib.c
        deps/lua/src/lparser.c
        deps/lua/src/lobject.c
        deps/lua/src/print.c
        deps/lua/src/luac
        deps/lua/src/ldo.c
        deps/lua/src/loslib.c
        deps/lua/src/lua_struct.c
        deps/lua/src/lua_cjson.c
        deps/lua/src/lundump.c
        deps/lua/src/strbuf.c
        deps/lua/src/ltablib.c
        deps/lua/src/luac.c
        deps/lua/src/loadlib.c
        deps/lua/src/lzio.c
        deps/lua/src/linit.c
        deps/lua/src/lua_bit.c
        deps/lua/src/lapi.c
        deps/lua/src/lauxlib.c
        deps/lua/src/lfunc.c
        deps/lua/src/ltable.c
        deps/lua/src/ldebug.c
        )
add_library(lua ${LUA_FILES})
target_link_libraries(lua m)

add_library(linenoise deps/linenoise/linenoise.c)
add_library(sds src/sds.c)
add_library(anet src/anet.c)
add_library(release src/release.c)
add_library(ae src/ae.c)
add_library(zmalloc src/zmalloc.c)
add_library(crc64 src/crc64.c)
add_library(geohash-int deps/geohash-int/geohash.c
        deps/geohash-int/geohash_helper.c)
add_library(hiredis deps/hiredis/hiredis.c
        deps/hiredis/dict.c
        deps/hiredis/net.c
        deps/hiredis/sds.c
        deps/hiredis/async.c)

# -------------client--------------------------
add_executable(redis-cli src/redis-cli.c)
target_link_libraries(redis-cli sds anet release hiredis linenoise m ae crc64 zmalloc)

# -----------server----------------------
set(SERVER_FILES
        src/server.c
        src/zipmap.c
        src/config.c
        src/crc16.c
        src/endianconv.c
        src/redis-check-rdb.c
        src/bio.c
        src/geo.c
        src/sentinel.c
        src/bitops.c
        src/ziplist.c
        src/quicklist.c
        src/t_string.c
        src/adlist.c
        src/slowlog.c
        src/replication.c
        src/rand.c
        src/scripting.c
        src/pqsort.c
        src/debug.c
        src/lzf_d.c
        src/object.c
        src/aof.c
        src/networking.c
        src/sort.c
        src/blocked.c
        src/db.c
        src/pubsub.c
        src/latency.c
        src/t_zset.c
        src/hyperloglog.c
        src/setproctitle.c
        src/notify.c
        src/util.c
        src/sha1.c
        src/t_list.c
        src/intset.c
        src/dict.c
        src/cluster.c
        src/memtest.c
        src/rdb.c
        src/syncio.c
        src/t_set.c
        src/t_hash.c
        src/multi.c
        src/sparkline.c
        src/lzf_c.c
        src/rio.c)
#
#IF (CMAKE_SYSTEM_NAME MATCHES "Linux")
#    list(APPEND SERVER_FILES src/ae_epoll.c)
#ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
#    list(APPEND SERVER_FILES src/ae_select.c)
#ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Darwin")
#    list(APPEND SERVER_FILES src/ae_kqueue.c)
#ELSE ()
#    list(APPEND SERVER_FILES src/ae_evport.c)
#ENDIF (CMAKE_SYSTEM_NAME MATCHES "Linux")

add_executable(redis-server ${SERVER_FILES})
add_executable(redis-sentinel ${SERVER_FILES})
target_link_libraries(redis-server dl lua geohash-int hiredis pthread sds release ae anet crc64 zmalloc)
target_link_libraries(redis-sentinel dl lua geohash-int hiredis pthread sds release ae anet crc64 zmalloc)
