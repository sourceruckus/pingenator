AC_PREREQ([2.65])

m4_define(pingenator_major_version, 0)
m4_define(pingenator_minor_version, 4)
m4_define(pingenator_micro_version, 0)
m4_define(pingenator_devtag, [dev])
m4_ifnblank(pingenator_devtag,
	[m4_define(pingenator_version, pingenator_major_version.pingenator_minor_version.pingenator_micro_version-pingenator_devtag)],
	[m4_define(pingenator_version, pingenator_major_version.pingenator_minor_version.pingenator_micro_version)])

AC_INIT([Pingenator], [pingenator_version], [veggiemike@sourceruckus.org], [pingenator])
AC_CONFIG_AUX_DIR([.])
AC_CONFIG_MACRO_DIR([m4])

AC_SUBST(PINGENATOR_MAJOR_VERSION, pingenator_major_version)
AC_SUBST(PINGENATOR_MINOR_VERSION, pingenator_minor_version)
AC_SUBST(PINGENATOR_MICRO_VERSION, pingenator_micro_version)
AC_SUBST(PINGENATOR_DEVTAG, pingenator_devtag)

AM_INIT_AUTOMAKE([dist-xz no-dist-gzip tar-pax foreign subdir-objects color-tests])

AC_PROG_INSTALL
AC_PROG_MKDIR_P
AC_PROG_LN_S


# check for python
#
# FIXME: need to double check functionality used and get this
#        correct...  original development was on Ubuntu 18.04, which
#        had python 3.6, so that's a starting point.  I do recall
#        wanting to use something that needed 3.7 or 3.8... but I
#        don't remember what it was.
#
min_python=3.6.0
AM_PATH_PYTHON($min_python)

# check for required python modules
#
# NOTE: By setting min_python above, we *should* have all these.  But
#       let's double-check just to be absolutely sure.  Specifically,
#       lzma could have been left out of the system's Python
#       compilation if the lzma libraries were missing.
#
AC_DEFUN([NEEDED_MODS],
  [dnl NOTE: comment out via dnl inside AC_DEFUN
   argparse
   atexit
   math
   multiprocessing
   os
   signal
   subprocess
   sys
  ])
m4_foreach_w([M], NEEDED_MODS,
  [AS_IF([test "M" != ""],
     AM_CHECK_PYMOD(M, [],
       [],
       [AC_MSG_ERROR([Missing required Python module: "M"])]))
  ])


# check for sed /w --in-place
#
# NOTE: Using sed -i in makefiles is frowned upon because it's not
#       portable.  We make 'sed -i' a requirement and check for it here
#       because... I like it and I think pretty much everyone has it.  But
#       I do want to get bonked upside the head if it's going to cause
#       problems.
#
AC_PROG_SED
AS_TMPDIR(pingenator-)
AC_CACHE_CHECK([whether $ac_cv_path_SED supports -i], [pingenator_cv_sed_i],
  [AS_IF([touch $tmp/sed && $ac_cv_path_SED -i 's|||' $tmp/sed 2>/dev/null],
      pingenator_cv_sed_i=yes,
      pingenator_cv_sed_i=no)])
rm -f $tmp/sed && rmdir $tmp
if test $pingenator_cv_sed_i != yes; then
    AC_MSG_ERROR([Pingenator requires sed -i])
fi

# check for needed programs
AC_DEFUN([NEEDED_PROGS],
	 [dnl needed at build-time
	  help2man
	  dnl needed at run-time
	  ping
	  ip
	 ])

m4_foreach_w([P], NEEDED_PROGS,
  [AC_PATH_PROG(m4_bpatsubst(m4_toupper(P), [\.], [_]), P)
   AS_IF([test "$m4_bpatsubst(m4_toupper(P), [\.], [_])" = ""],
     AC_MSG_ERROR([Could not find P]))
  ])


# FIXME: check ping for -w and -c?

AC_CONFIG_FILES([Makefile
		 src/Makefile
		 src/pingenatorconf.py
		 ])
AC_OUTPUT


# Create a fully expanded variable out of one with embedded path variables.
#
# NOTE: This might not be the best way to fully expand the variable, but it
#       works for me.  It might be portable.  It might not.  Fingers
#       crossed.
#
# NOTE: This only works if we make sure $prefix has been set already...
#       otherwise we end up with NONE in our path.
#
expandit()
{
  test "$prefix" = "NONE" && prefix=/usr/local
  result=$1
  while :; do
    temp=`eval echo $result`
    test "$temp" = "$result" && break
    result=$temp
  done
  echo $result
}


echo
echo
echo
echo "------------------------------------------------------------------------"
echo "$PACKAGE_STRING"
echo "------------------------------------------------------------------------"
echo
echo "Configuration Options Summary:"
echo
echo "Python:"
echo "  Interpreter..........: $PYTHON"
echo "  Version..............: $PYTHON_VERSION"
echo
echo "Installation:"
echo "  prefix...............: $prefix"
echo "  pyexecdir............: `expandit $pyexecdir`"
echo
