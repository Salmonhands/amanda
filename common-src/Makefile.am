# Makefile for Amanda library.

include $(top_srcdir)/config/automake/vars.am
include $(top_srcdir)/config/automake/scripts.am
include $(top_srcdir)/config/automake/installperms.am
include $(top_srcdir)/config/automake/precompile.am

AM_CPPFLAGS = -I$(top_srcdir)/gnulib -I$(top_builddir)/common-src

AM_CFLAGS = $(AMANDA_WARNING_CFLAGS) $(AMANDA_FILE_CFLAGS)
AM_LDFLAGS = $(AMANDA_STATIC_LDFLAGS) $(AS_NEEDED_FLAGS)

amlib_LTLIBRARIES =	libamanda.la

sbin_PROGRAMS = amservice

amlibexec_PROGRAMS = ambind

LINT=$(AMLINT)
LINTFLAGS=$(AMLINTFLAGS)

libamanda_la_SOURCES =		\
	alloc.c			\
	am_sl.c			\
	amfeatures.c		\
	amflock.c		\
	amjson.c		\
	ammessage.c		\
	ipc-binary.c		\
	amxml.c			\
	clock.c			\
	conffile.c		\
	debug.c			\
	dgram.c			\
	event.c			\
	file.c			\
	fileheader.c		\
	glib-util.c		\
	match.c			\
	mem-ring.c		\
	packet.c		\
	pipespawn.c		\
	protocol.c		\
	amsemaphore.c		\
	security.c		\
	security-file.c		\
	security-util.c		\
	shm-ring.c		\
	simpleprng.c		\
	sockaddr-util.c		\
	stream.c		\
	tapelist.c		\
	timestamp.c		\
	amutil.c

libamanda_la_SOURCES += amcrc32chw.c
amcrc32chw.o: AM_CFLAGS += $(SSE42_CFLAGS)
amcrc32chw.lo: AM_CFLAGS += $(SSE42_CFLAGS)

# version.c is generated; see below
nodist_libamanda_la_SOURCES = version.c

INSTALLPERMS_exec = \
	dest=$(amlibexecdir) chown=root:setuid chmod=04750 \
		ambind

LDADD = libamanda.la

libamanda_la_LIBADD =		\
	../gnulib/libgnu.la

if WANT_AMFLOCK_POSIX
libamanda_la_SOURCES += amflock-posix.c
endif

if WANT_AMFLOCK_FLOCK
libamanda_la_SOURCES += amflock-flock.c
endif

if WANT_AMFLOCK_LOCKF
libamanda_la_SOURCES += amflock-lockf.c
endif

if WANT_AMFLOCK_LNLOCK
libamanda_la_SOURCES += amflock-lnlock.c
endif


if WANT_RSH_SECURITY
libamanda_la_SOURCES += rsh-security.c
endif
if WANT_SSH_SECURITY
libamanda_la_SOURCES += ssh-security.c
endif
if WANT_BSD_SECURITY
libamanda_la_SOURCES += bsd-security.c
endif
if WANT_BSDTCP_SECURITY
libamanda_la_SOURCES += bsdtcp-security.c
endif
if WANT_BSDUDP_SECURITY
libamanda_la_SOURCES += bsdudp-security.c
endif
if WANT_KRB5_SECURITY
libamanda_la_SOURCES += krb5-security.c
endif
if WANT_SSL_SECURITY
libamanda_la_SOURCES += ssl-security.c
endif

libamanda_la_SOURCES += local-security.c

libamanda_la_LDFLAGS =  -release $(VERSION) $(AS_NEEDED_FLAGS)

noinst_HEADERS =		\
	amanda.h		\
	amcrc32chw.h		\
	amfeatures.h		\
	amjson.h		\
	ammessage.h		\
	ipc-binary.h		\
	amxml.h			\
	clock.h			\
	directtcp.h		\
	amflock.h		\
	conffile.h		\
	debug.h			\
	dgram.h			\
	event.h			\
	file.h			\
	fileheader.h		\
	glib-util.h		\
	match.h			\
	mem-ring.h		\
	packet.h		\
	pipespawn.h		\
	protocol.h		\
	amsemaphore.h		\
	security.h		\
	security-file.h		\
	security-util.h		\
	shm-ring.h		\
	simpleprng.h		\
	am_sl.h			\
	sockaddr-util.h		\
	stream.h		\
	tapelist.h		\
	timestamp.h		\
	amutil.h			\
	version.h

EXTRA_PROGRAMS = genversion $(TEST_PROGS) make_security_file

# Version-building steps:
#
# 1. configure builds svn-info.h, if svn info is available; this
#    file is included in distribution tarballs
# 2. Makefile creates genversion.h with build-time information
# 3. Makefile builds genversion
# 4. Makefile runs genversion to create version.c
# 5. version.c is compiled into libamanda normally

genversion_SOURCES = genversion.c svn-info.h
genversion_LDADD = $(libamanda_la_LIBADD)	\
	../gnulib/libgnu.la

genversion.$(OBJEXT): $(genversion_SOURCES) genversion.h

genversion.h ../perl/Amanda/Constants.pm.in: $(top_builddir)/config.status
	-rm -f genversion.h genversion.h.new
	echo '#define CC "$(CC)"' > genversion.h.new
	GENDATE=`date`; \
	echo '#define BUILT_DATE "'$$GENDATE'"' >> genversion.h.new; \
	echo '#define BUILT_MACH "$(target)"' >> genversion.h.new; \
	mv genversion.h.new genversion.h; \
	if test ! -e ../perl/Amanda; then \
		mkdir ../perl/Amanda; \
	fi; \
	sed "s/^\$$BUILT_DATE.*$$/\$$BUILT_DATE = \"$$GENDATE\";/" $(srcdir)/../perl/Amanda/Constants.pm.in.src > ../perl/Amanda/Constants.pm.in;

version.c: genversion$(EXEEXT)
	-rm -f version.c
	./genversion$(EXEEXT) > version.c

BUILT_SOURCES += genversion.h version.c

amanda-security.conf: make_security_file
	./make_security_file

etcamandadir = ${CONFIG_DIR}
etcamanda_FILE = amanda-security.conf
etcamanda_DATA = ${etcamanda_FILE}

# these are used for testing only:
TEST_PROGS = file bsdsecurity

DISTCLEANFILES += version.c genversion genversion.h config.log amanda-security.conf make_security_file

# used for testing only

STANDARD_COMMON_STUFF_NOT_FILE = \
	alloc.lo \
	clock.lo \
	conffile.lo \
	debug.lo \
	glib-util.lo \
	amutil.lo \
	match.lo \
	sl.lo \
	sockaddr-util.lo \
	timestamp.lo

STANDARD_COMMON_STUFF = \
	$(STANDARD_COMMON_STUFF_NOT_FILE) \
	file.lo

file_SOURCES = file.test.c
file_LDADD = $(libamanda_la_LIBADD) $(STANDARD_COMMON_STUFF_NOT_FILE)

bsdsecurity_SOURCES = bsd-security.test.c
bsdsecurity_LDADD = $(libamanda_a_LIBADD) \
		    alloc.lo \
		    clock.lo \
		    debug.lo \
		    dgram.lo \
		    event.lo \
		    file.lo \
		    packet.lo \
		    security.lo \
		    ssh-security.lo

# libtestutils.la
#
# Convenience library (never installed) for 'make check'-based tests.

noinst_LTLIBRARIES = libtestutils.la
libtestutils_la_SOURCES = \
	testutils.c \
	testutils.h

# automake-style tests

# automake-style tests

TESTS = amflock-test event-test amsemaphore-test crc32-test quoting-test \
	ipc-binary-test hexencode-test fileheader-test match-test
noinst_PROGRAMS = $(TESTS)

amflock_test_SOURCES = amflock-test.c
amflock_test_LDADD = libamanda.la libtestutils.la

event_test_SOURCES = event-test.c
event_test_LDADD = libamanda.la libtestutils.la

amsemaphore_test_SOURCES = amsemaphore-test.c
amsemaphore_test_LDADD = libamanda.la libtestutils.la

crc32_test_SOURCES = crc32-test.c
crc32_test_LDADD = libamanda.la libtestutils.la
crc32-test.o: AM_CFLAGS += $(SSE42_CFLAGS)
crc32-test.lo: AM_CFLAGS += $(SSE42_CFLAGS)

quoting_test_SOURCES = quoting-test.c
quoting_test_LDADD = libamanda.la libtestutils.la

ipc_binary_test_SOURCES = ipc-binary-test.c
ipc_binary_test_LDADD = libamanda.la libtestutils.la

hexencode_test_SOURCES = hexencode-test.c
hexencode_test_LDADD = libamanda.la libtestutils.la

fileheader_test_SOURCES = fileheader-test.c
fileheader_test_LDADD = libamanda.la libtestutils.la

match_test_SOURCES = match-test.c
match_test_LDADD = libamanda.la libtestutils.la

# scripts

# divide scripts up both by language and destination directory
sbin_SCRIPTS_PERL = \
	    amgpgcrypt \
	    amcryptsimple \
	    amgetconf \
	    amssl

amlibexec_SCRIPTS_PERL =

sbin_SCRIPTS_SHELL = \
	    amcrypt \
	    amaespipe \
	    amcrypt-ossl \
	    amcrypt-ossl-asym

amlibexec_SCRIPTS_SHELL = 

SCRIPTS_INCLUDE = \
	    amanda-sh-lib.sh

EXTRA_DIST += amanda-sh-lib.sh.in

SCRIPTS_PERL = $(sbin_SCRIPTS_PERL) $(amlibexec_SCRIPTS_PERL)
SCRIPTS_SHELL = $(sbin_SCRIPTS_SHELL) $(amlibexec_SCRIPTS_SHELL)

amlibexec_DATA = $(SCRIPTS_INCLUDE)
amlibexec_SCRIPTS = $(amlibexec_SCRIPTS_PERL) $(amlibexec_SCRIPTS_SHELL)
sbin_SCRIPTS = $(sbin_SCRIPTS_PERL) $(sbin_SCRIPTS_SHELL)

CLEANFILES += *.test.c $(SCRIPTS_PERL) $(SCRIPTS_SHELL) $(SCRIPTS_INCLUDE)

# installation

lint:
	@echo $(LINT) $(libamanda_la_SOURCES)
	@$(LINT) $(LINTFLAGS) $(CPPFLAGS) $(DEFS) -I. -I$(top_builddir)/config $(AM_CPPFLAGS) $(libamanda_la_SOURCES)
	@echo $(LINT) $(genversion_SOURCES)
	@$(LINT) $(LINTFLAGS) $(CPPFLAGS) $(DEFS) -I. -I$(top_builddir)/config $(AM_CPPFLAGS) $(genversion_SOURCES)

listlibsrc:
	@ for p in $(libamanda_la_SOURCES) $(REGEXCSRC); do	\
		listlibsrcs="$$listlibsrcs `pwd`/$$p";		\
	done;							\
	echo $$listlibsrcs >listlibsrc.output

%.test.c: $(srcdir)/%.c
	echo '#define TEST' >$@
	echo '#include "$<"' >>$@

install-build-dependencies: install-data-am

distclean-local:
	if test `cd $(srcdir) && pwd` != `pwd`; then rm -f svn-info.h; fi
MAINTAINERCLEANFILES+=svn-info.h
