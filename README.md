# Const::Introspect::C ![linux](https://github.com/PerlFFI/Const-Introspect-C/workflows/linux/badge.svg) ![windows](https://github.com/PerlFFI/Const-Introspect-C/workflows/windows/badge.svg) ![macos](https://github.com/PerlFFI/Const-Introspect-C/workflows/macos/badge.svg)

Find and evaluate C/C++ constants for use in Perl

# SYNOPSIS

```perl
use Const::Introspect::C;

my $c = Const::Introspect::C->new(
  headers => ['foo.h'],
);

foreach my $const ($c->get_macro_constants)
{
  # const isa Const::Introspect::C::Constant
  say "name  = ", $const->name;
  # type is one of: int, long, pointer, string,
  #                 float, double or "other"
  say "type  = ", $const->type;
  say "value = ", $const->value;
}
```

# DESCRIPTION

**Note**: This is an early release, expect some interface changes in the near future.

This module provides an interface for finding C/C++ constant style macros, and can
compute their types and values.  It can also be used to compute the values of
enumerated type constants, although this module doesn't have a way of finding
the names (For that try something like [Clang::CastXML](https://metacpan.org/pod/Clang::CastXML)).

# PROPERTIES

## headers

List of C/C++ header files.

## lang

The programming language.  Should be one of `c` or `c++`.  The default is `c`.

## cc

The C compiler.  The default is the C compiler used by Perl itself,
automatically split on the appropriate whitespace.
This should be a array reference, so `['clang']` and not `'clang'`.
This allows for `cc` with spaces in it.

## ppflags

The C pre-processor flags.  This may change in the future, or on some platforms, but as of
this writing this is `-dM -E -x c` for C and `-dM -E -x c++` for C++.  This must be an
array reference.

## cflags

C compiler flags.  This is what Perl uses by default.  This must be an array reference.

## extra\_cflags

Extra Compiler flags.  This is an empty array by default.  This allows the caller to provide additional
library specific flags, like `-I`.

## source

C source file.  This is an instance of [Path::Tiny](https://metacpan.org/pod/Path::Tiny) and it is created on-the-fly.  You shouldn't
need to specify this explicitly.

## filter

Filter regular expression that all macro names must match.  This is `^[^_]` by default, which means
all macros starting with an underscore are skipped.

## diag

List of diagnostic failures.

# METHODS

## get\_macro\_constants

```perl
my @const = $c->get_macro_constants;
```

This generates the source file, runs the pre-processor, parses the macros as well as possible and
returns the result as a list of [Const::Introspect::C::Constant](https://metacpan.org/pod/Const::Introspect::C::Constant) instances.

## get\_single

```perl
my $const = $c->get_single($name);
```

Get a single constant by the name of `$name`.  Returns an instance of
[Const::Introspect::C](https://metacpan.org/pod/Const::Introspect::C).  This is most useful for getting the integer
values for named enumerated values.

## compute\_expression\_type

```perl
my $type = $c->compute_expression_type($expression);
```

This attempts to compute the type of the C `$expression`.  It should
return one of `int`, `long`, `string`, `float`, `double`, or `other`.
If the type cannot be determined then `other` will be returned, and
often indicates a code macro that doesn't have a  corresponding
constant.

## compute\_expression\_value

```perl
my $value = $c->compute_expression_value($type, $expression);
```

This method attempts to compute the value of the given C `$expression` of
the given `$type`.  `$type` should be one of  `int`, `long`, `string`,
`float`, or `double`.

If you do not know the expression type, you can try to compute the type
using `compute_expression_type` above.

# CAVEATS

This modules requires the C pre-processor for macro constants, and for many constants
requires a compiler to compute the type and value.  The techniques used by this module
work with `clang` and `gcc`, but they probably don't work with other compilers.
Patches welcome to support other compilers.

This module can tell you the value of pointer constants, but there is not much utility
to the value of non `NULL` values.

# AUTHOR

Graham Ollis <plicease@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2020 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
