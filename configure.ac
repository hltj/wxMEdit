#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ(2.59)
AC_INIT([wxmedit], [3.1], [wxmedit@gmail.com])
AC_CONFIG_SRCDIR([src/wxmedit_app.cpp])
AC_CONFIG_HEADER([config.h])
AC_PREFIX_DEFAULT([/usr])

AM_INIT_AUTOMAKE([subdir-objects])

AC_USE_SYSTEM_EXTENSIONS
ALL_LINGUAS="de_DE es fr_FR it_IT ja_JP pl_PL ru_RU zh_CN zh_TW"
AM_GNU_GETTEXT([external])
AC_SUBST(ALL_LINGUAS)

# Checks for programs.
AC_PROG_CXX
AC_PROG_INSTALL
AC_LANG(C++)

#CXXFLAGS="-O2"
my_cxxflags_bak="$CXXFLAGS"
CXXFLAGS="$CXXFLAGS -std=c++11"
AC_MSG_CHECKING([whether -std=c++11 is supported])
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])],
	[AC_MSG_RESULT([yes])]
	[my_cxx11_suported="x"],
	[AC_MSG_RESULT([no])]
	[CXXFLAGS="$my_cxxflags_bak"]
)

if test "x$my_cxx11_suported" == "x"; then
my_cxxflags_bak="$CXXFLAGS"
CXXFLAGS="$CXXFLAGS -std=c++0x"
AC_MSG_CHECKING([whether -std=c++0x is supported])
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])],
	[AC_MSG_RESULT([yes])],
	[AC_MSG_RESULT([no])]
	[CXXFLAGS="$my_cxxflags_bak"]
)
fi


# Checks for MacOS
IS_MACOS=
case "${host}" in
    *-*-darwin* | powerpc-apple-macos* )
        IS_MACOS=1
    ;;
esac

# Checks for glib2, gtk3 libraries
#AM_PATH_GLIB_2_0(2.6.0)
#AM_PATH_GTK_3_0(3.24.0)

#if test "x$IS_MACOS" == "x"; then
#fi

GTK_MAJOR=3
AC_ARG_WITH(gtk,
    [  --with-gtk=VERSIOM      use GTK major version number [[default=3]]],
    [GTK_MAJOR=$withval])

PKG_CHECK_MODULES(glib_gtk, [gtk+-$GTK_MAJOR.0 >= $GTK_MAJOR.0.0 glib-2.0 >= 2.6.0])
CXXFLAGS="$CXXFLAGS $glib_gtk_CXXFLAGS -lX11"
LIBS="$LIBS $glib_gtk_LIBS -lX11"
AC_MSG_CHECKING([GTK major version])
AC_MSG_RESULT([$GTK_MAJOR])

PKG_CHECK_MODULES([curl], [libcurl])

# option for additional wxAUI lib
AC_ARG_WITH(aui, 
    [  --with-aui=PATH         use additional wxAUI library path],
    [WXAUI="$withval"])

if test "w$WXAUI" != "x"; then
    CPPFLAGS="$CXXFLAGS -I$WXAUI/include"
    LIBS="$LIBS -L$WXAUI/lib"
fi


# option for additional Boost.Xpressive and Boost.Foreach lib
AC_ARG_WITH(boost-add, 
    [  --with-boost-add=PATH   use additional Boost.Xpressive and Boost.Foreach library path],
    [CXXFLAGS="$CXXFLAGS -I$withval"])

# option for additional Boost lib
AC_ARG_WITH(boost, 
    [  --with-boost=PATH       use additional Boost library path,
                          otherwise use system include path],
    [CXXFLAGS="$CXXFLAGS -I$withval"])

# option for additional ICU lib
ICUCONFIG=icu-config
AC_ARG_WITH(icu, 
    [  --with-icu=PATH         use additional ICU library path,
                          otherwise use system include/library path],
    [ICUCONFIG="$withval/bin/icu-config"])
CPPFLAGS="$CPPFLAGS `$ICUCONFIG --cppflags`"
#CXXFLAGS="$CXXFLAGS `$ICUCONFIG --cxxflags`"
LIBS="$LIBS `$ICUCONFIG --ldflags`"


# Checks for wxWidgets library
WX_LIBS_set="0"
if test "x$WX_LIBS" != "x"; then
    WX_LIBS_set="1"
fi

AM_OPTIONS_WXCONFIG
AM_PATH_WXCONFIG(2.8.0, wxWin=1, ,[core,aui])
if test "$wxWin" != 1; then
   AC_MSG_ERROR([
           wxWidgets must be installed on your system
           but wx-config script couldn''t be found.

           Please check that wx-config is in path, the directory
           where wxWidgets libraries are installed (returned by
           'wx-config --libs' command) is in LD_LIBRARY_PATH or
           equivalent variable and wxWidgets version is 2.8.0.
   ])
fi
CPPFLAGS="$CPPFLAGS $WX_CPPFLAGS"
CXXFLAGS="$CXXFLAGS $WX_CXXFLAGS_ONLY"
#CFLAGS="$CFLAGS $WX_CFLAGS_ONLY"
if test "$WX_LIBS_set" != 1; then
    LIBS="$LIBS $WX_LIBS"
fi

UNICODE_FLAG=$(test ! $($WX_CONFIG_WITH_ARGS - -unicode >/dev/null 2>&1))

AC_MSG_CHECKING([if wxWidgets was built with UNICODE enabled])
if test "$UNICODE_FLAG" != "0"; then
    AC_MSG_RESULT([yes])
else
    AC_MSG_RESULT([no])
    AC_MSG_ERROR([wxWidgets must be built with UNICODE enabled.])
fi



# Checks for header files.
AC_CHECK_HEADERS([boost/config.hpp], [], AC_MSG_ERROR([cannot find required header boost/config.hpp]))
AC_CHECK_HEADERS([boost/xpressive/xpressive_dynamic.hpp], [], AC_MSG_ERROR([cannot find required header boost/xpressive/xpressive_dynamic.hpp]))
AC_CHECK_HEADERS([boost/foreach.hpp], [], AC_MSG_ERROR([cannot find required header boost/foreach.hpp]))
AC_CHECK_HEADERS([unicode/uchar.h], [], AC_MSG_ERROR([cannot find required header unicode/uchar.h]))
AC_CHECK_HEADERS([unicode/unistr.h], [], AC_MSG_ERROR([cannot find required header unicode/unistr.h]))
AC_CHECK_HEADERS([wx/aui/aui.h], [], AC_MSG_ERROR([cannot find required header wx/aui/aui.h]))

# Checks for typedefs, structures, and compiler characteristics.
#AC_HEADER_STDBOOL
AC_C_CONST
AC_C_INLINE
AC_TYPE_SIZE_T

# Checks for library functions.
#AC_FUNC_MALLOC
#AC_HEADER_STDC
AC_CHECK_FUNCS([dup2])

AC_CONFIG_FILES([Makefile syntax/Makefile po/Makefile.in])

AC_OUTPUT
