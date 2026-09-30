TARGET		:= zelda3
TITLE	:= ZELDASNES
SOURCES	:= ../.. ../../src ../../snes

LIBS = -lSDL2_mixer -lSDL2_image -lSDL2 -lpng -lwebp -ljpeg -lmikmod -lz -lm -lvita2d \
  -lstdc++ -lsndfile -lvorbis -lvorbisenc -lvorbisfile -logg -lcurl -lssl -lSceIme_stub \
  -lcrypto -lFLAC -lopenal -limgui -lvitaGL -lvitashark -lSceShaccCgExt -lmathneon -lopus \
  -lmpg123 -ltaihen_stub -lkubridge_stub -lSceAppMgr_stub -lSceAppUtil_stub -lSceAudio_stub \
  -lSceAudioIn_stub -lSceCtrl_stub -lSceCommonDialog_stub -lSceDisplay_stub -lSceFios2_stub \
  -lSceGxm_stub -lSceLibKernel_stub -lSceShaccCg_stub -lSceSysmodule_stub -lScePower_stub \
  -lSceTouch_stub -lSceMotion_stub -lSceHid_stub -lSceVshBridge_stub -lSceKernelDmacMgr_stub

CFILES   := $(foreach dir,$(SOURCES), $(wildcard $(dir)/*.c))
CPPFILES   := $(foreach dir,$(SOURCES), $(wildcard $(dir)/*.cpp))
BINFILES := $(foreach dir,$(DATA), $(wildcard $(dir)/*.bin))
OBJS     := $(addsuffix .o,$(BINFILES)) $(CFILES:.c=.o) $(CPPFILES:.cpp=.o) 

PREFIX  = arm-vita-eabi
CC      = $(PREFIX)-gcc
CXX      = $(PREFIX)-g++
# Keep the source language used before the compiler default changed to C23.
CFLAGS  = -std=gnu17 -g -Wl,-q,--wrap,fopen,--wrap,mkdir -O3 -ffast-math -DSTBI_NO_THREAD_LOCALS \
	-I$(VITASDK)/$(PREFIX)/include/SDL2 -fno-tree-vectorize -DNDEBUG -I../..
CXXFLAGS  = $(filter-out -std=gnu17,$(CFLAGS)) -fno-exceptions -std=gnu++11 -fpermissive
ASFLAGS = $(CFLAGS)
# Reserve room for SCE import metadata between load segments.
LDFLAGS += -Wl,--defsym=__sce_headroom=0x10000

all: $(TARGET).vpk

$(TARGET).vpk: $(TARGET).velf
	vita-make-fself -c -s $< eboot.bin
	vita-mksfoex -s TITLE_ID=$(TITLE) "Zelda: A Link to the Past" param.sfo
	vita-pack-vpk -s param.sfo -b eboot.bin \
		--add icon0.png=sce_sys/icon0.png \
		--add bg.png=sce_sys/livearea/contents/bg.png \
		--add pic0.png=sce_sys/pic0.png \
		--add startup.png=sce_sys/livearea/contents/startup.png \
		--add template.xml=sce_sys/livearea/contents/template.xml \
		$(TARGET).vpk

%.velf: %.elf
	cp $< $<.unstripped.elf
	$(PREFIX)-strip -g $<
	vita-elf-create $< $@

$(TARGET).elf: $(OBJS)
	$(CXX) $(CXXFLAGS) $(LDFLAGS) $^ $(LIBS) -o $@

clean:
	@rm -rf $(TARGET).velf $(TARGET).elf $(TARGET).sysvelf $(OBJS)
