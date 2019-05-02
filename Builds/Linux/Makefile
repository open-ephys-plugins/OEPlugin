#Edit the plugin name here
TARGET := OEPlugin.so

DEPFLAGS := $(if $(word 2, $(TARGET_ARCH)), , -MMD)

ifndef GUI_BASEDIR
	GUI_BASEDIR = ../../../plugin-GUI
endif

GUI_BUILDDIR := $(GUI_BASEDIR)/Builds/Linux/build

OUTDIR := $(GUI_BUILDDIR)/plugins
OBJDIR := $(CURDIR)/build
SRCDIR := ../../Source

ifndef CONFIG
	CONFIG=Debug
endif

ifeq ($(TARGET_ARCH),)
	TARGET_ARCH := -march=native
endif

#Common flags
CPPFLAGS := $(DEPFLAGS) -D "LINUX=1" -D "JUCE_DISABLE_NATIVE_FILECHOOSERS=1" -D "JUCER_LINUX_MAKE_7346DA2A=1" -D "JUCE_APP_VERSION=0.4.0" -D "JUCE_APP_VERSION_HEX=0x400" -I /usr/include -I /usr/include/freetype2 -I $(GUI_BASEDIR)/JuceLibraryCode -I $(GUI_BASEDIR)/JuceLibraryCode/modules -I $(GUI_BASEDIR)/Source/Plugins/Headers
LDFLAGS += $(TARGET_ARCH) -L$(GUI_BUILDDIR) -L/usr/X11R6/lib/ -L/usr/local/include -lGL -lX11 -lXext -lXinerama -lasound -ldl -lfreetype -lpthread -lrt -pg -ldl -lXext -lGLU -fPIC -rdynamic -Wl,-rpath,'$$ORIGIN/../shared'

#Extra include paths
#CPPFLAGS += -I(INCLUDE_PATH)
#extra library paths
#LDFLAGS += -L(LIBPATH) -l(LIBNAME)

CFLAGS += $(CPPFLAGS) $(TARGET_ARCH) -O3 -std=c++11 -fPIC -rdynamic

ifeq ($(CONFIG),Debug)
	CFLAGS += -g -ggdb
	OBJDIR := $(OBJDIR)/Debug
endif

ifeq ($(CONFIG),Release)
	CFLAGS += -g -ggdb
	OBJDIR := $(OBJDIR)/Release
endif

CXXFLAGS += $(CFLAGS)

SRC := $(foreach sdir,$(SRCDIR),$(wildcard $(sdir)/*.cpp))
OBJ := $(addprefix $(OBJDIR)/,$(notdir $(SRC:.cpp=.o)))

BLDCMD := $(CXX) -shared -o $(OUTDIR)/$(TARGET) $(OBJ) $(LDFLAGS) $(TARGET_ARCH)

VPATH :=  $(shell find $(SRCDIR) -type d -print)

.PHONY: clean objdir

$(OUTDIR)/$(TARGET): objdir $(OBJ)
	@echo "Building $(TARGET)"
	@$(BLDCMD)
	
$(OBJDIR)/%.o : %.cpp
	@echo "Compiling $<"
	@$(CXX) $(CXXFLAGS) -o "$@" -c "$<"


objdir:
	-@mkdir -p $(OBJDIR)

clean:
	@echo "Cleaning $(TARGET)"
	-@rm -rf $(OBJDIR)
	-@rm -f $(OUTDIR)/$(TARGET)

-include $(OBJ:%.o=%.d)



