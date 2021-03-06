AC_INIT(sshfs-fuse, 2.3)
AM_INIT_AUTOMAKE
AM_CONFIG_HEADER(config.h)

AC_PROG_CC
AM_PROG_CC_C_O
CFLAGS="$CFLAGS -Wall -W"
LIBS=
AC_SEARCH_LIBS(dlsym, [dl])
sshnodelay_libs=$LIBS
AC_SUBST(sshnodelay_libs)
LIBS=

AC_ARG_ENABLE(sshnodelay,
	[  --disable-sshnodelay    Don't compile NODELAY workaround for ssh])

if test -z "$enable_sshnodelay"; then
	AC_MSG_CHECKING([OpenSSH version])
	[eval `ssh -V 2>&1 | sed -n 's/^OpenSSH_\([1-9][0-9]*\)\.\([0-9][0-9]*\).*/ssh_major=\1 ssh_minor=\2/p'`]
	if test "x$ssh_major" != x -a "x$ssh_minor" != x; then
		if test $ssh_major -gt 4 -o \( $ssh_major = 4 -a $ssh_minor -ge 4 \); then
			AC_MSG_RESULT([$ssh_major.$ssh_minor >= 4.4, disabling NODELAY workaround])
			enable_sshnodelay=no
		else
			AC_MSG_RESULT([$ssh_major.$ssh_minor < 4.4, enabling NODELAY workaround])
			enable_sshnodelay=yes
		fi
	else
		AC_MSG_RESULT([not found])
	fi
fi

if test "$enable_sshnodelay" = "yes"; then
	AC_DEFINE(SSH_NODELAY_WORKAROUND, 1, [Compile ssh NODELAY workaround])
fi

AM_CONDITIONAL(SSH_NODELAY_SO, test "$enable_sshnodelay" = "yes")

export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH
PKG_CHECK_MODULES(SSHFS, [fuse >= 2.3 glib-2.0 gthread-2.0])
have_fuse_opt_parse=no
oldlibs="$LIBS"
LIBS="$LIBS $SSHFS_LIBS"
AC_CHECK_FUNC([fuse_opt_parse], [have_fuse_opt_parse=yes])
LIBS="$oldlibs"
if test "$have_fuse_opt_parse" = no; then
	CFLAGS="$CFLAGS -Icompat"
fi
AM_CONDITIONAL(FUSE_OPT_COMPAT, test "$have_fuse_opt_parse" = no)

AC_CONFIG_FILES([Makefile])
AC_OUTPUT
