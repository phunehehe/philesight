Summary
=======

Philesight is a tool to browse your filesystem and see where the diskspace is
being used at a glance. Philesight is implemented as a simple command line
program that generates PNG files; a wrapper CGI script is supplied to allow
navigating through the filesystem.

Philesight is actually a clone of the filelight program. Wheres filelight is
ment as an interactive, user friendly application for the X-windows desktop,
philesight is designed to run on a remote server without graphical user
interface.


Usage
=====

See https://gist.github.com/phunehehe/5097331 for instructions specific to Ubuntu.

Philesight can be run in different modes: first, the filesystem is indexed
and the results are stored in a database. When the database is generated, the
tool can used to generate PNG files with the graphs. The database should be
updated every once in a while of course.

 * Index bulding:

    ./philesight --db {db} --index {path}

   {db} is the name of the database file that will be generated. It is a good
   idea to throw away existing database files before indexing to avoid removed
   files showing in your graph.

   {path} is the top-level directory to start indexing. Usually, '/' is a
   good choice.

   This process might take some time, since it traverses the whole tree
   from path-to-index downward and stores the data into the db. Make
   sure to remove previous database files before indexing.

   You can save on index time and size by using the option '--only-dirs'. This
   will not store all individual files into the index, only the directory
   totals. You might want to use this for indexing multi-TB disks.

 * PNG generating: In this mode, philesight generates a graph of the
   filesystem from path and 4 levels downward. i

    ./philesight --db {db} --path {path} --draw {png}

   {db} is the filename of the index file that was generated earlier,
   {path} is the directory which should be drawn, and {png} is the filename
   of the to-be-generated PNG image file


 * CGI: Philesight comes with a CGI module that can be run from within a
   web server. Edit the configurable parameters in the top of this file
   to point to the database. Make sure the database file is readable by
   the webserver and the philesight.rb module can be found by the CGI.

   Available options:

   * $path_db: Path to database file.

   * $path_cache: Optional path to a directory used for caching generated
     images. Note that this directory should be writable by the httpd user.
     All images older then $cache_maxage seconds are automatically deleted
     every time the CGI is invoked.

   * $img_size: graph size. 800 pixels is a good choice.

   * $img_rings: number of rings = depth of graph. 3 or 4 should be ok

   * $img_gradients: use gradient colors in graph (set to 'false' to generate
     smaller PNG files)

   * $show_list: render list of directories and their sizes blow graph.




Dependencies
============

Philesight is written in ruby1.8, and requires the berkely-db4 and cairo
ruby libraries.

On debian, the following should be sufficient:

    apt-get install libdb4.2-ruby1.8 libcairo-ruby1.8

On Fedora 10, use

    yum -y update
    yum -y install ruby ruby-cairo ruby-bdb

I've been told the following should work on RedHat/Centos

    yum -y update
    cd /etc/yum.repos.d/
    wget http://centos.karan.org/kbsingh-CentOS-Extras.repo
    wget http://ruby.gfd-dennou.org/products/rpm/RPMS/CentOS/CentOS-DennouRuby.repo
    yum -y --enablerepo=kbs-CentOS-Testing install ruby bitmap-fonts ruby-bdb ruby-cairo


License
=======

This package is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; version 2 dated June, 1991. This package is distributed in the hope
that it will be useful, but WITHOUT ANY WARRANTY; without even the implied
warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
General Public License for more details.

Changelog
=========

2012-04-27  Fixed require of 'set' class, seems to be needed on some
        ruby distributions.

2012-03-22  Added support for proper counting of hard links (thanks
        Kyle Bygott)

2011-10-15  Fixed typo in help text. Fixed escaping in HTML output.

2011-05-17  Added an option for not storing all files in the index, only
        directory totals (--only-dirs), this saves considerable on
        index size. (Thanks Hans Lambermont). Changed size to use
        IEC 60027-2 format (TiB, GiB, MiB, Kib)

2010-11-10  Now possible to specify --skip <path> more then once (thanks
        Lars Gustäbel)

2010-11-05  Flush bugfix

2010-10-04  Flush fd when writing PNG to prevent corrupted PNG output

2009-12-14  Added --one-file-system option (thanks Simon Bland)

2009-02-18  Removed recently added internationalisation support because
        required libraries are not available in major linux distro's

2009-02-12  Fixed code for ruby 1.9.1 compatibility, added support for
        non-latin1 filenames. (thanks Stanislav Sedov)

2009-01-05  Added --skip option for skipping files while indexing
        Updated documentation for RedHat/Centos dependencies

2008-11-20  Updated documentation; added caching support

2008-11-19  Better escaping of CGI parameters, fixed breaking on '+' in
        pathnames

2008-11-18  Updated documentation for installation on RedHat/Centos

2008-05-29  Added option (use_gradients) to enable/disable gradients
        in circular graph. Added option to set graph size.

2008-04-16  Added optional table with file list to cgi, some bugfixes,
        increased default graph size to 800. (thanks Guillaume Sachot)

2008-03-17  Fixed bug where no image was shown with CGI's running
        on apache-mpm-worker.

2007-03-12  Fixed crash when indexing file named 'rest'

2006-12-09  Workaround for segmentaion fault with ruby 1.8.5


Bugs
====

    * Philesight is a ruby program, and thus is not particulary fast.
    * Indexing could probably be more efficient.
    * Proper error handling is mostly missing.
    * Caching is very untested
