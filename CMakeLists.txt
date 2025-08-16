cmake_minimum_required(VERSION 3.16)

project(Noor VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Sadece Qt6'yı aramasını sağlıyoruz.
find_package(Qt6 REQUIRED COMPONENTS Widgets LinguistTools)

# --- Kaynak Dosyalarını Otomatik Bulma ---

# Ana C++ kaynak dosyasını buluyoruz.
file(GLOB MAIN_SRC "src/main.cpp")

# Arayüz (.h, .cpp, .ui) dosyalarını buluyoruz.
file(GLOB UI_HEADERS "src/ui/views/*.h")
file(GLOB UI_SOURCES "src/ui/views/*.cpp")
file(GLOB UI_FORMS "src/ui/views/*.ui")

# SQLite kütüphanesini projeye ekliyoruz.
file(GLOB SQLITE_SRC "libs/sqlite/sqlite3.c")
file(GLOB SQLITE_HEADERS "libs/sqlite/sqlite3.h")

# Tüm bulunan dosyaları tek bir proje kaynak listesinde birleştiriyoruz.
set(PROJECT_SOURCES
    ${MAIN_SRC}
    ${UI_HEADERS}
    ${UI_SOURCES}
    ${UI_FORMS}
    ${SQLITE_SRC}       # SQLite kaynak dosyasını ekledik
    ${SQLITE_HEADERS}   # SQLite başlık dosyasını ekledik
)

qt_add_executable(Noor
    MANUAL_FINALIZATION
    ${PROJECT_SOURCES}
)

# Gerekli Qt kütüphanelerini projemize bağlıyoruz.
target_link_libraries(Noor PRIVATE Qt6::Widgets)

# Projemizin SQLite başlık dosyalarını bulabilmesi için yolu belirtiyoruz.
target_include_directories(Noor PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/libs/sqlite")
target_include_directories(Noor PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/src")

# Dosyaları Qt Creator'da daha düzenli görünmeleri için grupluyoruz.
source_group("Source Files" FILES ${MAIN_SRC} ${UI_SOURCES})
source_group("Header Files" FILES ${UI_HEADERS})
source_group("Form Files" FILES ${UI_FORMS})
source_group("External Libs/SQLite" FILES ${SQLITE_SRC} ${SQLITE_HEADERS})

# Standart kurulum ve platform ayarları.
include(GNUInstallDirs)
install(TARGETS Noor
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

qt_finalize_executable(Noor)
