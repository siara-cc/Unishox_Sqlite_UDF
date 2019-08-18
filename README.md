# Compress / Decompress functions based on Unishox for SQLite3

This Sqlite3 [loadable extension](https://www.sqlite.org/loadext.html) exposes two functions `unishox1c()` and `unishox1d()` to any Sqlite3 database for compressing and decompressing UTF-8 text data.

# Preface

Unishox is a compression technique developed for reducing storage size of Short Strings.  Details of how it works can be found [here](https://github.com/siara-cc/Unishox).

In general it can achieve upto 60% size reduction for Short Strings.

# This project

This project exposes the compression and decompression functions of Unishox in the form of Sqlite Loadable extension.

The suffix of `1` indicates the version of the compression technique.  In case the compression algorithm is changed in future, this implementation will not be affected.  That means any text compressed with `unishox1c()` can be uncompressed only by `unishox1d()`.

# Compiling

To compile the loadable extension, use the following commands on `unishox1.c` downloaded from this repository:

Unix / Linux: `gcc -g -fPIC -shared unishox1.c -o unishox1.so`

MacOS: `gcc -g -fPIC -dynamiclib unishox1.c -o unishox1.dylib`

Windows MSVC: `cl unishox1.c -link -dll -out:unishox1.dll`

Windows MinGW: gcc -g -shared unishox1.c -o unishox1.dll`

# Usage

After successful compilation, run `sqlite3` from the same folder to open the Sqlite3 console.  The following set of commands demonstrate how this extension can be used:

```sql
.open test.db
.load unishox1
create table test (lang text, b1 blob);
insert into test values ('English', unishox1c('Beauty is not in the face. Beauty is a light in the heart.'));
insert into test values ('Spanish', unishox1c('La belleza no está en la cara. La belleza es una luz en el corazón.'));
insert into test values ('German', unishox1c('Schönheit ist nicht im Gesicht. Schönheit ist ein Licht im Herzen.'));
insert into test values ('French', unishox1c('La beauté est pas dans le visage. La beauté est la lumière dans le coeur.'));
insert into test values ('Russian', unishox1c('Красота не в лицо. Красота - это свет в сердце.'));
insert into test values ('Arabic', unishox1c(' الجمال ليس في الوجه. الجمال هو النور الذي في القلب.'));
insert into test values ('Hindi', unishox1c('सुंदरता चेहरे में नहीं है। सौंदर्य हृदय में प्रकाश है।'));
insert into test values ('Chinese', unishox1c('美是不是在脸上。 美是心中的亮光。'));
insert into test values ('Japanese', unishox1c('美は顔にありません。美は心の中の光です。'));
insert into test values ('Emoji', unishox1c('🤣🤣🤣🤣🤣🤣🤣🤣🤣🤣🤣'));
select txt, length(txt) txt_len, clen from (select unishox1d(b1) txt, length(b1) clen from test);
```

# Screenshots

![](output.png?raw=true)

# Limitations

- Trying to decompress any blob that was not compressed using `unishox1c()` will crash the program.
- This works well with short strings as shown above.  For longer strings, use [compress](https://www.sqlite.org/src/file/ext/misc/compress.c) loadable extension provided in Sqlite website.

# Issues

Please contact the author (Arundale Ramanathan) at arun@siara.cc if you find any problem or create issue here.
