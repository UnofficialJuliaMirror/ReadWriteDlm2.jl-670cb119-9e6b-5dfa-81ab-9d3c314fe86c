# ReadWriteDlm2
### CSV IO Supporting Decimal Comma, Date, DateTime, Time, Complex and Rational
[![ReadWriteDlm2](http://pkg.julialang.org/badges/ReadWriteDlm2_0.5.svg)](http://pkg.julialang.org/?pkg=ReadWriteDlm2) [![ReadWriteDlm2](http://pkg.julialang.org/badges/ReadWriteDlm2_0.6.svg)](http://pkg.julialang.org/?pkg=ReadWriteDlm2) [![ReadWriteDlm2](http://pkg.julialang.org/badges/ReadWriteDlm2_0.7.svg)](http://pkg.julialang.org/?pkg=ReadWriteDlm2=0.7)
[![Build Status](https://travis-ci.org/strickek/ReadWriteDlm2.jl.svg?branch=master)](https://travis-ci.org/strickek/ReadWriteDlm2.jl)   [![Build status](https://ci.appveyor.com/api/projects/status/ojv8nnuw63kh9yba/branch/master?svg=true)](https://ci.appveyor.com/project/strickek/readwritedlm2-jl/branch/master)  [![codecov.io](http://codecov.io/github/strickek/ReadWriteDlm2.jl/coverage.svg?branch=master)](http://codecov.io/github/strickek/ReadWriteDlm2.jl?branch=master)

`ReadWriteDlm2` functions `readdlm2()`, `writedlm2()`, `readcsv2()` and `writecsv2()` are similar to those of stdlib.DelimitedFiles, but with additional support for `Date`, `DateTime`, `Time`, `Complex`, `Rational` types and special decimal marks.

* For "decimal dot" users the functions `readcsv2()` and `writecsv2()` have the respective defaults: Delimiter is `','` (fixed) and `decimal='.'`.

* The basic idea of `readdlm2()` and `writedlm2()` is to support the [decimal comma countries](https://commons.wikimedia.org/wiki/File:DecimalSeparator.svg?uselang=en#file). These functions use `';'` as default delimiter and `','` as default decimal mark. "Decimal dot" users of these functions need to define `decimal='.'`.

* This documentation is for Julia 0.7 - for older versions see: [Julia 0.5](https://github.com/strickek/ReadWriteDlm2.jl/blob/v0.3.1/README.md), [Julia 0.6](https://github.com/strickek/ReadWriteDlm2.jl/blob/v0.6.2/README.md)

### Installation
This package is registered and can be installed with:
```
julia> using Pkg
julia> Pkg.add("ReadWriteDlm2")
```
or within the [`Pkg3` REPL-mode](https://docs.julialang.org/en/latest/stdlib/Pkg3/#Pkg3.jl-1):
```
pkg> add ReadWriteDlm2
```

### Basic Examples: How To Use `ReadWriteDlm2`
```
julia> using ReadWriteDlm2, Dates              # activate modules ReadWriteDlm2 und Dates

julia> A = Any[1 1.2; "text" Date(2017)];      # create array with: Int, Float64, String and Date type
julia> writedlm2("test.csv", A)                # test.csv(decimal comma): "1;1,2\ntext;2017-01-01\n"
julia> readdlm2("test.csv")                    # read `CSV` data: All four types are parsed correctly!
2×2 Array{Any,2}:
 1        1.2
  "text"   2017-01-01

julia> B = Any[1 complex(1.5,2.7);1.0 1//3];   # create array with: Int, Complex, Float64 and Rational type
julia> writecsv2("test.csv", B)                # test.csv(decimal dot): "1,1.5 + 2.7im\n1.0,1//3\n"
julia> readcsv2("test.csv")                    # read CSV data: All four types are parsed correctly!
2×2 Array{Any,2}:
 1    1.5+2.7im
 1.0    1//3

julia> rm("test.csv")
```

## Function `readdlm2()`
Read a matrix from `source`. The `source` can be a text file, stream or byte array. Each line, separated
by `eol` (default is `'\n'`), gives one row. The columns are separated by `';'`, another `delim` can be defined.

    readdlm2(source; options...)
    readdlm2(source, T::Type; options...)
    readdlm2(source, delim::Char; options...)
    readdlm2(source, delim::Char, T::Type; options...)
    readdlm2(source, delim::Char, eol::Char; options...)
    readdlm2(source, delim::Char, T::Type, eol::Char; options...)

Pre-processing of `source` with regex substitution changes the decimal marks from `d,d` to `d.d`.
For default `rs` the keyword argument `decimal=','` sets the decimal Char in the `r`-string of `rs`.
When a special regex substitution tuple `rs=(r.., s..)` is defined, the argument `decimal` is not used.
Pre-processing can be switched off with: `rs=()`.

In addition to stdlib `readdlm()`, data is also parsed for `Dates` formats (ISO), the`Time` format
`HH:MM[:SS[.s{1,9}]]` and for complex and rational numbers. To deactivate parsing dates/time set: `dfs="", dtfs=""`.
`locale` defines the language of day (`E`, `e`) and month (`U`, `u`) names.

The result will be a (heterogeneous) array of default element type `Any`. Homogeneous arrays are supported for
Type arguments such as: `String`, `Bool`, `Int`, `Float64`, `Complex`, `Rational`, `DateTime`, `Date`
and `Time`. If data is empty, a `0×0 Array{T,2}` is returned.

### Additional Keyword Arguments `readdlm2()`
* `decimal=','`: Decimal mark Char used by default `rs`, irrelevant if `rs`-tuple is not the default one
* `rs=(r"(\d),(\d)", s"\1.\2")`: [Regex](https://docs.julialang.org/en/latest/manual/strings/#Regular-Expressions-1) (r,s)-tuple, the default change d,d to d.d if `decimal=','`
* `dtfs="yyyy-mm-ddTHH:MM:SS.s"`: [Format string](https://docs.julialang.org/en/latest/stdlib/dates/#Dates.DateFormat) for DateTime parsing, default is ISO
* `dfs="yyyy-mm-dd"`: [Format string](https://docs.julialang.org/en/latest/stdlib/dates/#Dates.DateFormat) for Date parsing, default is ISO
* `locale="english"`: Language for parsing dates names, default is english

### Function `readcsv2()`

    readcsv2(source, T::Type=Any; opts...)

Equivalent to `readdlm2()` with delimiter `','` and `decimal='.'`.

### Documentation For Base `readdlm()`
More information about Base functionality and (keyword) arguments - which are also
supported by `readdlm2()` and `readcsv2()` - is available in the [documentation for readdlm()](https://docs.julialang.org/en/latest/stdlib/DelimitedFiles/#DelimitedFiles.readdlm-Tuple{Any,AbstractChar,Type,AbstractChar}).

### Compare Default Functionality `readdlm()` - `readdlm2()` - `readcsv2()`
| Module         | Function               | Delimiter  | Dec. Mark | Element Type | Extended Parsing  |
|:-------------- |:-----------------------|:----------:|:---------:|:-------------|:------------------|
| DelimitedFiles | `readdlm()`            | `' '`      | `'.'`     | Float64/Any  | No (String)       |
| ReadWriteDlm2  | `readdlm2()`           | `';'`      | `','`     | Any          | Yes               |
| ReadWriteDlm2  | `readcsv2()`           | `','`      | `'.'`     | Any          | Yes               |



## Function `writedlm2()`
Write `A` (a vector, matrix, or an iterable collection of iterable rows) as text to `f`
(either a filename string or an IO stream). The columns are separated by `';'`,
another `delim` (Char or String) can be defined.

    writedlm2(f::IO, A; options...)
    writedlm2(f::IO, A, delim; options...)
    writedlm2(f::AbstractString, A; options...)
    writedlm2(f::AbstractString, A, delim; options...)

By default, a pre-processing of values takes place. Before writing as strings,
decimal marks are changed from `'.'` to `','`. With a keyword argument
another decimal mark can be defined. To switch off this pre-processing set: `decimal='.'`.

In `writedlm2()` the output format for `Date` and `DateTime` data can be defined with format strings.
Defaults are the ISO formats. Day (`E`, `e`) and month (`U`, `u`) names are written in
the `locale` language. For writing `Complex` numbers the imaginary component suffix can be selected with the
`imsuffix=` keyword argument.

### Additional Keyword Arguments `writedlm2()`
* `decimal=','`: Character for writing decimal marks, default is a comma
* `dtfs="yyyy-mm-ddTHH:MM:SS.s"`: [Format string](https://docs.julialang.org/en/latest/stdlib/dates/#Dates.DateFormat),  DateTime write format, default is ISO
* `dfs="yyyy-mm-dd"`: [Format string](https://docs.julialang.org/en/latest/stdlib/dates/#Dates.DateFormat), Date write format, default is ISO
* `locale="english"`: Language for writing date names, default is english
* `imsuffix="im"`: Complex - imaginary component suffix `"im"`(=default), `"i"` or `"j"`

### Function `writecsv2()`

    writecsv2(f::IO, A; opts...)
    writecsv2(f::AbstractString, A; opts...)

Equivalent to `writedlm2()` with fixed delimiter `','` and `decimal='.'`.

### Compare Default Functionality `writedlm()` - `writedlm2()` - `writecsv2()`
| Module          | Function           | Delimiter | Decimal Mark |
|:--------------- |:------------------ |:---------:|:------------:|
| DelimitedFiles  | `writedlm()`       | `'\t'`    | `'.'`        |
| ReadWriteDlm2   | `writedlm2()`      | `';'`     | `','`        |
| ReadWriteDlm2   | `writecsv2()`      | `','`     | `'.'`        |

-> `writedlm2(f, A, '\t', decimal='.')` writes the same as `writedlm(f, A)`.
