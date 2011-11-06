#
# Makefile.am for y2c_autoinst/doc
#
# $Id: Makefile.am 63765 2011-04-11 15:40:31Z ug $
#

SUBDIRS = images
htmldir = $(docdir)/html

xml_file = autoyast.xml
xml_files =  $(wildcard *.xml)
daps_ent_files = entity-decl.ent network-decl.ent

EXTRA_DIST = $(xml_files) $(daps_ent_files) $(wildcard *.xsl) \
  web/default.css bin entities examples

CLEANFILES = .html.sum  profile.dtd.xml elements \
  elements.xml elements.ent profile.dtd.xml examples.ent\
  components.ent .ps.sum  autoyast.out autoyast.pdf autoyast.fo \
  images.ent \
  ay_bigfile_docbook.xml ay_bigfile_novdoc.xml ay_bigfile.xml

STYLESHEET_CSS = default.css

html_DATA = $(wildcard html/*.html) \
	    html/index.html \
	    html/yast2docs.css \
		html/default.css

#doc_DATA = autoyast.pdf

components: components.ent
	bin/components.sh noref > components.ent

examples: examples.ent
	bin/examples.sh > examples.ent

html/index.html: autoyast.xml
	XML_CATALOG_FILES=@XML_CATALOG@ \
	time -p  @XSLTPROC@ --xinclude \
	-stringparam chunk.fast '1' \
	--stringparam html.stylesheet $(STYLESHEET_CSS) \
	@STYLESHEET_HTML@ autoyast.xml
	XML_CATALOG_FILES=@XML_CATALOG@ \
	time -p  @XSLTPROC@ --xinclude \
	-stringparam base.dir 'html/devel/' \
	-stringparam chunk.fast '1' \
	--stringparam html.stylesheet $(STYLESHEET_CSS) \
	@STYLESHEET_HTML@ creating_autoyast2_modules.xml
	cd html/devel; \
	ln -s ../default.css ./default.css; \
	cd ../..

html/index.html: autoyast.xml 
	XML_CATALOG_FILES=@XML_CATALOG@ \
	@XSLTPROC@ --xinclude  @STYLESHEET_HTML@ \
	$<

# was: images/PNG/*.png 
prep-daps-images:
	mkdir -p ../images/src/png
	cp ../autoyast2/img/*.png ../images/src/png

ay_bigfile_docbook.xml: prep-daps-images
# Remove internal entity statements
ay_bigfile_docbook.xml: autoyast.xml
	{ head -n 3 $< ; \
	XML_CATALOG_FILES=@XML_CATALOG@ \
	xmllint --xinclude --noent --nocdata -o - $< \
	  | awk 'BEGIN{b=0}; /^]>/ && b == 0 {b=1}; b == 1 {print}' \
	  | sed -e 's|fileref=\"img/|fileref=\"|' \
	        -e 's|<acronym>|<phrase role=\"acronym\">|g' \
	        -e 's|</acronym>|</phrase>|g' \
	        -e 's|<caution>|<!-- role=\"caution\" --><note>|g' \
	        -e 's|</caution>|</note>|g' \
	        -e 's|<literallayout|<!-- role=\"literallayout\" --><screen|g' \
	        -e 's|</literallayout>|</screen>|g' \
	        -e 's|<orderedlist numeration=\"arabic\">|<!-- numeration=\"arabic\" --><orderedlist>|g' \
		-e 's|title id=[^>]*|title|' \
		-e 's|endterm=[^/]*||' \
		-e 's|frame=\"top\"||g' \
		-e "s|frame='top'||g" \
		-e 's|revision=\"1\"||g' \
		-e 's|align=\"center\"||' ; } > $@

ay_bigfile.xml: ay_bigfile_docbook.xml
	xsltproc --output $@ \
	  /usr/share/susedoc/xslt/misc/db2novdoc.xsl $<

# Not in use; use ay_bigfile.xml
ay_bigfile_novdoc.xml: prep-daps-images
ay_bigfile_novdoc.xml: autoyast.xml
	{ echo '<?xml version="1.0" encoding="UTF-8"?>'; \
	  echo '<!DOCTYPE book PUBLIC "-//Novell//DTD NovDoc XML V1.0//EN" "novdocx.dtd"'; \
	  echo '['; \
	  echo '<!ENTITY % NOVDOC.DEACTIVATE.IDREF "INCLUDE">'; \
	XML_CATALOG_FILES=@XML_CATALOG@ \
	xmllint --xinclude --noent --nocdata -o - $< \
	  | awk 'BEGIN{b=0}; /^]>/ && b == 0 {b=1}; b == 1 {print}' \
	  | sed 's|fileref=\"img/|fileref=\"|' ; } > $@

#autoyast.fo: autoyast.xml
#	XML_CATALOG_FILES=@XML_CATALOG@ \
#	@XSLTPROC@ @XSLTPROC_FLAGS@ --xinclude \
#	-o $@ @STYLESHEET_PDF@  $<

#autoyast.fo: autoyast.xml
#	saxon -o autoyast.fo autoyast.xml @STYLESHEET_PDF@

#autoyast.pdf: autoyast.fo
#	fop -q $< $@

autoyast.xml: images.ent examples components

html/yast2docs.css: html/index.html
	cp @STYLESHEET_CSS@ html
	cp -a $(ydatadir)/docbook/images html

html/default.css: html/index.html webimages
	cp web/default.css html

images.ent:
	@make -r -C images/WEB -f makefile.ent ima

: images.ent chunks webimages


webimages:
	mkdir -p html/img
	cp -a images/WEB/*png html/img


.PHONY: images examples.ent components.ent

images: images/EPS/* images/PNG/* 
	@if ! [ -e "img" ]; then \
		mkdir -p img; \
	fi
	@if ! [ -e "img/Makefile" ]; then \
		ln -s ../images/makefile.images img/Makefile; \
	fi

	@echo "generating PNG files for all outputs...";
	@if [ "$(IMAGES)" = "all" ]; then \
		rm -f img/*.{png,pdf}; \
	fi;

	@for j in `bin/findImages.sh` ; do \
		if [ -e "images/EPS/$$j" ]; then \
			source="images/EPS/$$j"; eps="1";\
		elif [ -e "images/PNG/$$j" ]; then \
			source="images/PNG/$$j"; eps="0"; \
		fi; \
		dest=`echo "img/$$j" | sed -e "s|_|-|g;s|.eps||g;s|.png||g;"`; \
		if [ -e "$$source" ]; then \
			if [ ! -f $$dest".png" ] || [ $$dest".png" -ot $$source ]; then \
				if  [ $$eps == "1" ]; then \
					echo "	converting $$source -> $$dest'.pdf'"; \
					bin/epstopdf12 -o=$$dest".pdf" $$source; \
					echo "	converting $$source -> $$dest'.png'"; \
					convert $$source $$dest".png"; \
				else \
					echo "	linking $$source -> $$dest'.png'"; \
					convert -depth 8 $$source $$dest".png"; \
				fi; \
			fi; \
		fi; \
	done
	@make -r -C img  ima


install-data-local:
	install -d -m 755 $(DESTDIR)$(htmldir)
	cp -a $(srcdir)/html/images $(DESTDIR)$(htmldir)
	cp -a $(srcdir)/html/img $(DESTDIR)$(htmldir)
#	cp -a $(srcdir)/html/devel $(DESTDIR)$(htmldir)

clean-local:
	rm -rf html
