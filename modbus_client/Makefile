prefix=/usr
BIN_NAME=modbus_client
W32_CROSS=i686-w64-mingw32

VERSION := $(shell head -n 1 ../debian/changelog  | grep -oh -P "\(\K.*(?=\))")
W32_BIN_NAME=$(BIN_NAME)_$(VERSION).exe

ifeq ($(DEB_BUILD_GNU_TYPE),$(DEB_HOST_GNU_TYPE))
       CC=gcc
else
       CC=$(DEB_HOST_GNU_TYPE)-gcc
endif

CC_FLAGS=-I../common `PKG_CONFIG_PATH=/usr/lib/$(DEB_HOST_GNU_TYPE)/pkgconfig pkg-config --libs --cflags libmodbus`

$(BIN_NAME): modbus_client.c
	$(CC) modbus_client.c $(CC_FLAGS) -o $(BIN_NAME)

libmodbus-$(W32_CROSS):
	git clone https://github.com/wirenboard/libmodbus.git $@

libmodbus-$(W32_CROSS)/src/.libs/libmodbus.a: libmodbus-$(W32_CROSS)
	cd $< && ./autogen.sh && ./configure --host $(subst libmodbus-,,$<) --enable-static=yes --without-documentation --disable-tests
	make -C $<

$(W32_BIN_NAME): modbus_client.c libmodbus-$(W32_CROSS)/src/.libs/libmodbus.a
	$(W32_CROSS)-gcc modbus_client.c $(CC_FLAGS) -Ilibmodbus-$(W32_CROSS)/src -mconsole -static -Llibmodbus-$(W32_CROSS)/src/.libs/ -lmodbus -l ws2_32 -o $(W32_BIN_NAME)
	$(W32_CROSS)-strip --strip-unneeded $(W32_BIN_NAME)

win32: $(W32_BIN_NAME)

install: $(BIN_NAME)
	install -Dm0755 $(BIN_NAME) -t $(DESTDIR)$(prefix)/bin

clean:
	-@rm $(BIN_NAME)
.PHONY: install clean all



