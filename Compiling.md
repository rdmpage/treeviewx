These instructions are relevent if you want to compile the program yourself, rather than use one of the binaries. The instructions are partly intended as notes to myself to remind me how I did various things. One of the joys of supporting multiple platforms is trying to get to grips with the individual quirks.

# Install wxWidgets #

wxWidgets is an open source cross-platform C++ library.

## Unix (including Linux and Mac OS X) ##

### Step 1: Build and install wxWidgets ###

You can get the latest stable and development releases of wxWidgets from [wxWidgets.org](http://www.wxwidgets.org).

I build the static libraries for wxWidgets to make it easier to distribute the binaries. Hence, the Mac OS X XCode project file assumes you have static libraries (you could build the Linux version with either static or dynamic libraries). To build the static libraries you can do the following:
```
./configure --enable-static=yes
```
Thereafter it's the usual story:
```
make
make install
```
with the install step requiring you to be superuser.

### Step 2: Compile SVG support ###

After building and installing wxWidgets, go to the contrib/src/svg folder in the root of the wxWidgets folder and make and install dcsvg:
```
cd contrib/src/svg
make
make install
```
This adds support for SVG files for creating picture files.

### Update library ###

On some systems (such as Linux) you should run ldconfig to update the library locations. You will need to do this as superuser:
```
	su
	/sbin/ldconfig
```

You may find that this isn't enough, especially on a new installation of Linux (for example, I had this problem on Fedora Core 13). In this case, you may need to add the line
```
/usr/local/lib
```
to the file /etc/ld.so.conf

## Windows ##
This assumes we are using Borland's free C++ compiler, and that we are using wxWindows as a DLL (faster linking times compared to static version). Hence, download and install wxWidgets source, then compile as a DLL using Borland C++ 5.5
```
make -f makefile.b32 DLL=1
```

# Compile program #

## Mac OS X ##
Ultimately I hope to be able to build TreeView using the GNU make tools, but I'm not quite there yet. Meantime, TreeView X can be built using the XCode project file tv.xcode supplied with the Mac OS X source code. You can also use Project Builder on Mac 10.2, but the results aren't quite so pretty.

## Unix ##
Follow the usual steps for a building a Unix program:
```
./configure
make
make install
```

You can then run the program by typing
```
tv
```
at the command line.

## Windows ##
The build system for Windows is not quite automatic yet, so you need to do the following:

### Build the NEXUS class library ###
```
cd ncl-2.0/src
make -f makefile.b32	
```

### Build the expatpp XML library ###
```
cd expatpp
make -f makefile.b32	
```
### Go to the root of the source directory and make tv itself ###
```
make -f makefile.b32
```

# Packaging for distribution #

Instructions for building packages for installation.

## Source for Linux/Unix/Mac OS X ##
Just go
```
make dist
```
to build a gzipped tarball of the sources.

## Mac OS X binary ##
You could just stuff the executable, but I create a dmg file using [DropDMG](http://c-command.com/dropdmg/). Simply drop the application on the DropDMG icon to create the dmg file.

## RPMs for Linux ##
This assumes you've set up the ~/.rpmmacros file in your home directory, e.g. see http://www.kayos.org/download/rpm-tutorial.html. My file is
```
%packager      Roderic D. M. Page 
%distribution  Roderic D. M. Page
%vendor        Roderic D. M. Page

%_topdir       /home/rpage/rpm
```
  1. Make a tarball of the sources
```
make dist
```
  1. Copy tarball (e.g., tv-0.4.1.tar.gz) to $HOME/rpm/SOURCES
  1. Copy tv.spec to $HOME/rpm/SPECS
  1. To build RPM
```
cd $HOME/rpm/SPECS
rpmbuild -ba tv.spec
```

To test, uninstall program and then install the RPM (as superuser).
```
	rpm -i tv-0.4-1.i386.rpm
```

## Windows ##
### Windows source ###

Make sure you have zip installed and on your path (you can get a copy from http://www.info-zip.org/Zip.html or one of its mirrors, such as http://www.mirror.ac.uk/sites/ftp.info-zip.org/pub/infozip/).

  1. List all relevant source, resource, and make files are listed in tv.rsp.
  1. Run the batch file makedist.bat

### Windows binary ###

I use Inno Setup from http://www.jrsoftware.org/isinfo.php We need an installer to copy across the DLLs required, as well as create the necessary keys in the Windows registry to associate the correct files with TreeView X. The installer also provide the ability to uninstall the program.

  1. Open the file tv.iss in Inno Setup.
  1. Make sure that you distribute both the wxWindows DLL (found in the lib directory of your wxWindows installation, e.g., wx24d\_bcc.dll) and the Borland C++ runtime (cc3250mt.dll, found in the bin directory of your Borland installation, e.g. c:\borland\bcc55\bin). These are set in the distribution version of tv.iss, but you may have to alter the paths for your installation.