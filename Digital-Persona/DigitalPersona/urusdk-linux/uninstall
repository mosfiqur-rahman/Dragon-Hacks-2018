#!/bin/sh

# This script can be used to uninstall DigitalPersona U.are.U SDK
# packages.

# Check root
if [ "`whoami`" != "root" ]; then
  echo "You must be root to uninstall this product."
  echo ""
  exit
fi

# Root of SDK and Linux installation directories
SDKDIR=/opt/DigitalPersona/urusdk-linux
LINUXDIR=$SDKDIR/Linux

if [ -f $SDKDIR/redist/init-script-functions ]; then
	. $SDKDIR/redist/init-script-functions
else
	echo "File $SDKDIR/redist/init-script-functions is"
	echo "missing. Make sure DigitalPersona U.are.U SDK for Linux is properly"
	echo "installed."
	echo ""
	exit 1
fi

echo "Removing all the objects and executables:"
MAKEDIRS=" \
  $LINUXDIR/Samples/UareUCaptureOnly \
  $LINUXDIR/Samples/UareUSample \
  $LINUXDIR/Samples/UareUSampleJava \
  $LINUXDIR/Samples/UareUSampleJavaPOS \
  $LINUXDIR/drivers/source/usbdpfp"
  
for d in ${MAKEDIRS}; do
  if [ -f "$d/Makefile" ]; then
    make clean -C "$d"
    echo " "
  fi
done


echo "Removing links in /usr/lib and /usr/include..."
LINKS=`find /usr/lib*/* -maxdepth 0 -lname '/opt/DigitalPersona/urusdk-linux/Linux/*'`
if [ -n "$LINKS" ]; then
  rm $LINKS 2>/dev/null
fi

if [ -L /usr/include/DigitalPersona ]; then
  rm /usr/include/DigitalPersona
fi

echo "Done"

echo "Removing DigitalPersona kernel module:"
if [ -s /etc/init.d/usbdpfp-modprobe ]; then
  LINKS=`find /etc/rc*/* -maxdepth 0 -lname '../init.d/usbdpfp-modprobe'`
  rm $LINKS /etc/init.d/usbdpfp-modprobe
fi

DRIVER=`find /lib/modules -name mod_usbdpfp.ko`
if [ "$DRIVER" != "" ]; then
  modprobe -r -q mod_usbdpfp 2>/dev/null
  rm $DRIVER
  /sbin/depmod
fi

if [ -s /etc/udev/rules.d/40-usbdpfp.rules ]; then
  rm /etc/udev/rules.d/40-usbdpfp.rules
fi

if [ -s /etc/udev/rules.d/99-dpuvc.rules ]; then
  rm /etc/udev/rules.d/99-dpuvc.rules
fi

echo "Removing usbdpfp:"
cleanup_init       usbdpfp
remove_init_script usbdpfp

CONTENTFILE="$SDKDIR/.filelist-uareu"
CONTENT=""
OLDPATH=`pwd`
cd /opt
if [ -f $CONTENTFILE ]; then
  CONTENT=`sort -sr $CONTENTFILE`
  for i in $CONTENT; do
    if [ -d $i ]; then
      rmdir $i 2>/dev/null
    else
      rm -f $i
    fi
  done
  rm $CONTENTFILE
fi
cd $OLDPATH

if [ -z "$CONTENT" ]; then
  echo ""
  echo "Files under /opt/DigitalPersona/urusdk-linux were not installed using the"
  echo "SDK installer. You can manually remove /opt/DigitalPersona/urusdk-linux"
  echo "directory if needed."
  echo ""
fi

echo "Clean up /opt/DigitalPersona/urusdk-linux:"
if [ -h $LINUXDIR/lib/lib ]; then
  rm $LINUXDIR/lib/lib
fi
if [ -d $LINUXDIR/lib ]; then
  rmdir $LINUXDIR/lib
fi
if [ -L $LINUXDIR/bin/dpuvccfg ]; then
  rm $LINUXDIR/bin/dpuvccfg
fi
if [ -d $LINUXDIR/bin ]; then
  rmdir $LINUXDIR/bin
fi

if [ -d $LINUXDIR/drivers ]; then
  if [ "`ls -a $LINUXDIR/drivers | wc -l`" = "2" ]; then
    rmdir $LINUXDIR/drivers
  fi
fi

cd /opt

if [ -d $LINUXDIR/scripts ]; then
  cd $LINUXDIR/scripts
  rm 40-usbdpfp.rules usbdpfp
  cd /opt
  if [ "`ls -a $LINUXDIR/scripts | wc -l`" = "2" ]; then
    rmdir $LINUXDIR/scripts
  fi
fi

if [ -d $LINUXDIR/redist ]; then
  rm -rf $LINUXDIR/redist
fi

if [ -d $LINUXDIR ]; then
  if [ "`ls -a $LINUXDIR | wc -l`" = "2" ]; then
    rmdir $LINUXDIR
  fi
fi

if [ -f $SDKDIR/uninstall ]; then
  rm $SDKDIR/uninstall
fi

if [ "`ls -a $SDKDIR | wc -l`" = "2" ]; then
  rmdir $SDKDIR
fi

if [ "`ls -a /opt/DigitalPersona | wc -l`" = "2" ]; then
  rmdir /opt/DigitalPersona
fi

echo "Done"

