проблема с xlnt
при скачивание останавливается на fetch
___
Очистите всё
```shell
Remove-Item -Recurse -Force "C:\Users\AAKrivov-vnkt\Desktop\vcpkg-master\buildtrees\xlnt" -ErrorAction SilentlyContinue
Remove-Item "C:\Users\AAKrivov-vnkt\Desktop\vcpkg-master\downloads\xlnt*.tar.gz" -ErrorAction SilentlyContinue
```
___
создаем копию папки xlnt-fixed и в ней редактируем файл
**portfile.cmake**
```
# Загрузка libstudxml с GitHub (исправленный URL)
vcpkg_download_distfile(LIBSTUDXML_ARCHIVE
    URLS "https://github.com/codesynthesis-com/libstudxml/archive/refs/tags/v1.1.0-b.10%2B2.tar.gz"
    FILENAME "libstudxml-1.1.0-b.10+2.tar.gz"
    SHA512 932CDA1596DAFF1D21BCD5769B81F6D2C9C3F9DC0CF5A1EE906D73EB8A5E3067E51D5B1382EB0A5270C5BAC2744C941CD5273486E168B11F5775CC6107A22B33
)

# Распаковка libstudxml
vcpkg_extract_source_archive_ex(
    OUT_SOURCE_PATH LIBSTUDXML_SOURCE_PATH
    ARCHIVE "${LIBSTUDXML_ARCHIVE}"
)

# ---------- ВАЖНО: ДОБАВЬТЕ ЭТО ----------
# Определяем путь к исходникам xlnt
set(SOURCE_PATH "${CURRENT_BUILDTREES_DIR}/src/v1.6.1-6a5c9e18ee.clean")
# ---------- КОНЕЦ ДОБАВЛЕНИЯ ----------

# Копирование libstudxml в нужное место для xlnt
file(COPY "${LIBSTUDXML_SOURCE_PATH}/" DESTINATION "${SOURCE_PATH}/third-party/libstudxml")

# Оригинальная часть порта xlnt (оставляем без изменений)
if(VCPKG_LIBRARY_LINKAGE STREQUAL dynamic)
    set(STATIC OFF)
else()
    set(STATIC ON)
endif()

# УДАЛИТЕ эту дублирующую строку (строка 20 в вашем файле):
# file(COPY "${LIBSTUDXML_SOURCE_PATH}/" DESTINATION "${SOURCE_PATH}/third-party/libstudxml")

vcpkg_cmake_configure(
    SOURCE_PATH "${SOURCE_PATH}"
    DISABLE_PARALLEL_CONFIGURE
    OPTIONS -DTESTS=OFF -DSAMPLES=OFF -DBENCHMARKS=OFF -DSTATIC=${STATIC}
)
vcpkg_cmake_install()

vcpkg_cmake_config_fixup(CONFIG_PATH lib/cmake/xlnt)

file(REMOVE_RECURSE "${CURRENT_PACKAGES_DIR}/debug/include")
file(REMOVE_RECURSE "${CURRENT_PACKAGES_DIR}/debug/share")
file(REMOVE_RECURSE "${CURRENT_PACKAGES_DIR}/share/man")
vcpkg_install_copyright(FILE_LIST "${SOURCE_PATH}/LICENSE.md")
file(INSTALL "${CMAKE_CURRENT_LIST_DIR}/usage" DESTINATION "${CURRENT_PACKAGES_DIR}/share/${PORT}")

vcpkg_copy_pdbs()

vcpkg_fixup_pkgconfig()
```
___
причем файлы новые ненужно скачивать когда первый раз скачает сам vcpkg просто настроить файл и вызвать конфиг все заработало.
___
Выполняем установку
```shell
.\vcpkg install xlnt:x64-windows --overlay-ports=ports/xlnt-fixed 
```