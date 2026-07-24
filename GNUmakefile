# Self-contained GNU makefile for libparserutils.
#
# GNU make prefers GNUmakefile over the upstream netsurf-buildsystem Makefile,
# so plain `make` here builds libparserutils without that buildsystem.  This
# file is deliberately standalone — it touches nothing tracked and writes its
# output under build-gnu/ — so the commit adding it can be rebased cleanly.
# `make -f Makefile` still drives the original buildsystem.
#
# It builds a static archive: build-gnu/libparserutils.a
#
# The input filter uses the system iconv (glibc) by default; nothing extra is
# needed at archive time (iconv resolves when the final program is linked).

CC       ?= cc
AR       ?= ar
BUILDDIR := build-gnu
LIB      := $(BUILDDIR)/libparserutils.a

all: $(LIB)

CFLAGS := -std=c99 -O2 -g -D_BSD_SOURCE -D_DEFAULT_SOURCE -Iinclude -Isrc

SRCS := $(shell find src -name '*.c')
OBJS := $(patsubst src/%.c,$(BUILDDIR)/%.o,$(SRCS))

$(LIB): $(OBJS)
	@mkdir -p $(dir $@)
	$(AR) cr $@ $^

# src/charset/aliases.c #includes aliases.inc, a charset-alias table generated
# by a perl script from build/Aliases.  make-aliases.pl hardcodes the output
# path (src/charset/aliases.inc), so it is written in-tree — but that file is
# gitignored, so this does not affect the tracked tree or rebasing.
src/charset/aliases.inc: build/Aliases build/make-aliases.pl
	perl build/make-aliases.pl
$(BUILDDIR)/charset/aliases.o: src/charset/aliases.inc

$(BUILDDIR)/%.o: src/%.c
	@mkdir -p $(dir $@)
	$(CC) $(CFLAGS) -MMD -MP -MF $(@:.o=.d) -c -o $@ $<

clean:
	rm -rf $(BUILDDIR)
	rm -f src/charset/aliases.inc

.PHONY: all clean

-include $(shell find $(BUILDDIR) -name '*.d' 2>/dev/null)
