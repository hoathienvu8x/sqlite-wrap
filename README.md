# Lightweight SQLite3 wrapper for C++

> This project copy from [Daniel Beer](https://dlbeer.co.nz/oss/sqlite.html) [6 Jan 2015]

This pair of files implement a very lightweight wrapper for the public-domain [SQLite3](http://sqlite.org/) database library:

* [sqlite.hpp](https://github.com/hoathienvu8x/sqlite-wrap/blob/master/sqlite.hpp)
* [sqlite.cpp](https://github.com/hoathienvu8x/sqlite-wrap/blob/master/sqlite.cpp)

There are no external dependencies, apart from the SQLite3 library itself.

There are several types declared in the header:

* `io::sqlite::error`: an exception thrown when a database error occurs. Use the code() method to obtain the SQLite error code.
* `io::sqlite::db`: a database handle. It's not possible to copy a database object, but they can be swapped and moved (both of these operations are no-throw).
* `io::sqlite::stmt`: a statement handle. Also non-copyable, but swappable and movable.

Opening a database an executing a simple query is easy:

```c++
io::sqlite::db my_db("test.db");
my_db.exec("CREATE TABLE foo (key TEXT PRIMARY KEY, value TEXT)");
```

Using a prepared statement is only slightly more difficult:

```c++
io::sqlite::db my_db("test.db");
io::sqlite::stmt s(my_db, "UPDATE foo SET value=? WHERE key=?");

s.bind().text(1, "value goes here").text(2, "some_key");
s.exec();
```

Note that `bind()` returns a proxy object providing many methods for binding different data types. The methods provided are:

* blob
* blob_ref
* real
* int32
* int64
* null
* text
* text_ref

Note that for consistency with the C API, binding positions are numbered from 1. You can reuse a prepared statement by calling the `reset` method.

Obtaining rows from a prepared query is done by stepping through the execution of the query as follows:

```c++
io::sqlite::db my_db("test.db");
io::sqlite::stmt s(my_db, "SELECT key, value FROM foo");

// Prepare bindings before execution, if any...

while (s.step())
    std::cout << s.row().text(0) << '=' << s.row().text(1) << '\n';
```

The `row()` method returns a proxy object, like `bind()`. Its methods allow you to retrieve column values as one of several data types supported by SQLite. Methods provided are:

* size
* blob
* real
* int32
* int64
* cstr
* text

Confusingly, columns are numbered from 0, but this is also consistent with the C API.

## License

Copyright (C) 2014 Daniel Beer <dlbeer@gmail.com>

Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted, provided that the above copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
