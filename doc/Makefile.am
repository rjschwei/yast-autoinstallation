#
# Makefile.am for y2c_autoinst/doc
#
# $Id$
#

SUBDIRS = xml entities bin examples
#htmldir = $(docdir)/html
EXTRA_DIST = ENV*

#html_DATA = html
#doc_DATA = pdf


#html:
#	$(MAKE) -C xml html

#pdf:
#	$(MAKE) -C xml pdf

pkgsrc:
	$(MAKE) -C xml pkgsrc

#html-debug:
#	$(MAKE) -C xml pkgsrc

bigfile:
	$(MAKE) -C xml ay_bigfile.xml

clean:
	$(MAKE) -C xml clean
	rm -rf build
