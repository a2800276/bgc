<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# File Input/Output (Ein- und Ausgabe) 

[i[File I/O]<]
Wir haben bereits ein paar Beispiel von I/O mittels der Konsole
(Bildschirm und Tastatur) mit `scanf()` and
`printf()` gesehen.

In diesem Kapitel werden wir die Konzept weiter vertiefen.

(An.d.Üb. an dieser Stelle sollte es nicht überraschen, dass ich I/O nicht mit E/A übersetzen werde.)

## The `FILE*` Data Type

[i[`FILE*` type]<]

Jegliche Art von I/O in C findet durch eine Datenstruktur vom Typ `FILE
*` statt. Dieser `FILE*` beinhaltet alle notwendigen Information, um
sich mit dem I/O Subsystem über offene Dateien und wo man sich innerhalb
der Datei befindet, usw. auszutauschen.

Die Spezifikation bezeichnet diese als _stream_ (dt. Datenströme), d.h.
eine Strom Daten aus einer Datei oder irgendeiner anderen Quelle. Ich
werde "file" und "stream" austauschbar verwenden (An.d.Ü. vielleicht
würfel ich auch hier und da "Datei"), aber eigentlich sollte man Dateien als
spezifische Ausprägung von _Streams_ betrachten. Es gibt viele andere
Wege, Daten in ein Programm einströmen zu lassen, als Dateien zu lesen.

Gleich sehen wir wie man mit einem Dateinamen ein `FILE*` öffnet, aber
erst möchte ich noch drei _Streams_ vorstellen, die bereits geöffnet
vorliegen und verwendet werden können.

[i[`stdin` standard input]<]
[i[`stdout` standard output]<]
[i[`stderr` standard error]<]

|`FILE*` Name|Beschreibung|
|-|-|
|`stdin`|Standard Input, Standardeingabe, üblicherweise per Default die Tastatur|
|`stdout`|Standard Output, Standardausgabe, üblicherweise per Default
der Bildschirm |
|`stderr`|Standard Error, Standardfehlerausgabe, üblicherweise auch per
Default der Bildschirm |

Wie sich herrausstellt haben wir diese schon implizit benutzt. Folgende
beiden Aufrufe sind identisch:

``` {.c}
printf("Hello, world!\n");
fprintf(stdout, "Hello, world!\n");  // printf to a file
```

Mehr dazu später.

Dir wird aufgefallen sein, dass sowohl `stdout` als auch `stderr` auf
dem Bildschirm landen. Das wirkt erstmal redundant oder überflüssig, ist
es aber nicht. Betriebssysteme erlauben es typischerweise die Ausgaben
der beiden _Streams_ in unterschiedliche Dateien umzuleiten, was
hilfreich sein kann, um Fehler von den gewünschten Ausgabedaten zu
unterscheiden.

Verwendet man beispielsweise eine POSIX kompatibele Shell (wie sh, ksh,
bash, zsh, etc.) auf einem Unix-ähnlichem System, kann man Programme
so starten, dass nur die nicht-fehlerhaften Ausgaben (`stdout`) in eine
Datei und alle Fehler (`stderr`) in einer anderen geschrieben werden.


``` {.zsh}
./foo > output.txt 2> errors.txt   # This command is Unix-specific
```

Aus diesem Grund sollen ernsthafte Fehler immer an `stderr` statt
`stdout` geschickt werden.
[i[`stdin` standard input]>]
[i[`stdout` standard output]>]
[i[`stderr` standard error]>]

Mehr dazu später.
[i[`FILE*` type]<]

## Text Dateien lesen

[i[File I/O-->text files, reading]<]

_Streams_ werden hauptsächlich in zwei Kategorien unterteilt: _Text_ und
_Binär_ (_binary).

Text _Streams_ sind erhebliche Änderungen der zugrundliegenden Daten
erlaubt, insbesondere die Übersetzung von Zeilenumbrüchen in ihre
jeweilige Darstellung^[Früher gab es gewöhnlich drei Arten
Zeilenumbruch: Wagenrücklauf (CR, eng. _Carriage Return_, verwendet auf
alten Macs), Zeilenende (LF, eng. _Line Feed_ verwendet auf
Unix-Systemen) und Wagenrücklauf/Zeilenende (CRLF, verwendet auf
Windows-Systemen). Zum Glück hat die Einführung von OS X, da auf Unix
basiert, diese Zahl auf zwei reduziert.]. Als Abstraktion bestehen
Textdateien aus einer Folge von _Zeilen_, die durch Zeilenumbrüche
getrennt sind. Um portabel zu sein, sollte Ihre Eingabedaten immer mit
einem Zeilenumbruch enden.

Ganz allgemein: wenn Du die Datei in einem normalen Text-Editor
bearbeiten kannst, handelt es sich um eine Textdatei. Sonst ist die
Datei binär. Mehr zu binären Dateien später.

An die Arbeit --- Wie öffnen wir eine Datei zum lesen und wie kommen wir
an die Daten?

Erstellen wir eine Datei namens `hello.txt` mit nur dem folgenden
Inhalt:

``` {.default}
Hello, world!
```

Und dann schreiben wir ein Programm, das die Dateie öffnet, ein Zeichen
liest, die Datei schließt und dann fertig ist. Das ist der Plan!

[i[`fopen()` function]<]
[i[`fgetc()` function]<]
[i[`fclose()` function]<]
``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;                      // Variable to represent open file

    fp = fopen("hello.txt", "r");  // Open file for reading

    int c = fgetc(fp);             // Read a single character
    printf("%c\n", c);             // Print char to stdout

    fclose(fp);                    // Close the file when done
}
```


Beachte, dass uns `fopen()` beim Öffnen der Datei das `FILE*`
zurückgegeben hat, damit wir es später verwenden können.

(Aus Platzgründe lasse ich es hier weg,  aber fopen() gibt NULL zurück,
wenn etwas schief geht, wie beispielsweise wenn eine Datei nicht gefundene wird,
man sollte daher wirklich immer die Rückgabe auf Fehler überprüfen!)

Beachte auch das "r", das wir übergeben haben - es bedeutet "öffne
einen Textstream zum Lesen". (eng. "read") (Es gibt verschiedene Zeichenfolgen, die
wir `fopen()` übergeben können, z.B. Schreiben
oder Anhängen usw.) 

[i[`fopen()` function]>]

Danach haben wir `fgetc()` verwendet, um ein Zeichen aus dem
_Stream_ zu erhalten. Du fragst sich vielleicht, warum ich `c` als `int`
deklariert habe, statt als `char` - halten Sie an diesen Gedanken fest!

[i[`fgetc()` function]>]

Schließlich schließen wir den _Stream_, wenn wir damit fertig sind. Alle
Streams werden automatisch geschlossen, wenn sich das Programm beendet,
aber es gilt als gute Form, alle Dateien explizit selbst
zu schließen, sobald man sie nicht mehr benötigt.

[i[`fclose()` function]>]


Das [i[`FILE*` type]]`FILE*` verwaltet unsere Position innerhalb der
Datei. Nachfolgende Aufrufe von `fgetc()` würden das nächste Zeichen aus
der Datei auslesen und dann das nächste usw. bis ans Ende.

Das klingt nervid, Schauen wir, ob man das vereinfachen kann.

[i[File I/O-->text files, reading]>]

## End of File: `EOF`

[i[`EOF` end of file]<]
There is a special character defined as a macro: `EOF`. This is what
`fgetc()` will return when the end of the file has been reached and
you've attempted to read another character.

How about I share that Fun Fact™, now. Turns out `EOF` is the reason why
`fgetc()` and functions like it return an `int` instead of a `char`.
`EOF` isn't a character proper, and its value likely falls outside the
range of `char`. Since `fgetc()` needs to be able to return any byte
**and** `EOF`, it needs to be a wider type that can hold more values. so
`int` it is. But unless you're comparing the returned value against
`EOF`, you can know, deep down, it's a `char`.

All right! Back to reality! We can use this to read the whole file in a
loop.

[i[`fopen()` function]<]
[i[`fgetc()` function]<]
[i[`fclose()` function]<]
``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    int c;

    fp = fopen("hello.txt", "r");

    while ((c = fgetc(fp)) != EOF)
        printf("%c", c);

    fclose(fp);
}
```
[i[`fopen()` function]>]
[i[`fclose()` function]>]

(If line 10 is too weird, just break it down starting with the
innermost-nested parens. The first thing we do is assign the result of
`fgetc()` into `c`, and _then_ we compare _that_ against `EOF`. We've
just crammed it into a single line. This might look hard to read, but
study it---it's idiomatic C.)
[i[`fgetc()` function]>]

And running this, we see:

``` {.default}
Hello, world!
```

But still, we're operating a character at a time, and lots of text files
make more sense at the line level. Let's switch to that.
[i[`EOF` end of file]>]

### Reading a Line at a Time

[i[File I/O-->line by line]<]
So how can we get an entire line at once? [i[`fgets()`
function]<]`fgets()` to the rescue! For arguments, it takes a pointer to
a `char` buffer to hold bytes, a maximum number of bytes to read, and a
`FILE*` to read from. It returns `NULL` on end-of-file or error.
`fgets()` is even nice enough to NUL-terminate the string when its
done^[If the buffer's not big enough to read in an entire line, it'll
just stop reading mid-line, and the next call to `fgets()` will continue
reading the rest of the line.].[i[`fgets()` function]>]

Let's do a similar loop as before, except let's have a multiline file
and read it in a line at a time.

Here's a file `quote.txt`:

``` {.default}
A wise man can learn more from
a foolish question than a fool
can learn from a wise answer.
                  --Bruce Lee
```

And here's some code that reads that file a line at a time and prints
out a line number before each one:

[i[`fgets()` function]<]
``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    char s[1024];  // Big enough for any line this program will encounter
    int linecount = 0;

    fp = fopen("quote.txt", "r");

    while (fgets(s, sizeof s, fp) != NULL) 
        printf("%d: %s", ++linecount, s);

    fclose(fp);
}
```
[i[`fgets()` function]>]

Which gives the output:

``` {.default}
1: A wise man can learn more from
2: a foolish question than a fool
3: can learn from a wise answer.
4:                   --Bruce Lee
```
[i[File I/O-->line by line]>]

## Formatted Input

[i[File I/O-->formatted input]<]
You know how you can get formatted output with `printf()` (and, thus,
`fprintf()` like we'll see, below)?

[i[`fscanf()` function]<]
You can do the same thing with `fscanf()`.

Let's have a file with a series of data records in it. In this case,
whales, with name, length in meters, and weight in tonnes. `whales.txt`:

``` {.default}
blue 29.9 173
right 20.7 135
gray 14.9 41
humpback 16.0 30
```

Yes, we could read these with [i[`fgets()` function]]`fgets()` and then parse the
string with `sscanf()` (and in some ways that's more resilient against
corrupted files), but in this case, let's just use `fscanf()` and pull
it in directly.

The `fscanf()` function skips leading whitespace when reading, and
returns `EOF` on end-of-file or error.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    char name[1024];  // Big enough for any line this program will encounter
    float length;
    int mass;

    fp = fopen("whales.txt", "r");

    while (fscanf(fp, "%s %f %d", name, &length, &mass) != EOF)
        printf("%s whale, %d tonnes, %.1f meters\n", name, mass, length);

    fclose(fp);
}
```
[i[`fscanf()` function]>]

Which gives the result:

``` {.default}
blue whale, 173 tonnes, 29.9 meters
right whale, 135 tonnes, 20.7 meters
gray whale, 41 tonnes, 14.9 meters
humpback whale, 30 tonnes, 16.0 meters
```
[i[File I/O-->formatted input]>]

## Writing Text Files

[i[File I/O-->text files, writing]<]
[i[`fputc()` function]<]
[i[`fputs()` function]<]
[i[`fprintf()` function]<]
In much the same way we can use `fgetc()`, `fgets()`, and `fscanf()` to
read text streams, we can use `fputc()`, `fputs()`, and `fprintf()` to
write text streams.

To do so, we have to `fopen()` the file in write mode by passing `"w"`
as the second argument. Opening an existing file in `"w"` mode will
instantly truncate that file to 0 bytes for a full overwrite.

We'll put together a simple program that outputs a file `output.txt`
using a variety of output functions.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    int x = 32;

    fp = fopen("output.txt", "w");

    fputc('B', fp);
    fputc('\n', fp);   // newline
    fprintf(fp, "x = %d\n", x);
    fputs("Hello, world!\n", fp);

    fclose(fp);
}
```
[i[`fputc()` function]>]
[i[`fputs()` function]>]
[i[`fprintf()` function]>]

And this produces a file, `output.txt`, with these contents:

``` {.default}
B
x = 32
Hello, world!
```

Fun fact: since `stdout` is a file, you could replace line 8 with:

``` {.c}
fp = stdout;
```

and the program would have outputted to the console instead of to a
file. Try it!
[i[File I/O-->text files, writing]>]

## Binary File I/O

[i[File I/O-->binary files]<]
So far we've just been talking text files. But there's that other beast
we mentioned early on called _binary_ files, or binary streams.

These work very similarly to text files, except the I/O subsystem
doesn't perform any translations on the data like it might with a text
file. With binary files, you get a raw stream of bytes, and that's all.

The big difference in opening the file is that you have to add a `"b"`
to the mode. That is, to read a binary file, open it in `"rb"` mode. To
write a file, open it in `"wb"` mode.

Because it's streams of bytes, and streams of bytes can contain NUL
characters, and the NUL character is the end-of-string marker in C, it's
rare that people use the `fprintf()`-and-friends functions to operate on
binary files.

[i[`fwrite()` function]<]
Instead the most common functions are [i[`fread()` function]]`fread()`
and `fwrite()`. The functions read and write a specified number of bytes
to the stream.

To demo, we'll write a couple programs. One will write a sequence of
byte values to disk all at once. And the second program will read a byte
at a time and print them out^[Normally the second program would read all
the bytes at once, and _then_ print them out in a loop. That would be
more efficient. But we're going for demo value, here.].

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    unsigned char bytes[6] = {5, 37, 0, 88, 255, 12};

    fp = fopen("output.bin", "wb");  // wb mode for "write binary"!

    // In the call to fwrite, the arguments are:
    //
    // * Pointer to data to write
    // * Size of each "piece" of data
    // * Count of each "piece" of data
    // * FILE*

    fwrite(bytes, sizeof(char), 6, fp);

    fclose(fp);
}
```
[i[`fwrite()` function]>]

Those two middle arguments to `fwrite()` are pretty odd. But basically
what we want to tell the function is, "We have items that are _this_
big, and we want to write _that_ many of them." This makes it convenient
if you have a record of a fixed length, and you have a bunch of them in
an array. You can just tell it the size of one record and how many to
write. 

In the example above, we tell it each record is the size of a `char`,
and we have 6 of them.

Running the program gives us a file `output.bin`, but opening it in a
text editor doesn't show anything friendly! It's binary data---not text.
And random binary data I just made up, at that!

If I run it through a [flw[hex dump|Hex_dump]] program, we can see the
output as bytes:

``` {.default}
05 25 00 58 ff 0c
```

And those values in hex do match up to the values (in decimal) that we
wrote out.

But now let's try to read them back in with a different program. This
one will open the file for binary reading (`"rb"` mode) and will read
the bytes one at a time in a loop.

[i[`fread()` function]<]
`fread()` has the neat feature where it returns the number of bytes
read, or `0` on EOF. So we can loop until we see that, printing numbers
as we go.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    FILE *fp;
    unsigned char c;

    fp = fopen("output.bin", "rb"); // rb for "read binary"!

    while (fread(&c, sizeof(char), 1, fp) > 0)
        printf("%d\n", c);
}
```
[i[`fread()` function]>]

And, running it, we see our original numbers!

``` {.default}
5
37
0
88
255
12
```

Woo hoo!
[i[File I/O-->binary files]>]

### `struct` and Number Caveats

[i[File I/O-->with `struct`s]<]
As we saw in the `struct`s section, the compiler is free to add padding
to a `struct` as it sees fit. And different compilers might do this
differently. And the same compiler on different architectures could do
it differently. And the same compiler on the same architectures could do
it differently.

What I'm getting at is this: it's not portable to just `fwrite()` an
entire `struct` out to a file when you don't know where the padding will
end up.
[i[File I/O-->with `struct`s]>]

How do we fix this? Hold that thought---we'll look at some ways to do
this after looking at another related problem.

[i[File I/O-->with numeric values]<]
Numbers!

Turns out all architectures don't represent numbers in memory the same
way.

Let's look at a simple `fwrite()` of a 2-byte number. We'll write it in
hex so each byte is clear. The most significant byte will have the value
`0x12` and the least significant will have the value `0x34`.

``` {.c}
unsigned short v = 0x1234;  // Two bytes, 0x12 and 0x34

fwrite(&v, sizeof v, 1, fp);
```

What ends up in the stream?

Well, it seems like it should be `0x12` followed by `0x34`, right?

But if I run this on my machine and hex dump the result, I get:

``` {.default}
34 12
```

They're reversed! What gives?

This has something to do with what's called the
[i[Endianess]][flw[_endianess_|Endianess]] of the architecture. Some
write the most significant bytes first, and some the least significant
bytes first.

This means that if you write a multibyte number out straight from
memory, you can't do it in a portable way^[And this is why I used
individual bytes in my `fwrite()` and `fread()` examples, above,
shrewdly.].

A similar problem exists with floating point. Most systems use the same
format for their floating point numbers, but some do not. No guarantees!

[i[File I/O-->with `struct`s]<]
So... how can we fix all these problems with numbers and `struct`s to
get our data written in a portable way?

The summary is to [i[Data serialization]]_serialize_ the data, which is
a general term that means to take all the data and write it out in a
format that you control, that is well-known, and programmable to work
the same way on all platforms.

As you might imagine, this is a solved problem. There are a bunch of
serialization libraries you can take advantage of, such as Google's
[flw[_protocol buffers_|Protocol_buffers]], out there and ready to use.
They will take care of all the gritty details for you, and even will
allow data from your C programs to interoperate with other languages
that support the same serialization methods.

Do yourself and everyone a favor! Serialize your binary data when you
write it to a stream! This will keep things nice and portable, even if
you transfer data files from one architecture to another.
[i[File I/O-->with `struct`s]>]
[i[File I/O-->with numeric values]>]
[i[File I/O]>]
