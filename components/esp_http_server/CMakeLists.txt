set(priv_req lwip esp_timer)
set(priv_inc_dir "src/util" "src/port/esp32")
set(requires http_parser esp_event)

idf_component_register(SRCS "src/httpd_main.c"
                            "src/httpd_parse.c"
                            "src/httpd_sess.c"
                            "src/httpd_txrx.c"
                            "src/httpd_uri.c"
                            "src/httpd_ws.c"
                            "src/util/ctrl_sock.c"
                    INCLUDE_DIRS "include"
                    PRIV_INCLUDE_DIRS ${priv_inc_dir}
                    REQUIRES ${requires}
                    PRIV_REQUIRES ${priv_req})

# WebSocket support hashes the handshake key with mbedtls; plain HTTP does not need it.
idf_build_get_property(build_components BUILD_COMPONENTS)
if(mbedtls IN_LIST build_components)
    idf_component_optional_requires(PRIVATE mbedtls)
elseif(CONFIG_HTTPD_WS_SUPPORT)
    message(FATAL_ERROR "esp_http_server: CONFIG_HTTPD_WS_SUPPORT needs the mbedtls component")
endif()
