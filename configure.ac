dnl Process this file with autoconf to produce a configure script.

AC_PREREQ([2.63])

AC_INIT([libmodplug],[0.8.10.0])
AC_CONFIG_SRCDIR([Makefile.am])

AM_INIT_AUTOMAKE
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES])
AC_CONFIG_HEADERS([src/config.h])
AC_CONFIG_MACRO_DIR([m4])
AM_MAINTAINER_MODE([enable])

dnl Checks for programs.
AC_PROG_CC
AC_PROG_CXX
AC_LANG([C++])
AC_C_BIGENDIAN

LT_INIT([win32-dll disable-static])

AC_CHECK_HEADERS([inttypes.h stdint.h malloc.h strings.h])

CXXFLAGS="$CXXFLAGS -fno-exceptions -Wall -ffast-math -fno-common -D_REENTRANT"

AC_CANONICAL_HOST

LIBS_PRIVATE=-lstdc++
LIBM=
case "${host_os}" in
dnl Djgpp has all c89 math funcs in libc.a
*djgpp)
  ;;
dnl These systems don't have libm or don't need it (list based on libtool)
darwin*|haiku*|beos*|cegcc*|pw32*)
  ;;
dnl MinGW and Cygwin don't need libm, either
mingw*|cygwin*)
  LIBS_PRIVATE="-luser32 ${LIBS_PRIVATE}"
  ;;
dnl All others:
*) AC_CHECK_LIB(m, pow, LIBM="-lm")
  if test x$LIBM != x; then
    LIBS="${LIBS} ${LIBM}"
    LIBS_PRIVATE="${LIBS_PRIVATE} ${LIBM}"
  fi
  ;;
esac
AC_SUBST(LIBS_PRIVATE)
AC_CHECK_FUNCS(sinf)

# symbol visibility
ac_save_CXXFLAGS="$CXXFLAGS"
CXXFLAGS="$CXXFLAGS -fvisibility=hidden -Werror"
AC_CACHE_CHECK([if compiler supports visibility attributes],[libmodplug_cv_gcc_visibility],
	AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[void foo(void);
__attribute__((visibility("default"))) void foo(void) {}]], [])],
		[libmodplug_cv_gcc_visibility=yes],
		[libmodplug_cv_gcc_visibility=no])
	)
# we want symbol -fvisibility for elf targets, however it works
# with darwin/macho too.  other than that, windows, dos and os2
# do not need it:  for any such targets, the -Werror switch is
# supposed to fail the above check. (I'm adding the manual test
# below nonetheless, just in case.)
case $host_os in
   mingw*|cygwin*|emx*|*djgpp)
	libmodplug_cv_gcc_visibility=no
	;;
esac
CXXFLAGS="$ac_save_CXXFLAGS"
if test $libmodplug_cv_gcc_visibility = yes ;then
   CXXFLAGS="$CXXFLAGS -DSYM_VISIBILITY -fvisibility=hidden"
fi

case ${target_os} in
	*sun* | *solaris*)
		CXXFLAGS="$CXXFLAGS -fpermissive"
		;;
esac

AC_ARG_ENABLE([cxx_interface],
    [AS_HELP_STRING([--enable-cxx-interface],
                    [export C++ interface from library [[default=yes]]])],,
    [enable_cxx_interface=yes])
if test x$enable_cxx_interface != xyes; then
    AC_DEFINE(NO_CXX_EXPORTS, 1, [Do not export the C++ interface.])
fi
AM_CONDITIONAL(EXPORT_CXX, test x$enable_cxx_interface = xyes)

AC_ARG_ENABLE([midi],
    [AS_HELP_STRING([--enable-midi],
                    [compile with midi formats support [[default=no]]])],,
    [enable_midi=no])
if test x$enable_midi = xyes; then
    AC_DEFINE(MIDIFMT_SUPPORT, 1, [Enable midi formats support.])
fi

AC_ARG_ENABLE([wav],
    [AS_HELP_STRING([--enable-wav],
                    [compile with wav formats support [[default=no]]])],,
    [enable_wav=no])
if test x$enable_wav = xyes; then
    AC_DEFINE(WAV_SUPPORT, 1, [Enable wav formats support.])
fi

AC_ARG_ENABLE([mmcmp],
    [AS_HELP_STRING([--enable-mmcmp],
                    [compile with mmcmp and pp20 decompression support [[default=yes]]])],,
    [enable_mmcmp=yes])
if test x$enable_mmcmp = xyes; then
    AC_DEFINE(MMCMP_SUPPORT, 1, [Enable mmcmp and pp20 decompression support.])
fi

# portable types. requires autoconf 2.60
# `configure' will check if these are defined in system headers.
# if not, it will auto-detect and define them in `config.h'
AC_TYPE_INT8_T
AC_TYPE_UINT8_T
AC_TYPE_INT16_T
AC_TYPE_UINT16_T
AC_TYPE_INT32_T
AC_TYPE_UINT32_T
AC_TYPE_INT64_T
AC_TYPE_UINT64_T

MODPLUG_LIBRARY_VERSION=1:0:0

AC_SUBST(MODPLUG_LIBRARY_VERSION)

AC_CONFIG_FILES([Makefile
	  src/Makefile
	  libmodplug.pc])
AC_OUTPUT
