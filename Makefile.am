# SPDX-License-Identifier: BSD-2
#
.PHONY: example
AM_CFLAGS = -I$(srcdir)/src

TESTS = $(TEST_UNIT) $(TEST_INTEGRATION)
libtss2_tcti_uefi = src/libtss2-tcti-uefi.a
lib_LIBRARIES = $(libtss2_tcti_uefi)
libexample_util = example/libexample-util.a
noinst_LIBRARIES = $(libexample_util) test/libtest-util.a
TEST_EXTENSIONS = .efi
EFI_LOG_COMPILER = $(srcdir)/lib/efi-test-setup.sh
EFI_LOG_FLAGS = --startup-template=$(srcdir)/lib/startup.nsh.template
if UNIT
TEST_UNIT = \
    test/example-util_unit \
    test/tcg2-util_unit \
    test/tcti-uefi-cast_unit \
    test/tcti-uefi-finalize_unit \
    test/tcti-uefi-init_unit \
    test/tcti-uefi-receive_unit \
    test/tcti-uefi-transmit_unit
endif
if INTEGRATION
TEST_INTEGRATION = \
    test/hello-world.efi \
    test/tss2-mu-uint32.efi
endif
check_PROGRAMS = $(TEST_UNIT)
tss2dir = $(includedir)/tss2
tss2_HEADERS = src/tss2-tcti-uefi.h

if AUTOCONF_CODE_COVERAGE_2019_01_06
include $(top_srcdir)/aminclude_static.am
clean-local: code-coverage-clean
	rm -rf $(CLEANS)
distclean-local: code-coverage-dist-clean

# workaround for a bug in Autoconf Archive 2019.01.06
# (https://github.com/autoconf-archive/autoconf-archive/pull/182)
if CODE_COVERAGE_ENABLED
AM_DISTCHECK_CONFIGURE_FLAGS := $(AM_DISTCHECK_CONFIGURE_FLAGS) --disable-code-coverage
endif # CODE_COVERAGE_ENABLED

else # AUTOCONF_CODE_COVERAGE_2019_01_06
@CODE_COVERAGE_RULES@
CODE_COVERAGE_DIRECTORY = $(top_builddir)/src
clean-local:
	rm -rf $(CLEANS)
endif # AUTOCONF_CODE_COVERAGE_2019_01_06

all-local: AUTHORS

example: \
    example/tcg2-get-caps.efi \
    example/tcg2-get-eventlog.efi \
    example/tpm2-get-caps-fixed.efi \
    example/tpm2-get-pcrs.efi \
    example/tcg2-get-pcr-banks.efi

CLEANS = \
    AUTHORS \
    example/tcg2-get-caps.efi \
    example/tcg2-get-caps.so \
    example/tcg2-get-eventlog.efi \
    example/tcg2-get-eventlog.so \
    example/tpm2-get-caps-fixed.efi \
    example/tpm2-get-caps-fixed.so \
    example/tpm2-get-pcrs.efi \
    example/tpm2-get-pcrs.so \
    example/tcg2-get-pcr-banks.efi \
    example/tcg2-get-pcr-banks.so \
    example/*.$(OBJEXT) \
    test/hello-world.efi \
    test/hello-world.so \
    test/tss2-mu-uint32.efi \
    test/tss2-mu-uint32.so

EXTRA_DIST = \
    lib/efi-test-setup.sh \
    lib/startup.nsh.template \
    test/hello-world.c \
    test/tss2-mu-uint32.c \
    AUTHORS \
    CHANGELOG.md \
    CONTRIBUTING.md \
    INSTALL.md \
    LICENSE \
    README.md \
    VERSION

# rules to build libraries
src_libtss2_tcti_uefi_a_CFLAGS = $(EXTRA_CFLAGS) $(AM_CFLAGS) \
    $(CODE_COVERAGE_CFLAGS)
src_libtss2_tcti_uefi_a_SOURCES = src/tcti-uefi.c src/tcti-uefi.h \
    src/tcg2-protocol.h src/uefi-types.h src/tcg2-util.c src/tcg2-util.h

example_libexample_util_a_CFLAGS = $(EXTRA_CFLAGS) $(AM_CFLAGS) \
    $(CODE_COVERAGE_CFLAGS)
example_libexample_util_a_SOURCES = example/compat.c example/compat.h \
    example/tss2-util.c example/tss2-util.h example/util.c example/util.h

# hack to ensure .deps directory required by autotools is created in the
# 'example' directory before we build any targets
example/.deps:
	mkdir -p $@
test/hello-world.$(OBJEXT): test/hello-world.c | example/.deps
example/tcg2-get-caps.$(OBJEXT): example/tcg2-get-caps.c \
    src/tcg2-protocol.h src/tcg2-util.h | example/.deps
example/tcg2-get-eventlog.$(OBJEXT): example/tcg2-get-eventlog.c \
    |example/.deps
example/tpm2-get-caps-fixed.$(OBJEXT): example/tpm2-get-caps-fixed.c \
    example/compat.h example/tss2-util.h | example/.deps
example/tpm2-get-pcrs.$(OBJEXT): example/tpm2-get-pcrs.c \
    example/tss2-util.h | example/.deps
example/tcg2-get-pcr-banks.$(OBJEXT): example/tcg2-get-pcr-banks.c \
    example/compat.h example/tss2-util.h | example/.deps
test/tss2-mu-uint32.$(OBJEXT): test/tss2-mu-uint32.c | example/.deps

# dependency expression for shared objects built for the UEFI executables
example/tcg2-get-caps.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
example/tcg2-get-caps.so: LDFLAGS+=-Wl,--no-undefined
example/tcg2-get-caps.so: LDLIBS+=$(CODE_COVERAGE_LIBS)
example/tcg2-get-caps.so: $(libtss2_tcti_uefi) $(libexample_util)
example/tcg2-get-caps.so: example/tcg2-get-caps.$(OBJEXT)

example/tcg2-get-eventlog.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
example/tcg2-get-eventlog.so: LDFLAGS+=-Wl,--no-undefined
example/tcg2-get-eventlog.so: LDLIBS+=$(CODE_COVERAGE_LIBS)
example/tcg2-get-eventlog.so: $(libtss2_tcti_uefi) $(libexample_util)
example/tcg2-get-eventlog.so: example/tcg2-get-eventlog.$(OBJEXT)

example/tpm2-get-caps-fixed.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
example/tpm2-get-caps-fixed.so: LDFLAGS+=-Wl,--no-undefined
example/tpm2-get-caps-fixed.so: LDLIBS+=-l:libtss2-sys.a -l:libtss2-mu.a \
    $(CODE_COVERAGE_LIBS)
example/tpm2-get-caps-fixed.so: $(libexample_util) $(libtss2_tcti_uefi)
example/tpm2-get-caps-fixed.so: example/tpm2-get-caps-fixed.$(OBJEXT)

example/tpm2-get-pcrs.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
example/tpm2-get-pcrs.so: LDFLAGS+=-Wl,--no-undefined
example/tpm2-get-pcrs.so: LDLIBS+=-l:libtss2-sys.a -l:libtss2-mu.a \
    $(CODE_COVERAGE_LIBS)
example/tpm2-get-pcrs.so: $(libexample_util) $(libtss2_tcti_uefi)
example/tpm2-get-pcrs.so: example/tpm2-get-pcrs.$(OBJEXT)

example/tcg2-get-pcr-banks.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
example/tcg2-get-pcr-banks.so: LDFLAGS+=-Wl,--no-undefined
example/tcg2-get-pcr-banks.so: LDLIBS+=$(CODE_COVERAGE_LIBS)
example/tcg2-get-pcr-banks.so: $(libexample_util) $(libtss2_tcti_uefi)
example/tcg2-get-pcr-banks.so: example/tcg2-get-pcr-banks.$(OBJEXT)

test/tss2-mu-uint32.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
test/tss2-mu-uint32.so: LDFLAGS+=-Wl,--no-undefined
test/tss2-mu-uint32.so: LDLIBS+=-l:libtss2-mu.a
test/tss2-mu-uint32.so: test/tss2-mu-uint32.$(OBJEXT)

test/hello-world.so: CFLAGS+=$(EXTRA_CFLAGS) $(AM_CFLAGS)
test/hello-world.so: LDFLAGS+=-Wl,--no-undefined
test/hello-world.so: test/hello-world.$(OBJEXT)

# rule to extract contributors from git history & generate AUTHORS file
AUTHORS :
	git log --format='%aN <%aE>' | grep -v 'users.noreply.github.com' | sort | \
	    uniq -c | sort -nr | sed 's/^\s*//' | cut -d" " -f2- > $@

# variables to simplify test rules
TEST_FLAGS = $(CMOCKA_CFLAGS) $(EXTRA_CFLAGS) $(AM_CFLAGS) \
    $(CODE_COVERAGE_CFLAGS)
TEST_LIBS = $(libexample_util) $(CMOCKA_LIBS) $(CODE_COVERAGE_LIBS) \
    src/libtss2-tcti-uefi.a test/libtest-util.a
TEST_WRAPS_TCTI = -Wl,--wrap=AllocatePool,--wrap=CopyMem,--wrap=FreePool \
    -Wl,--wrap=Print,--wrap=tcg2_get_capability,--wrap=tcg2_get_max_buf \
    -Wl,--wrap=tcg2_get_protocol,--wrap=tcg2_submit_command,--wrap=DumpHex
TEST_WRAPS_TCG2 = -Wl,--wrap=AllocatePool,--wrap=CopyMem \
    -Wl,--wrap=FreePool,--wrap=Print,--wrap=efi_call2,--wrap=efi_call5 \
    -Wl,--wrap=LibLocateProtocol

if UNIT
# rule to build test utility library
test_libtest_util_a_CFLAGS = $(EXTRA_CFLAGS) $(AM_CFLAGS)
test_libtest_util_a_SOURCES = test/tcti-uefi-wraps.c test/tcti-uefi-wraps.h

# rules to build test executables
test_example_util_unit_CFLAGS = $(TEST_FLAGS) -DGNU_EFI_SETJMP_H
test_example_util_unit_LDADD = $(TEST_LIBS)
test_example_util_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_example_util_unit_SOURCES = test/example-util_unit.c

test_tcg2_util_unit_CFLAGS = $(TEST_FLAGS) -DGNU_EFI_SETJMP_H
test_tcg2_util_unit_LDADD = $(TEST_LIBS)
test_tcg2_util_unit_LDFLAGS = $(TEST_WRAPS_TCG2)
test_tcg2_util_unit_SOURCES = test/tcg2-util_unit.c

test_tcti_uefi_cast_unit_CFLAGS = $(TEST_FLAGS)
test_tcti_uefi_cast_unit_LDADD = $(TEST_LIBS)
test_tcti_uefi_cast_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_tcti_uefi_cast_unit_SOURCES = test/tcti-uefi-cast_unit.c

test_tcti_uefi_init_unit_CFLAGS = $(TEST_FLAGS)
test_tcti_uefi_init_unit_LDADD = $(TEST_LIBS)
test_tcti_uefi_init_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_tcti_uefi_init_unit_SOURCES = test/tcti-uefi-init_unit.c

test_tcti_uefi_receive_unit_CFLAGS = $(TEST_FLAGS)
test_tcti_uefi_receive_unit_LDADD = $(TEST_LIBS)
test_tcti_uefi_receive_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_tcti_uefi_receive_unit_SOURCES = test/tcti-uefi-receive_unit.c

test_tcti_uefi_transmit_unit_CFLAGS = $(TEST_FLAGS)
test_tcti_uefi_transmit_unit_LDADD = $(TEST_LIBS)
test_tcti_uefi_transmit_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_tcti_uefi_transmit_unit_SOURCES = test/tcti-uefi-transmit_unit.c

test_tcti_uefi_finalize_unit_CFLAGS = $(TEST_FLAGS)
test_tcti_uefi_finalize_unit_LDADD = $(TEST_LIBS)
test_tcti_uefi_finalize_unit_LDFLAGS = $(TEST_WRAPS_TCTI)
test_tcti_uefi_finalize_unit_SOURCES = test/tcti-uefi-finalize_unit.c
endif

# pattern rule to link intermediate shared object
%.so: %.o
	$(CC) $(LDFLAGS) $(EXTRA_LDFLAGS) $^ -o $@ -Bstatic $(LDLIBS) $(EXTRA_LDLIBS)

# pattern to transform intermediate shared object into efi executable
%.efi: %.so
	$(OBJCOPY) $(OBJFLAGS) $(EXTRA_OBJFLAGS) $^ $@
