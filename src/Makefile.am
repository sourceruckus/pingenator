dist_bin_SCRIPTS =
dist_bin_SCRIPTS += pingenator

pkgdata_DATA =
pkgdata_DATA += pingenatorconf.py

install-exec-hook:
	sed -i 's|^#!.*|#!$(PYTHON)|' $(DESTDIR)$(bindir)/pingenator
	sed -i 's|__DEV__||' $(DESTDIR)$(bindir)/pingenator
	sed -i 's|__PKGDATADIR__|$(pkgdatadir)|' $(DESTDIR)$(bindir)/pingenator
