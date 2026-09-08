Very low footprint DOM-style JSON parser written in portable C89 (sometimes referred to as ANSI C).

* BSD licensed with no dependencies (i.e. just drop `json.c` and `json.h` into your project)
* Never recurses or allocates more memory than it needs to represent the parsed JSON
* Very simple API with operator sugar for C++

[![Build Status](https://github.com/json-parser/json-parser/actions/workflows/main.yml/badge.svg)](https://github.com/json-parser/json-parser/actions)

_Want to serialize?  Check out [json-builder](https://github.com/json-parser/json-builder)!_

Installing
----------

There is now a makefile which will produce a libjsonparser static and dynamic library.  However, this
is _not_ required to build json-parser, and the source files (`json.c` and `json.h`) should be happy
in any build system you already have in place.


API
---
```c
json_value * json_parse (const json_char * json,
                         size_t length);

json_value * json_parse_ex (json_settings * settings,
                            const json_char * json,
                            size_t length,
                            char * error);

void json_value_free (json_value *);
```
The `type` field of `json_value` is one of:

* `json_object` (see `u.object.length`, `u.object.values[x].name`, `u.object.values[x].value`)
* `json_array` (see `u.array.length`, `u.array.values`)
* `json_integer` (see `u.integer`)
* `json_double` (see `u.dbl`)
* `json_string` (see `u.string.ptr`, `u.string.length`)
* `json_boolean` (see `u.boolean`)
* `json_null`


Compile-Time Options
--------------------
Unless otherwise specified, compile definitions must be provided both when compiling `json.c` and when compiling any of your own source files that include `json.h`.

### `JSON_TRACK_SOURCE`
Stores the source location (line and column number) inside each `json_value`.

This is useful for application-level error reporting.


### `json_int_t`
By default, `json_int_t` is defined as `long` under C89 and `int_fast64_t` otherwise. For MSVC it is defined as `__int64` regardless of language standard support.

Optionally, you may define `json_int_t` to be your own preferred type name for integer types parsed from JSON documents. It must be a signed integer type, there is no support for unsigned types. If you specify a raw primitive type without `signed` or `unsigned` (and not a typdef), `JSON_INT_MAX` will be calculated for you. Otherwise, you must provide your own definition of `JSON_INT_MAX` as the highest positive integer value that can be represented by `json_int_t`.

Example usage:
* `-Djson_int_t=short`
* `"-Djson_int_t=signed char" -DJSON_INT_MAX=127`
* `"-Djson_int_t=long long"`
* `-Djson_int_t=__int128`


Runtime Options
---------------
```c
settings |= json_enable_comments;
```
Enables C-style `// line` and `/* block */` comments.
```c
size_t value_extra
```
The amount of space (if any) to allocate at the end of each `json_value`, in
order to give the application space to add metadata.
```c
void * (* mem_alloc) (size_t, int zero, void * user_data);
void (* mem_free) (void *, void * user_data);
```
Custom allocator routines.  If NULL, the default `malloc` and `free` will be used.

The `user_data` pointer will be forwarded from `json_settings` to allow application
context to be passed.


Changes in version 1.1.1
------------------------

* Improved Unicode surrogate pair handling
  ([#58](https://github.com/json-parser/json-parser/pull/58),
   [#159](https://github.com/json-parser/json-parser/pull/159),
   [#179](https://github.com/json-parser/json-parser/issues/179))

* Fixes for null pointer derefs when using memory limiting or a fallible allocator
  ([e7ce6f7](https://github.com/json-parser/json-parser/commit/839bc075bf78d78c5af4cd2b0d9e0f5682fce752))

* Fixes for out of bounds reads with malformed inputs
  ([#72](https://github.com/json-parser/json-parser/pull/72),
   [#74](https://github.com/json-parser/json-parser/issues/74),
   [#78](https://github.com/json-parser/json-parser/issues/78))

* Reduced undefined behavior
  ([#108](https://github.com/json-parser/json-parser/pull/108),
   [#128](https://github.com/json-parser/json-parser/pull/128),
   [#130](https://github.com/json-parser/json-parser/pull/130),
   [#131](https://github.com/json-parser/json-parser/issues/131),
   [#154](https://github.com/json-parser/json-parser/pull/154),
   [#166](https://github.com/json-parser/json-parser/issues/166))

* Added guards against improper allocations and overflows
  ([#52](https://github.com/json-parser/json-parser/issues/52),
   [#169](https://github.com/json-parser/json-parser/issues/169),
   [#185](https://github.com/json-parser/json-parser/issues/185))

* Named `json_object_entry` for more easily working with object elements
  ([05f2c34](https://github.com/json-parser/json-parser/commit/05f2c346d155db8b5b658ec45a51b9ffa2972dbf),
   [c967d4d](https://github.com/json-parser/json-parser/commit/c967d4d86788c50352e8ea4d67c64808a49c6650))

* Better support for configuring `json_int_t`
  ([#84](https://github.com/json-parser/json-parser/issues/84),
   [#148](https://github.com/json-parser/json-parser/issues/148),
   [#151](https://github.com/json-parser/json-parser/pull/151),
   [#153](https://github.com/json-parser/json-parser/pull/153))

* Some of the C++ helpers now work in older standards / with more compilers
  ([#77](https://github.com/json-parser/json-parser/pull/77))

* Error messages are a bit more consistent
  ([#123](https://github.com/json-parser/json-parser/pull/123))

* Switched to using `size_t` in more places
  ([#138](https://github.com/json-parser/json-parser/issues/138),
   [#139](https://github.com/json-parser/json-parser/pull/139))

* Slightly better floating point parsing
  ([#136](https://github.com/json-parser/json-parser/pull/136),
   [#178](https://github.com/json-parser/json-parser/issues/178))

* Fixed compile errors for some environments
  ([#49](https://github.com/json-parser/json-parser/issues/49),
   [#57](https://github.com/json-parser/json-parser/pull/57))

* Reduced compiler warnings
  ([#88](https://github.com/json-parser/json-parser/pull/88),
   [#110](https://github.com/json-parser/json-parser/pull/110),
   [#146](https://github.com/json-parser/json-parser/pull/146))

* Small tweaks to which headers are included where
  ([#109](https://github.com/json-parser/json-parser/pull/109),
   [#115](https://github.com/json-parser/json-parser/pull/115),
   [#127](https://github.com/json-parser/json-parser/pull/127),
   [#140](https://github.com/json-parser/json-parser/pull/140))

* Small build system improvements
  ([#37](https://github.com/json-parser/json-parser/issues/37),
   [#45](https://github.com/json-parser/json-parser/pull/45),
   [#56](https://github.com/json-parser/json-parser/pull/56),
   [#62](https://github.com/json-parser/json-parser/pull/62),
   [#75](https://github.com/json-parser/json-parser/pull/75),
   [#104](https://github.com/json-parser/json-parser/pull/104),
   [#105](https://github.com/json-parser/json-parser/pull/105),
   [#107](https://github.com/json-parser/json-parser/pull/107),
   [#113](https://github.com/json-parser/json-parser/pull/113),
   [#114](https://github.com/json-parser/json-parser/pull/114),
   [#122](https://github.com/json-parser/json-parser/pull/122),
   [#164](https://github.com/json-parser/json-parser/pull/164),
   [#183](https://github.com/json-parser/json-parser/issues/183))

Changes in version 1.1.0
------------------------

* UTF-8 byte order marks are now skipped if present

* Allows cross-compilation by honoring --host if given (@wkz)

* Maximum size for error buffer is now exposed in header (@LB--)

* GCC warning for `static` after `const` fixed (@batrick)

* Optional support for C-style line and block comments added (@Jin-W-FS)

* `name_length` field added to object values

* It is now possible to retrieve the source line/column number of a parsed `json_value` when `JSON_TRACK_SOURCE` is enabled

* The application may now extend `json_value` using the `value_extra` setting

* Un-ambiguate pow call in the case of C++ overloaded pow (@fcartegnie)

* Fix null pointer de-reference when a non-existing array is closed and no root value is present
