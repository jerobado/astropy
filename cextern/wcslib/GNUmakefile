#-----------------------------------------------------------------------------
# GNU makefile for building WCSLIB 8.4
#
# Summary of the main targets
# ---------------------------
#   all:       Do 'make all' in each subdirectory (excluding ./doxygen).
#   check:     Do 'make check' in each subdirectory (compile and run tests).
#   tests:     Do 'make tests' in each subdirectory (compile test programs but
#              don't run them).
#   install:   Do 'make install' in each subdirectory.
#   uninstall: Deletes installed files (this release only), including the
#              sharable library.
#   clean:     Recursively delete intermediate files produced as part of the
#              build, e.g. object modules, core dumps, etc.
#   cleaner:   Recursively clean, and also delete test executables, test
#              input and output, and intermediates produced in compiling the
#              programmers' manual.
#   distclean (or realclean): Recursively delete all platform-dependent files
#              generated during the build, preserving only the programmers'
#              manual and man pages (which are normally provided pre-built).
#              It is the one to use between builds for multiple platforms.
#   cleanest:  Like distclean, but deletes everything that can be regenerated
#              from the source files, including the programmers' manual and
#              man pages, but excluding 'configure'.
#   show:      Print the values of important variables used in this and the
#              other makefiles.
#   writable:  Run chmod recursively to make all sources writable.
#
# Notes:
#   1) If you need to make changes then preferably modify makedefs.in instead.
#
#   2) Refer also to the makefiles in subdirectories, particularly
#      C/GNUmakefile.
#
# Author: Mark Calabretta, Australia Telescope National Facility, CSIRO.
# http://www.atnf.csiro.au/people/Mark.Calabretta
# $Id: GNUmakefile,v 8.4 2024/10/28 13:56:17 mcalabre Exp $
#-----------------------------------------------------------------------------
# Get configure settings.
SUBDIR := .
include makedefs

ifeq "$(CHECK)" "nopgplot"
  TSTDIRS := $(filter-out pgsbox,$(TSTDIRS))
endif

.PHONY : build check chmod clean cleaner cleanest distclean install \
         realclean show tests uninstall writable

build :
	-@ for DIR in $(SUBDIRS) ; do \
	     echo '' ; \
	     $(TIMER) ; \
	     $(MAKE) -k -C $$DIR build ; \
	   done

check tests :: show
	-@ echo ''
	-@ $(TIMER)
	 @ for DIR in $(SUBDIRS) ; do \
	     echo '' ; \
	     $(MAKE) -i -C $$DIR cleaner build ; \
	   done
	-@ echo ''
	 @ for DIR in $(TSTDIRS) ; do \
	     echo '' ; \
	     $(TIMER) ; \
	     $(MAKE) -k -C $$DIR $@ ; \
	   done

check ::
	-@ echo ''
	-@ echo 'Summary of results for non-graphical tests'
	-@ echo '------------------------------------------'
	-@ cat ./*/test_results
	 @ if grep 'FAIL:' ./*/test_results > /dev/null ; then \
	     exit 1 ; \
	   else \
	     exit 0 ; \
	   fi

install :
	 @ for DIR in $(INSTDIR) ; do \
	     $(MAKE) -k -C $$DIR $@ ; \
	   done
	   if [ ! -d "$(LIBDIR)/pkgconfig" ] ; then \
	     $(INSTALL) -d -m 775 $(LIBDIR)/pkgconfig ; \
	   fi
	   $(INSTALL) -m 444 wcslib.pc $(LIBDIR)/pkgconfig/wcslib.pc
	   $(INSTALL) -m 444 wcsconfig.h wcsconfig_f77.h $(INCDIR)
	-  if [ ! -d "$(DOCDIR)" ] ; then \
	     $(INSTALL) -d -m 775 $(DOCDIR) ; \
	   fi
	   $(INSTALL) -m 444 CHANGES COPYING* README $(DOCDIR)
	   $(INSTALL) -m 444 INSTALL THANKS VALIDATION $(DOCDIR)
	-  if [ -h $(DOCLINK) ] ; then \
	     $(RM) $(DOCLINK) ; \
	   fi
	   $(LN_S) $(notdir $(DOCDIR)) $(DOCLINK)
	   $(MAKE) -k -C doxygen $@

uninstall :
	 @ for DIR in $(INSTDIR) ; do \
	     $(MAKE) -k -C $$DIR $@ ; \
	   done
	-  cd $(LIBDIR) && $(RM) pkgconfig/wcslib.pc
	-  cd $(INCDIR) && $(RM) wcsconfig*.h
	-  $(RM) $(DOCLINK)
	-  $(RM) $(DOCDIR)
	   $(MAKE) -k -C doxygen $@

clean cleaner :
	   for DIR in $(SUBDIRS) doxygen ; do \
	     $(MAKE) -C $$DIR $@ ; \
	   done

cleanest distclean realclean :
	   for DIR in $(SUBDIRS) doxygen ; do \
	     $(MAKE) -C $$DIR $@ ; \
	   done
	-  $(RM) *.log
	-  $(RM) -r autom4te.cache autoscan.log
	-  $(RM) -r api-sanity-check
	-  $(RM) configure~ confdefs.h conftest.*
	-  $(RM) config.log config.status configure.lineno
	-  $(RM) makedefs wcslib.pc
	-  $(RM) wcsconfig.h wcsconfig_*.h
	-  $(RM) wcslib-*.tar.gz

show ::
	-@ echo 'Subdirectories to be built...'
	-@ echo '  SUBDIRS     := $(SUBDIRS)'
	-@ echo '  TSTDIRS     := $(TSTDIRS)'
	-@ echo ''

writable :
	  chmod -R u+w .

GNUmakefile : makedefs ;

makedefs : makedefs.in config.status
	-@ echo ''
	-@ $(TIMER)
	   ./config.status

config.status : configure
	-@ echo ''
	-@ $(TIMER)
	-@ echo ''
	-@ echo "Environment variables that affect 'configure':"
	-@ echo "  FLEX     = $${FLEX-(undefined)}"
	-@ echo "  FLFLAGS  = $${FLFLAGS-(undefined)}"
	-@ echo "  CPP      = $${CPP-(undefined)}"
	-@ echo "  CPPFLAGS = $${CPPFLAGS-(undefined)}"
	-@ echo "  CC       = $${CC-(undefined)}"
	-@ echo "  CFLAGS   = $${CFLAGS-(undefined)}"
	-@ echo "  F77      = $${F77-(undefined)}"
	-@ echo "  FFLAGS   = $${FFLAGS-(undefined)}"
	-@ echo "  BINDC    = $${BINDC-(undefined)}"
	-@ echo "  ARFLAGS  = $${ARFLAGS-(undefined)}"
	-@ echo "  LDFLAGS  = $${LDFLAGS-(undefined)}"
	-@ echo ''
	   ./configure --no-create


#-----------------------------------------------------------------------------
# These are for code management.

.PHONY : dist

dist :
	   $(MAKE) -C doxygen cleanest build
	   $(MAKE) -C utils man
	   $(MAKE) distclean
	-@ echo $(WCSLIBPKG)/C/RCS        >  wcslib.X
	-@ echo $(WCSLIBPKG)/C/flexed/RCS >> wcslib.X
	-@ echo $(WCSLIBPKG)/C/test/RCS   >> wcslib.X
	-@ echo $(WCSLIBPKG)/doxygen/RCS  >> wcslib.X
	-@ echo $(WCSLIBPKG)/Fortran/RCS  >> wcslib.X
	-@ echo $(WCSLIBPKG)/Fortran/test/RCS >> wcslib.X
	-@ echo $(WCSLIBPKG)/makedefs     >> wcslib.X
	-@ echo $(WCSLIBPKG)/other        >> wcslib.X
	-@ echo $(WCSLIBPKG)/pgsbox/RCS   >> wcslib.X
	-@ echo $(WCSLIBPKG)/RCS          >> wcslib.X
	-@ echo $(WCSLIBPKG)/TODO         >> wcslib.X
	-@ echo $(WCSLIBPKG)/utils/RCS    >> wcslib.X
	-@ echo $(WCSLIBPKG)/wcslib.T     >> wcslib.X
	-@ echo $(WCSLIBPKG)/wcslib.X     >> wcslib.X
	   rm -f $(WCSLIBPKG).tar.bz2
	   tar cf - -C .. -X wcslib.X $(WCSLIBPKG) | \
	     tar t | \
	     grep -v '/$$' | \
	     sort > wcslib.T
	   rm -f wcslib.X
	   tar cvf $(WCSLIBPKG).tar -C .. -T wcslib.T
	   rm -f wcslib.T
	   bzip2 $(WCSLIBPKG).tar
	   chmod 444 $(WCSLIBPKG).tar.bz2

install_dist :
	   scp -p $(WCSLIBPKG).tar.bz2 cal103@venice:/nfs/ftp/software/wcslib/
	   cp -fp $(WCSLIBPKG).tar.bz2 ~/public_html/WCS/
	   mv -f  $(WCSLIBPKG).tar.bz2 ../wcslib-releases/
	   ssh cal103@venice "cd /nfs/ftp/software/wcslib/ && \
	     rm -f wcslib.tar.bz2 && \
	     ln -s $(WCSLIBPKG).tar.bz2 wcslib.tar.bz2"
	   cp -fp CHANGES wcslib.pdf ~/public_html/WCS/
	   rsync --archive --delete html/ ~/public_html/WCS/wcslib/

configure : configure.ac
	-@ echo ''
	-@ $(TIMER)
	   autoconf
	-@ $(RM) configure~

# Code development settings exported for 'configure'.
-include flavours
