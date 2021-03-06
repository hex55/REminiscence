DEBUG=0

DEFINES = -DBYPASS_PROTECTION
#DEFINES = -DBYPASS_PROTECTION -DENABLE_PASSWORD_MENU -DNDEBUG

target = reminiscence

section := $(shell sed -n distrib/control -e 's/^.*Section: //p')
title := $(shell sed -n distrib/control -e 's/^.*Package: //p')
description := $(shell sed -n distrib/control -e 's/^.*Description: //p')

ifeq ($(DEBUG),1)
  CC = g++
  CXX = g++
else
  CC = /opt/rs97-toolchain/bin/mipsel-linux-g++
  CXX = /opt/rs97-toolchain/bin/mipsel-linux-g++
endif

SYSROOT    := $(shell $(CC) --print-sysroot)
SDL_CFLAGS := -I/opt/rs97-toolchain/usr/mipsel-buildroot-linux-uclibc/sysroot/usr/include/SDL/
SDL_LIBS   := -lSDL

CXXFLAGS := -g -O -Wall -Wuninitialized -Wshadow -Wundef -Wreorder -Wnon-virtual-dtor -Wno-multichar
CXXFLAGS += -MMD $(SDL_CFLAGS) -DUSE_ZLIB $(DEFINES)

SRCS = collision.cpp cutscene.cpp file.cpp fs.cpp game.cpp graphics.cpp main.cpp menu.cpp \
	mixer.cpp mod_player.cpp piege.cpp resource.cpp scaler.cpp seq_player.cpp sfx_player.cpp \
	staticres.cpp systemstub_sdl.cpp unpack.cpp util.cpp video.cpp

OBJS = $(SRCS:.cpp=.o)
DEPS = $(SRCS:.cpp=.d)

LIBS = $(SDL_LIBS) -lz

-include Makefile.local

$(target).dge: $(OBJS)
	$(CXX) $(LDFLAGS) -o $@ $(OBJS) $(LIBS)

clean:
	rm -f *.o *.d *.ipk *.dge

-include $(DEPS)

ipk: $(target).dge
	@rm -rf /tmp/.$(target)-ipk/ && mkdir -p /tmp/.$(target)-ipk/root/home/retrofw/$(section)/$(target) /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)
	@cp -r $(target).dge distrib/files/* /tmp/.$(target)-ipk/root/home/retrofw/$(section)/$(target)
	@echo title=$(title) > /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk
	@echo description=$(description) >> /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk
	@echo exec=/home/retrofw/$(section)/$(target)/$(target).dge >> /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk
	@echo params=--datapath=data >> /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk
	@echo clock=600 >> /tmp/.$(target)-ipk/root/home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk
	@sed -e "s/^Version:.*/Version: $$(date +%Y%m%d)/" -e "s/^Section:.*/Section: $(section)/" distrib/control > /tmp/.$(target)-ipk/control
	@echo /home/retrofw/apps/gmenu2x/sections/$(section)/$(target).lnk > /tmp/.$(target)-ipk/conffiles
	@tar --owner=0 --group=0 -czvf /tmp/.$(target)-ipk/control.tar.gz -C /tmp/.$(target)-ipk/ control conffiles
	@tar --owner=0 --group=0 -czvf /tmp/.$(target)-ipk/data.tar.gz -C /tmp/.$(target)-ipk/root/ .
	@echo 2.0 > /tmp/.$(target)-ipk/debian-binary
	@ar r distrib/$(target).ipk /tmp/.$(target)-ipk/control.tar.gz /tmp/.$(target)-ipk/data.tar.gz /tmp/.$(target)-ipk/debian-binary
