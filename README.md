# inko-sqlite

[Inko](https://inko-lang.org/) bindings for [SQLite](https://sqlite.org/). These
bindings don't expose the entire SQLite C API, instead they focus on exposing
what's useful for say 90% of all use cases. For example, defining custom
functions isn't supported but support for that may be added in the future if
deemed necessary.

# Example

```inko
import sqlite (Database)

let db = Database.new(':memory:').or_panic

# Create the table. Here we use `Database.execute` as we don't care about
# iterating over the returned rows.
let _ = db.execute('CREATE TABLE users (name TEXT)').or_panic

# Insert some data, guarding against SQL injection attacks by binding values
# instead of embedding them directly in the SQL string.
let st = db.prepare('INSERT INTO users VALUES (?)').or_panic

st.bind(column: 0, value: 'Alice').or_panic
st.execute.or_panic

# Now we can query the data:
let st = db.prepare('SELECT * FROM users').or_panic

for row in st.rows {
  let row = row.or_panic # Each value is a Result[Row, Error]

  row.string(0) # => Result.Ok('Alice')
}
```

# Requirements

- Inko `main`
- An installation of SQLite that provides a shared or static library, such as
  the `sqlite-devel` package when using Fedora

# Installation

```bash
inko pkg add github.com/yorickpeterse/inko-sqlite 0.1.0
inko pkg sync
```

# License

All source code in this repository is licensed under the Mozilla Public License
version 2.0, unless stated otherwise. A copy of this license is found in the
file "LICENSE".
