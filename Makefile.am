SUBDIRS=. po

AM_CPPFLAGS = $(ALL_CFLAGS)

packagesdir = $(datadir)/mime/packages
packages_DATA = freedesktop.org.xml

bin_PROGRAMS = update-mime-database
update_mime_database_SOURCES = update-mime-database.c
update_mime_database_CFLAGS = $(ALL_CFLAGS)
update_mime_database_LDADD = $(ALL_LIBS)

noinst_PROGRAMS = test-subclassing
test_subclassing_SOURCES = test-subclassing.c
test_subclassing_CFLAGS = $(ALL_CFLAGS)
test_subclassing_LDADD = $(ALL_LIBS)

if HAVE_GIO
noinst_PROGRAMS += test-tree-magic
test_tree_magic_SOURCES = test-tree-magic.c
test_tree_magic_CFLAGS = $(GIO_CFLAGS)
test_tree_magic_LDADD = $(GIO_LIBS)
endif

man_MANS = update-mime-database.1

EXTRA_DIST = \
	shared-mime-info-spec.xml	\
	freedesktop.org.xml.in		\
	test-tree-magic.c		\
	$(packages_DATA) 		\
	README				\
	HACKING				\
	$(man_MANS)			\
	shared-mime-info.pc.in		\
	po/shared-mime-info.pot

CLEANFILES = freedesktop.org.xml po/stamp-it
DISTCLEANFILES = intltool-extract intltool-merge intltool-update

pkgconfigdir = $(datadir)/pkgconfig
pkgconfig_DATA = shared-mime-info.pc

$(pkgconfig_DATA): config.status

@INTLTOOL_XML_RULE@
@INTLTOOL_DESKTOP_RULE@

if CROSS_COMPILING
  update_mime_database=$(UPDATE_MIME_DATABASE)
else
  update_mime_database="$(top_builddir)/update-mime-database$(EXEEXT)"
endif

install-data-hook: install-binPROGRAMS
if ENABLE_UPDATE_MIMEDB
	$(update_mime_database) -V "$(DESTDIR)$(datadir)/mime"
endif

uninstall-hook:
	for media in text application image audio inode video message model multipart x-epoc x-content font; do rm -f "$(DESTDIR)$(datadir)/mime/$${media}/"*.xml; done
	rm -f "$(DESTDIR)$(datadir)/mime/globs"
	rm -f "$(DESTDIR)$(datadir)/mime/magic"
	rm -f "$(DESTDIR)$(datadir)/mime/aliases"
	rm -f "$(DESTDIR)$(datadir)/mime/subclasses"
	rm -f "$(DESTDIR)$(datadir)/mime/XMLnamespaces"
	rm -f "$(DESTDIR)$(datadir)/mime/mime.cache"
	rm -f "$(DESTDIR)$(datadir)/mime/XMLnamespaces"
	rm -f "$(DESTDIR)$(datadir)/mime/mime.cache"
	rm -f "$(DESTDIR)$(datadir)/mime/icons"
	rm -f "$(DESTDIR)$(datadir)/mime/generic-icons"
	rm -f "$(DESTDIR)$(datadir)/mime/globs2"
	rm -f "$(DESTDIR)$(datadir)/mime/treemagic"
	rm -f "$(DESTDIR)$(datadir)/mime/types"
	rm -f "$(DESTDIR)$(datadir)/mime/version"

defaultmakedeps=create-pot
if ENABLE_DEFAULT_MAKE_CHECK
defaultmakedeps += check
endif

all: $(defaultmakedeps)

create-pot:
	$(AM_V_GEN) $(MAKE) -C po shared-mime-info.pot

local-test: create-pot freedesktop.org.xml update-mime-database$(EXEEXT)
if CROSS_COMPILING
	@echo "Cross compiling: Skipping checks"
else
	@if ! test -d staging-tests/ ; then \
		echo "***************************************************************************" ; \
		echo "*** Add your file to process to staging-tests/ and run local-test again ***" ; \
		echo "***************************************************************************" ; \
		exit 1 ; \
	fi
	if false && test -d .git/ && test -x ../xdgmime/src/print-mime-data$(EXEEXT) ; then \
		mkdir -p $(top_builddir)/temp-mime-dir/mime/packages ; \
		cp -a $(top_builddir)/freedesktop.org.xml $(top_builddir)/temp-mime-dir/mime/packages/ ; \
		XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" $(top_builddir)/update-mime-database$(EXEEXT) "$(top_builddir)/temp-mime-dir/mime/" ; \
		XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" XDG_DATA_HOME="$(top_builddir)/temp-mime-dir/" ../xdgmime/src/print-mime-data$(EXEEXT) staging-tests/; \
		rm -rf "$(top_builddir)/temp-mime-dir/" ; \
	fi
endif

check: create-pot freedesktop.org.xml update-mime-database$(EXEEXT) check-translations
if CROSS_COMPILING
	@echo "Cross compiling: Skipping checks"
else
	if test -e $(top_builddir)/freedesktop.org.xml; then \
		xmllint --noout --valid $(top_builddir)/freedesktop.org.xml || exit 1;	\
	else \
		xmllint --noout --valid $(top_srcdir)/freedesktop.org.xml || exit 1;	\
	fi; \
	if false && test -d .git/ && test -x $(top_builddir)/update-mime-database$(EXEEXT) && test -d $(top_srcdir)/mime-db-tests ; then \
		$(top_builddir)/update-mime-database$(EXEEXT) mime-db-tests/ ; \
	fi
	@if false && test -d .git/ ; then \
		if test ! -x ../xdgmime/src/test-mime-data$(EXEEXT) || test ! -x $(top_builddir)/test-tree-magic$(EXEEXT) ; then \
			echo "***************************************************************************" ; \
			echo "*** xdgmime not compiled, test suite cannot run. Check HACKING for info ***" ; \
			echo "***************************************************************************" ; \
			exit 1 ; \
		else \
			mkdir -p $(top_builddir)/temp-mime-dir/mime/packages ; \
			cp -a $(top_builddir)/freedesktop.org.xml $(top_builddir)/temp-mime-dir/mime/packages/ ; \
			XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" $(top_builddir)/update-mime-database$(EXEEXT) "$(top_builddir)/temp-mime-dir/mime/" ; \
			XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" XDG_DATA_HOME="$(top_builddir)/temp-mime-dir/" ../xdgmime/src/test-mime-data$(EXEEXT) -v tests/list || XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" XDG_DATA_HOME="$(top_builddir)/temp-mime-dir/" ../xdgmime/src/test-mime$(EXEEXT) || (XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" ../xdgmime/src/test-mime-data$(EXEEXT) -v -v tests/list ; exit 1); \
			XDG_DATA_DIRS="$(top_builddir)/temp-mime-dir/" $(top_builddir)/test-tree-magic$(EXEEXT) tests/tree-list || exit 1; \
			rm -rf "$(top_builddir)/temp-mime-dir/" ; \
		fi; \
	fi
	@if `grep comment $(srcdir)/freedesktop.org.xml.in | grep -v _comment | grep -q -v '<!'` ; then \
		echo "***************************************************************************" ; \
		echo "*** Some comments are not marked as translatable, fix before committing ***" ; \
		echo "***************************************************************************" ; \
		exit 1 ; \
	fi
	@echo Number of mime-types that might need generic-icon: `grep "mime-type type=" $(srcdir)/freedesktop.org.xml.in | grep -v video/ | grep -v image/ | grep -v text/ | grep -v audio/ | grep -v "octet-stream" | wc -l` ; \
	echo Number of mime-types with a generic icon: `grep "generic-icon name=" $(srcdir)/freedesktop.org.xml.in | wc -l` ;
endif

check-translations:
	@export RET=0 ; for i in $(top_srcdir)/po/*.po ; do						\
		if ! grep -q ^`basename $$i | sed 's,.po,,'`$$ $(top_srcdir)/po/LINGUAS ; then		\
			echo '***' `basename $$i | sed 's,.po,,'` missing from po/LINGUAS '***' ;	\
			RET=1;										\
		fi;											\
	done;												\
	if [ $$RET == 1 ] ; then exit 1; fi

update-translations: create-pot
	@tx pull --all --force --skip
	@tx push --source

# Build ChangeLog from GIT history
ChangeLog:
	@if test -f $(top_srcdir)/.git/HEAD; then \
		git log --stat --after="Tue Oct 6 14:46:33 2009 +0000" > $@; \
	fi

dist: ChangeLog

.PHONY: ChangeLog

