all : index.html
docs : doc.html

SUBDIRS =
PACKAGE = mksite

TODAYSDATE := $(shell date +%Y.%m%d)
TODAYSHOUR := $(shell date +%Y.%m%d.%H%M)
VERSION=$(TODAYSDATE)
SNAPSHOT=$(TODAYSHOUR)
DISTNAME = $(PACKAGE)-$(VERSION)
SNAPSHOTNAME = $(PACKAGE)-$(SNAPSHOT)
SAVETO = _dist

DISTFILES = GNUmakefile README.TXT COPYING.ZLIB $(PACKAGE).spec \
            mksite.txt mksite.sh mksite.pl \
            doc/*.htm doc/*.gif test*/*.htm \
            selftest/GNUmakefile \
            selftest/*/GNUmakefile \
            selftest/*/*.htm \
            selftest/*/*.html.test 

diff =  diff
diffs = diff -U1
ignore = formatter
known = -e "/$(ignore)/d"

test : 3 3x

local= /vol/www/htdocs/guidod.homelinux.org/test/
3 : 
	touch test3/_.htm
	$(MAKE) test3.html
3x : 
	touch test3/_.htm
	$(MAKE) test3x.html
33 : 3
	cp test3/*.html test3/*.xml $(local)/

HTMLPAGES= [_A-Za-z0-9-][/_A-Za-z0-9-]*[.]html

test3.html : test3/*.htm test3/*.dbk mksite.sh
	rm -rf test3/DEBUG ; mkdir test3/DEBUG
	cd test3 && sh ../mksite.sh site.htm
	sed -e "s|href=\"\\($(HTMLPAGES)\"\\)|href=\"test3/\\1|" \
	    test3/index.html > $@
	sleep 3 # done $@
test3x.html : test3/*.htm test3/*.dbk mksite.pl GNUmakefile
	test ! -d test3x/ || rm -r test3x/* ; mkdir test3x ; test -d test3x
	cp -a test3/*.htm test3/*.dbk test3/*.css test3x/
	- rm test3x/*.print.* ; mkdir test3x/DEBUG
	cd test3x && perl ../mksite.pl site.htm
	sed -e "s|href=\"\\($(HTMLPAGES)\"\\)|href=\"test3x/\\1|" \
	    test3x/index.html > $@
	sleep 2 # done $@

doc.html : doc/*.htm mksite.sh
	cd doc && sh ../mksite.sh features.htm    "-VERSION=$(VERSION)"
	cd doc && sh ../mksite.sh site.htm        "-VERSION=$(VERSION)"
	sed -e "s|href=\"\\($(HTMLPAGES)\"\\)|href=\"doc/\\1|" \
	    doc/index.html > $@
	sleep 5 # done $@

print.html : doc/*.htm mksite.sh
	cd doc && sh ../mksite.sh site.htm -print "-VERSION=$(VERSION)"
	sed -e "s|href=\"\\($(HTMLPAGES)\"\\)|href=\"doc/\\1|" \
	    doc/index.print.html > $@

index.html : doc.html
	cp doc.html index.html

ff :
	cd doc && sh ../mksite.sh features.htm

clean : 
	- rm *.html */*.html */*.tmp

distdir = $(PACKAGE)-$(VERSION)
distdir :
	test -d $(distdir) || mkdir $(distdir)
	- $(MAKE) distdir-hook
	@ list='$(DISTFILES)' ; for dat in $$list $(EXTRA_DIST) \
	; do dir=`echo "$(distdir)/$$dat" | sed 's|/[^/]*$$||' ` \
	; test -d $$dir || mkdir $$dir ; echo cp -p $$dat $(distdir)/$$dat \
	; cp -p $$dat $(distdir)/$$dat || exit 1 ; done
	@ list='$(SUBDIRS)' ; for dir in $$list $(DIST_SUBDIRS) \
	; do $(MAKE) -C "$$dir" distdir "distdir=../$(distdir)/$$dir" \
	|| exit 1 ; done
	- chmod -R a+r $(distdir)
dist : distdir
	tar cvf $(DISTNAME).tar $(distdir)
	rm -r $(distdir)
	- bzip2 -9 $(DISTNAME).tar
	test -d $(SAVETO) || mkdir $(SAVETO)
	mv $(DISTNAME).tar* $(SAVETO)
snapshot : distdir
	find $(distdir) -name \*.gif -exec rm {} \;
	tar cvf $(SNAPSHOTNAME).tar $(distdir)
	rm -r $(distdir)
	- bzip2 -9 $(SNAPSHOTNAME).tar
	test -d $(SAVETO) || mkdir $(SAVETO)
	mv $(SNAPSHOTNAME).tar* $(SAVETO)

.FORCE :

# -----------------------------------------------------------------------
SUMMARY = The mksite.sh documentation formatter
RPM_MAKE_ARGS = prefix=%_prefix DESTDIR=%buildroot VERSION=%version
distdir-hook : $(PACKAGE).spec
$(PACKAGE).spec : $(distdir)
	echo "Name: $(PACKAGE)" > $@
	echo "Summary: $(SUMMARY)" >> $@
	echo "Version: $(VERSION)" >> $@
	echo "Release: 1" >> $@
	echo "License: ZLIB" >> $@
	echo "Group: Productivity/Networking/Web" >> $@
	echo "URL: http://zziplib.sf.net/mksite" >> $@
	echo "BuildRoot:  /tmp/%{name}-%{version}" >> $@
	echo "Source: $(DISTNAME).tar.bz2" >> $@
	echo " " >> $@
	echo "%package sh" >> $@
	echo "Summary: $(SUMMARY) script" >> $@
	echo "Group: Productivity/Networking/Web" >> $@
	echo "Provides: mksitesh " >> $@
	echo "%package doc" >> $@
	echo "Summary: $(SUMMARY) webpages" >> $@
	echo "Group: Productivity/Networking/Web" >> $@
	echo "BuildRequires: perl" >> $@
	echo " " >> $@
	echo "%description" >> $@
	head -6 mksite.sh | sed -e "s/^../    /" >> $@
	echo "%description sh" >> $@
	head -6 mksite.sh | sed -e "s/^../    /" >> $@
	echo "%description doc" >> $@
	head -6 mksite.sh | sed -e "s/^../    /" >> $@
	echo " " >> $@
	echo "%prep" >> $@
	echo "%setup -q" >> $@
	echo "%build" >> $@
	echo "make $(RPM_MAKE_ARGS)" >> $@
	echo "%install" >> $@
	echo "make install-data $(RPM_MAKE_ARGS)" >> $@
	echo "make install-programs $(RPM_MAKE_ARGS)" >> $@
	echo "%clean" >> $@
	echo "rm -rf %buildroot" >> $@
	echo "%files sh" >> $@
	echo "%_bindir/*" >> $@
	echo "%files doc" >> $@
	echo "%_datadir/*" >> $@

rpm : dist
	rpmbuild -ta --target noarch $(SAVETO)/$(DISTNAME).tar.bz2

# -----------------------------------------------------------------------
INSTALLDIRS =
INSTALLFILES = doc/*.html doc/site.htm doc/site.print.htm doc/mksite-icon.gif \
               mksite.sh mksite.txt COPYING.ZLIB 
INSTALLTARBALL = _dist/$(DISTNAME).tar.bz2
INSTALLPROGRAMS = mksite.sh mksite.pl

prefix  ?= /usr
datadir ?= $(prefix)/share
bindir  ?= $(prefix)/bin
mkinstalldirs ?= mkdir -p
INSTALL_DATA ?= cp -p
INSTALL_SCRIPT ?= cp -p
htmpath = groups/z/zz/zziplib/htdocs/mksite
docdir = $(datadir)/$(htmpath)
install-data : doc.html # $(htm_FILES:.htm=.html)
	$(mkinstalldirs) $(DESTDIR)$(docdir)
	@ for i in $(INSTALLDIRS) ; do test -d $$i || continue \
	; echo cp -pR "$$i" $(DESTDIR)$(docdir) \
	; cp -pR $$i $(DESTDIR)$(docdir) \
	; chmod -R a+r $(DESTDIR)$(docdir)/$$i \
	; done
	find $(DESTDIR)$(docdir) -name "*~" -exec rm {} \;
	find $(DESTDIR)$(docdir) -name "*.tmp" -exec rm {} \;
	$(INSTALL_DATA) $(INSTALLFILES) $(DESTDIR)$(docdir)
install-tarball :
	$(mkinstalldirs) $(DESTDIR)$(docdir)
	$(INSTALL_DATA) $(INSTALLTARBALL) $(DESTDIR)$(docdir)
install-programs : 
	$(mkinstalldirs) $(DESTDIR)$(bindir)
	$(INSTALL_SCRIPT) $(INSTALLPROGRAMS) $(DESTDIR)$(bindir)
install : install-data install-tarball
	echo "# skipped  make install-programs"

WWWNAME= guidod
WWWHOST= shell.sourceforge.net
WWWPATH= /home/$(htmpath)
TMPPATH=/tmp/$(PACKAGE)-$(VERSION)
preload :
	$(MAKE) install docdir=$(TMPPATH)
upload : preload
	$(MAKE) install docdir=$(TMPPATH)
	- ssh $(WWWNAME)@$(WWWHOST) mkdir $(WWWPATH)/
	- scp -r $(TMPPATH)/* $(WWWNAME)@$(WWWHOST):$(WWWPATH)/
	rm -r $(TMPPATH)

uploads :
	test -s "$(file)"
	- scp "$(file)" $(WWWNAME)@$(WWWHOST):$(WWWPATH)/$(file)

check : selftest.pl selftest.sh
selftest.pl :
	$(MAKE) -C selftest "MKSITE=perl ../../mksite.pl"
selftest.sh :
	$(MAKE) -C selftest "MKSITE=sh ../../mksite.sh"
