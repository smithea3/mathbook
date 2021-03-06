## ********************************************************************* ##
## Copyright 2015-7                                                      ##
## Robert A. Beezer, Michael Gage, Geoff Goehle, Alex Jordan             ##
##                                                                       ##
## This file is part of PreTeXt.                                         ##
##                                                                       ##
## PreTeXt is free software: you can redistribute it and/or modify       ##
## it under the terms of the GNU General Public License as published by  ##
## the Free Software Foundation, either version 2 or version 3 of the    ##
## License (at your option).                                             ##
##                                                                       ##
## PreTeXt is distributed in the hope that it will be useful,            ##
## but WITHOUT ANY WARRANTY; without even the implied warranty of        ##
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the         ##
## GNU General Public License for more details.                          ##
##                                                                       ##
## You should have received a copy of the GNU General Public License     ##
## along with PreTeXt. If not, see <http://www.gnu.org/licenses/>.       ##
## ********************************************************************* ##

#######################
# DO NOT EDIT THIS FILE
#######################

#   1) Do make a copy of Makefile.paths.original
#      as Makefile.paths
#   2) Edit Makefile.paths as directed there
#   3) This file (Makefile) and Makefile.paths.original
#      are managed by revision control and edits will conflict
#   4) See updated history in Makefile.paths.original
#      for changes, or follow the revision control history

##############
# Introduction
##############

# This is not a "true" makefile, since it does not
# operate on dependencies.  It is more of a shell
# script, sharing common configurations

######################
# System Prerequisites
######################

#   install         (system tool to make directories)
#   xsltproc        (xml/xsl text processor)
#   xmllint         (only to check source against DTD)
#   <helpers>       (PDF viewer, web browser, pager, Sage executable, etc)

#####
# Use
#####

#	A) Set default directory to be the location of this file
#	B) At command line:  make <some-target>

# The included file contains customized versions
# of locations of the principal components of this
# project and names of various helper executables
include Makefile.paths

# These paths are subdirectories of
# the Mathbook XML distribution
# MBUSR is where extension files get copied
# so relative paths work properly
MBXSL = $(MB)/xsl
MBUSR = $(MB)/user
WWEXAMPLE = $(MB)/examples/webwork
DTD = $(MB)/schema/dtd

# XML to apply templates to
SMPCHP = $(WWEXAMPLE)/sample-chapter/sample-chapter.xml
MIN = $(WWEXAMPLE)/minimal/mini.xml

# These paths are subdirectories of
# the scratch directory
PGOUT      = $(SCRATCH)/pg
HTMLOUT    = $(SCRATCH)/html
PDFOUT     = $(SCRATCH)/pdf
EPUBOUT    = $(SCRATCH)/epub

# Some aspects of producing these examples require a WeBWorK server.
# For all but trivial testing or examples, please look into setting
# up your own WeBWorK server, or consult Alex Jordan about the use
# of PCC's server in a nontrivial capacity.    <alex.jordan@pcc.edu>
SERVER = https://webwork.pcc.edu

#  Write out each WW problem of the "sample-chapter" as a
#  standalone problem in PGML ready for use on a WW server.
#  "def" files and "header" files are produced. Directories
#  and filenames are derived from titles of chapters, sections,
#  etc., in addition to the titles of the problems themselves.
#
#  level 1 here partitions by sections of the sample chapter
#
#  Results land in the subdirectory:  $(PGOUT)/local
#
sample-chapter-extract:
	install -d $(PGOUT)
	cd $(PGOUT); \
	xsltproc --stringparam chunk.level 1 $(MBXSL)/mathbook-webwork-archive.xsl $(WWEXAMPLE)/sample-chapter/sample-chapter.xml

# HTML with WeBWorK exercises need a WW server
# See example below for how to specify this
# Output is:  $(HTMLOUT)/sample-ww-chapter.html
sample-chapter-html:
	install -d $(HTMLOUT)
	-rm $(HTMLOUT)/*.html
	-rm $(HTMLOUT)/knowl/*.html
	cp -a $(WWEXAMPLE)/sample-chapter/images $(HTMLOUT)
	cd $(HTMLOUT); \
	xsltproc --stringparam webwork.server $(SERVER) --stringparam html.knowl.exercise.inline no $(MBXSL)/mathbook-html.xsl $(WWEXAMPLE)/sample-chapter/sample-chapter.xml

# a one-time prerequisite for LaTeX conversion
# problems living on a server, and image construction at server
# our "webwork-tex" is a subdirectory of where the PDF is compiled
# -s specifies an existing WW server to use (ignore security warnings)
sample-chapter-server-tex:
	install -d $(PDFOUT)/webwork-tex
	$(MB)/script/mbx -v -c webwork-tex -s $(SERVER) -d $(PDFOUT)/webwork-tex $(WWEXAMPLE)/sample-chapter/sample-chapter.xml

# LaTeX version of the sample chapter
# see prerequisite just above
# the "webwork-tex" directory must be given here
# [note trailing slash (subject to change)]
sample-chapter-latex:
	install -d $(PDFOUT)
	-rm $(PDFOUT)/*.tex
	cd $(PDFOUT); \
	xsltproc --stringparam webwork.server.latex $(PDFOUT)/webwork-tex/ $(MBXSL)/mathbook-latex.xsl $(WWEXAMPLE)/sample-chapter/sample-chapter.xml; \
	xelatex sample-ww-chapter.tex; \
	xelatex sample-ww-chapter.tex

# HTML for a minimal WeBWorK cell
# Useful for debugging
# Output is:  $(HTMLOUT)/webwork-mini.html
webwork-mini-html:
	install -d $(HTMLOUT)
	-rm $(HTMLOUT)/*.html
	-rm $(HTMLOUT)/knowl/*.html
	cd $(HTMLOUT); \
	xsltproc --stringparam webwork.server $(SERVER) --stringparam html.knowl.exercise.inline no $(MBXSL)/mathbook-html.xsl $(WWEXAMPLE)/minimal/mini.xml

###########
# Utilities
###########

# Verify Source integrity
#   Leaves "jingreport.txt" in SCRATCH
#   Automatically invokes the "less" pager, could configure as $(PAGER)
sample-chapter-check:
	install -d $(SCRATCH)
	-rm $(SCRATCH)/jingreport.txt
	-java -classpath $(JINGTRANG) -Dorg.apache.xerces.xni.parser.XMLParserConfiguration=org.apache.xerces.parsers.XIncludeParserConfiguration -jar $(JINGTRANG)/jing.jar $(MB)/schema/pretext.rng $(SMPCHP) > $(SCRATCH)/jingreport.txt
	less $(SCRATCH)/jingreport.txt

webwork-mini-check:
	install -d $(SCRATCH)
	-rm $(SCRATCH)/jingreport.txt
	-java -classpath $(JINGTRANG) -Dorg.apache.xerces.xni.parser.XMLParserConfiguration=org.apache.xerces.parsers.XIncludeParserConfiguration -jar $(JINGTRANG)/jing.jar $(MB)/schema/pretext.rng $(MINI) > $(SCRATCH)/jingreport.txt
	less $(SCRATCH)/jingreport.txt
