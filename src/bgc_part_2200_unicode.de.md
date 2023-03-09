<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Unicode, Wide Characters, und so weiter

[i[Unicode]<]

Als Einführung sei gesagt, dass diese Ecke von C noch eine aktive
Baustelle ist. Wenn C2x veröffentlich wird, gibt es vermutlich ein paar
Änderungen.

Für die meisten Leute ist eigentlich nur die vermeintlich einfach Frage:
"Wie nutzen ich eigentlich diesen oder jenen Zeichensatz in C?" Da
kommen wir noch zu. Werden aber auch sehen, dass das vielleicht schon
funktioniert. Oder, dass man eine Bibliothek einbeinden muss.

Dieses Kapitel wird viel Inhalt abdecken -- manches spezifisch zu C,
anderes von allgemeiner Natur.

Lass uns vorab die Themen aufschlüsseln:

* Unicode Hintergrundwissen
* Hintergrundwissen zu _Character Encoding_ (Zeichensatz)
* Zeichensatz im Quelltext- und Ausführungskontext
* Unicode und UTF-8 verwenden
* Verwendung von Datentypen wie `wchar_t`, `char16_t` und `char32_t`

Ab in medias res!

## Was ist Unicode?

In den guten alten Zeiten was es, insbesonders in den USA und breiten
Teilen der westlichen Welt beliebt, 7- oder 8-Bit Darstellung für
Zeichen im Speicher zu verwenden. Daraus folgte, dass man 128 oder 256
verschiedene Zeichen insgesamt verwenden konnte (inklusive der nicht
druckbaren Zeichen). Auf Sicht der USA war das ausreichend, aber wer
hätte wissen können das tatsächlich andere Alphabete mit weitaus mehr
Buchstaben existieren. Es gibt über 50.000 chinesiche Schriftzeichen,
viel Glück die alle mit einem Byte darzustellen. 

Also haben sich Leute alle möglichen Tricks ausgedacht, um ihre
speziellen Zeichensätze darzustellen. Was ok war, sich aber als Alptraum
für Interoperabilität herausgestellt hat.

Um dem zu entkommen wurde Unicode erfunden. _Der_ Zeichensatz der die
Probleme ein-für-alle-mal löst. Für alle praktischen Zwecke undendliche
groß, soll es uns nie an Platz für neue Zeichen mangeln. Er kann
Chinesisch, Latein, Griechisch, Keilschrift, Schachsymbole, Emojis ...
wirklich alles. Und ständig kommt mehr dazu!

## _Code Points_

[i[Unicode-->code points]<]

An dieser Stelle möchte ich zwei Konzept verdeutlichen. Die verwirrend
sind, da es sich bei beiden um Zahlen handelt ... unterschiedliche
Zahlen für dieselbe Sache. 

Definieren wir _Codepoint_ locker als einen numerischen Wert, der für
ein Zeichen steht. (_Codepoints_ können auch Steuerzeichen usw.
darstellen, aber beschränken wir uns erstmal auf so etwas wie den
Buchstaben "B" oder das Zeichen "π".)

Jeder Codepoint repräsentiert eindeutig ein Zeichen. Und jedes Zeichen
hat einen eindeutigen, numerischen Codepoint zugeordnet. 


In Unicode steht beispielsweise der numerische Wert 66 für "B" und 960
steht für "π". Andere Zeichenzuordnungen als Unicode
verwenden möglicherweise andere Werte, aber vergessen wir die und
schauen in die Zukunft: auf Unicode, die Zukunft!

Das ist also das Eine: es gibt für jedes Zeichen eine Zahl, die es
repräsentiert. Die laufen in Unicode von 0 bis über 1 Millionen.

[i[Unicode-->code points]>]

Alles klar?

Denn jetzt betrachten wir die Kehrseite der Medaille.

## _Encoding_

[i[Unicode-->encoding]<]

Wie Du Dich bestimmt erinnerst, kann ein 8-Bit Byte Werte von 0 bis 255
darstellen. Erfreulich für unser "B" mit Wert 66 -- der passt in ein
Byte. Aber "π" ist 960 und passt eben nicht in ein Byte! Wir brauchen
noch einen Byte. Aber wie legen wir das im Speicher ab? Und was ist mit
noch größeren Zahlen wie 195.024? Dafür bräuchten wir driekt einen
ganzen haufen Bytes.

Die Große Frage lautet: wie stellen wir diese Zahlen im Speicher dar?
Dabei handelt es sich um das _Encoding_ von Zeichen.

So haben wir nun unsere zwei Konzepte: einerseits _Codepoints_, die
jedem Zeichen sozusagen eine Seriennummer zuweisen. Und _Encoding_ das
bestimmt, wie diese Zahl im Speicher dargestellt wird.

Es gibt jede Menge _Encodings_. Wenn Du magst, kannst Du Dir selbst eins
ausdenken^[Beispielsweise könntest Du den _Codepoint_ einfach und
unkompliziert als _big-endian_ 32-Bit Integer ablegen. Oder auch nicht,
weil Du dann UTF-32BE Encoding neu erfunden hättest. Naja, zurück an die
Arbeit.]. Stattdessen schauen wir uns erstmal die gläufigen, bestehenden
_Encodings_ für Unicode an.

[i[Unicode-->UTF-8]<]
[i[Unicode-->UTF-16]<]
[i[Unicode-->UTF-32]<]

|Encoding|Beschreibung|
|:-----------------:|:--------------------------------------------------------------|
|UTF-8|Byte orientiert, verwendet eine variabele Anzahl Bytes pro Zeichen. Sollte man nehmen, wenn nichts dagegen spricht.|
|UTF-16|Verwendet 16-Bit pro Zeichen[^091d].|
|UTF-32|Verwendet 32-Bit pro Zeichen.|

[^091d]: Oder so. Genaugenommen handelt es sich auch um eine variabele
  Breite, denn es gibt eine Möglichkeit _Codepoints_ die größer als
  $2^{16}$ sind darzustellen, in dem man zwei UTF-16 Zeichen
  aneinanderreiht.

Bei UTF-16 und UTF-32 spielt die Bytereihenfolge eine Rolle, so dass einem durchaus 
UTF-16BE für _Big-Endian_ und UTF-16LE für _Little-Endian_ über den Weg
laufen. Sofern nichts angegeben ist, sollte man von _big-endian_
ausgehen. Aber da Windows ziemlich intensiv gebrauch von UTF-16 macht
und _little-endian_ ist, wird auch das manchmal angenommen.^[Es gibt
ein Sonderzeichen names _Byte Order Mark_ (kurz BOM) mit _Codepoint_
0xFEFF das man optional seinen Daten voranstellen kann um _Endianess_
explizit anzuzeigen. Ist aber nicht vorgeschrieben].

Schauen wir uns ein paar Beispiele an. Ich notiere die Werte in Hex,
weil das genau zwei Ziffer pro 8-Bit Byte ergibt, so kann man sich
besser ein Bild machen, wie alles im Speicher arrangiert ist..

|Character|Codepoint|UTF-16BE|UTF-32BE|UTF-16LE|UTF-32LE|UTF-8|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|`A`|41|0041|00000041|4100|41000000|41|
|`B`|42|0042|00000042|4200|42000000|42|
|`~`|7E|007E|0000007E|7E00|7E000000|7E|
|`π`|3C0|03C0|000003C0|C003|C0030000|CF80|
|`€`|20AC|20AC|000020AC|AC20|AC200000|E282AC|

[i[Unicode-->endianess]<]

Schau mal, ob Dir irgendwelche Muster auffallen. Nicht vergessen, dass
es sich bei
UTF-16BE und UTF-32BE schlicht und ergreifend um die 16- und 32-Bit
Darstellung der _Codepoints_ handelt ^[Und nochmal, stimmt bei UTF-16
selbstverständlich nur für Zeichen deren _Codepoint_ in zwei Bytes
passt.]

[i[Unicode-->UTF-16]>]
[i[Unicode-->UTF-32]>]

_Little Endian_ ist genauso aufgebaut, außer, dass die Bytes in _Little Endian_
Reihenfolge angeordnet sind.

[i[Unicode-->endianess]>]

Und zum Schluss haben wir noch UTF-8. Als erstes fällt auf, dass ein
Byte _Codepoints_ von einem einzigen Byte dargestellt werden. Das ist
gut. Ebenso fällt auf, dass unterschiedliche _Codepoints_ mit
einer variabelen Anzahl Bytes dargestellt werden. Daher ist es ein
_variable lenght Encoding_.

Sobald der Wert des _Codepoints_ einen gewissen Wert übersteigt,
verwendet UTF-8 weitere Bytes um den Wert darzustellen. Aber die Werte
scheinen keinen Zusammenhang mit den eigentlichen _Codepoints_ zu haben.


[flw[Die Feinheiten von UTF-8 _Encoding_|UTF-8]] übersteigen hier den
Umfang. Es reicht zu wissen, dass UTF-8 Zeichen unterschiedlich lang
sein können, und, dass ihr Wert nicht ihrem _Codepoint_ entspricht, _es
sei denn, es handelt sich um einen der ersten 128 Codepoints_. Wenn Du
tieder einsteigen magst, kann ich folgendes [fl[Computerphile Video über
UTF-8 video mit Tom Scott|https://www.youtube.com/watch?v=MijmeoH9LT4]]
empfehlen.

Das letzte erwähnt Detail macht Unicode und UTF-8 für Nordamerikaner
besonders schmackhaft: es Rückwärtskompatibel zu 7-Bit ASCII! Wenn man
ausschliesslich 7-Bit ASCII verwendet, verändert sich gar nichts! Jedes
bestehende ASCII Dokument ist automatisch auch UTF-8 encoded!
(Funktioniert andersrum natürlich nicht.)

Und das ist vermutlich auch der Grund, warum UTF-8 so gut angenommen
wird.

[i[Unicode-->UTF-8]>]
[i[Unicode-->encoding]>]

## Zeichensätze im Quelltext- und Ausführungskontxt {#src-exec-charset}

[i[Character sets]<]

Beim Programmieren in C spielen (mindestens) frei verschiedene
Zeichensätze eine Rolle:

* Der, mit dem Dein Quelltext auf die Festplatte geschrieben ist
* [i[Character sets-->source]] Der, zu dem der Compiler den Quelltext
  während des Kompiliervorgangs übersetzt (der _source character set_).
  Dabei kann es sich um den Zeichensatz auf der Festplatte handeln, muss
  es aber nicht.
* [i[Character sets-->execution]] Den Zeichensatz, zu dem der Compiler
  den Quelltext umwandelt für die Verwendung beim ausführen des
  kompilierten Programms. Dieses _execution character
  set_ kann mit der _source character set_ übereinstimmen, muss aber
  nicht.

Zum Zeitpunkt des _Build_ kann man dem Compilere all diese Zeichensätze
angeben.

[i[Character sets-->basic]<]

Der Basiszeichensatz (_basic character set_) bleibt im Quelltext- und
Ausführungskontext gleich, und besteht aus den folgenden Zeichen:

``` {.default}
A B C D E F G H I J K L M
N O P Q R S T U V W X Y Z
a b c d e f g h i j k l m
n o p q r s t u v w x y z
0 1 2 3 4 5 6 7 8 9
! " # % & ' ( ) * + , - . / :
; < = > ? [ \ ] ^ _ { | } ~
space tab vertical-tab
form-feed end-of-line
```

All diese Zeichen können im Quelltext verwendet werden und alles bleibt
100% portabel.

Der Zeichensatz zur Ausführung hat darüberhinaus Zeichen für Alerts
(bimmeln und blinken), Backspace, Wagenrücklauf und Zeilenumbrüche.

Die meisten Menschen schränken sich aber nicht so weit ein und verwenden
auch erweiterte Zeichen im Quelltext und beim Ausführen des Programms.
Insbesonders seit Unicode und UTF-8 so verbreitet geworden sind. Der
Basiszeichensatz erlaubt ja nicht mal '@', '$' oder gar '' ' '' !

An der Stelle soll nicht unerwähnt bleiben, dass es nervig (aber dank
Escape Sequenzen möglich ...) ist, Unicode Zeichen nur mit Hilfe ds
Basiszeichensatz einzugeben.

[i[Character sets-->basic]>]
[i[Character sets]>]

## Unicode in C {#unicode-in-c}

Lass uns kurz über Unicode aus der Sicht von _Codepoints_ sprechen,
bevor für Zeichensätze in C besprechen. Es gibt in C die Möglichkeit,
Unicode Zeichen anzugeben, so dass diese vom Compiler in den
Ausführungszeichensatz übersetzt werden^[Vermutlich führt der Compiler
_Best Effort_ Anstrengungen durch, die _Codepoint_ in den
Ausgabezeichensatz zu übersetzen, aber ich habe nicht spezifisches dazu
in der Spezifikation finden können.]. 

Wie funktioniert das also?

Z.B. das Euro-Symbol, _Codepoint_ 0x20AC. (Hier in Hex notiert, da beide
Arten, es in C darzustellen, Hex erfordern.) Wie bringen wir das in
unserem C Code unter?

[i[`\u` Unicode escape]<]

Um es in eine Zeichenkette zu packen, verwenden wir die `\u`
Escapesequenz, d.h. `"\u20AC"`. Während Groß- und Kleinschreibung bei
Hex keine Rolle spielt, muss beachtet werden, dass sich **ganz genau** vier
hexadezimale Ziffern hinter dem `\u` befinden. Falls nicht, muss mit
führenden Nullen aufgefüllt werden.

Hier ein Beispiel:

``` {.c}
char *s = "\u20AC1.23";

printf("%s\n", s);  // €1.23
```

[i[`\U` Unicode escape]<]

`\u` funktioniert also für 16-Bit Unicode _Codepoints_, aber wie gehen
wir mit _Codepoints_ um, die größer sind? Dafür brauchen wir Majuskeln:
`\U`.

Zum Beispiel:

``` {.c}
char *s = "\U0001D4D1";

printf("%s\n", s);  // Prints a mathematical letter "B"
```

Die Escapesequenz verhält sich genau wie `\u`, nur mit 32 Bits statt mit
16. Die beiden _Escapes_ bezeichnen das Gleiche:

``` {.c}
\u03C0
\U000003C0
```

[i[`\u` Unicode escape]>]
[i[`\U` Unicode escape]>]

<!-- Translator TODO: check previous character set/encoding usage -->
Nochmal, die _Escapes_ werden in den Zeichensatz [i[Character
sets-->execution]] des ausführenden Kontext übersetzt, nicht zu einer
spezifischen Kodierung. Ferner darf der Compiler machen, was er will
wenn ein _Codepoint_ im Ausführenden Zeichensatz nicht darstellbar ist. 

Vielleicht fragst Du Dich jetzt, warum wir das nicht einfacher machen:

``` {.c}
char *s = "€1.23";

printf("%s\n", s);  // €1.23
```

Und das wird vermutlich funktionieren, vorrausgesetzt, ein moderner
Compiler wird verwendet. Der Compiler übersetzt den [i[Character
sets-->source]]Quelltext Zeichensatz in den [i[Character
sets-->execution]] Ausführunszeichensatz. Aber der Compiler darf
Zeichen, die sich nicht in seinem erweiterten Zeichensatz befinden
einfach ausspucken. Und das € Symbol befindet sich ganz bestimmt nicht
im [i[Character sets-->basic]]Basiszeichensatz.

[i[`\u` Unicode escape]<]
[i[`\U` Unicode escape]<]

Kleiner Vorbehalt aus der Spezifikation: die `\u` und `\U` _Escapes_
können nicht verwendet werden, um _Codepoints_ die kleiner als 0xA0
sind, darzustellen. Ausser 0x24 (`$`), 0x40 (`@`) und 0x60 (`` ` ``) --
genau das Trio gängiger Satzzeichen, die im Basiszeichensatz fehlen.
Diese Einschränkung soll wohl in einer kommenden Spezifikation
aufgehoben werden.

[i[`\u` Unicode escape]>]
[i[`\U` Unicode escape]>]

Abschließend soll kurz erwähnt werden, dass diese auch im Code als
Bezeichner verwendet werden dürfen, allerdings mit ein paar
Einschränkung. Das vertiefen wir an dieser Stelle nicht weiter, dieser
Kapitel dreh sich um Zeichenketten.

Und das war's schon zum Thema Unicode in C (Ausnahme: _Encoding_)
And that's about it for Unicode in C (except encoding).

## Noch ein kurzer Hinweis zu UTF-8 bevor wir die Abgründe ansteuern {#utf8-quick}
[i[Unicode-->UTF-8]<]

It could be that your source file on disk, the extended source
characters, and the extended execution characters are all in UTF-8
format. And the libraries you use expect UTF-8. This is the glorious
future of UTF-8 everywhere.

If that's the case, and you don't mind being non-portable to systems
that aren't like that, then just run with it. Stick Unicode characters
in your source and data at will. Use regular C strings and be happy.

A lot of things will just work (albeit non-portably) because UTF-8
strings can safely be NUL-terminated just like any other C string. But
maybe losing portability in exchange for easier character handling is a
tradeoff that's worth it to you.

There are some caveats, however:

* Things like `strlen()` report the number of bytes in a string, not the
  number of characters, necessarily. (The [i[`mbstowcs()` function-->with
  UTF-8]]`mbstowcs()` returns the number of characters in a string when
  you convert it to wide characters. POSIX extends this so you can pass
  `NULL` for the first argument if you just want the character count.)

* The following won't work properly with characters of more than one
  byte: [i[`strtok()` function-->with UTF-8]]`strtok()`, [i[`strchr()`
  function-->with UTF-8]]`strchr()` (use [i[`strstr()` function-->with
  UTF-8]]`strstr()` instead), `strspn()`-type functions, [i[`toupper()`
  function-->with UTF-8]]`toupper()`, [i[`tolower()` function-->with
  UTF-8]]`tolower()`, [i[`isalpha()` function-->with
  UTF-8]]`isalpha()`-type functions, and probably more. Beware anything
  that operates on bytes.

* [i[`printf()` function-->with UTF-8]]`printf()` variants allow for a
  way to only print so many bytes of a string^[With a format specifier
  like `"%.12s"`, for example.]. You want to make certain you print the
  correct number of bytes to end on a character boundary.

* [i[`malloc()` function-->with UTF-8]]If you want to `malloc()` space
  for a string, or declare an array of `char`s for one, be aware that
  the maximum size could be more than you were expecting. Each character
  could take up to [i[`MB_LEN_MAX` macro]]`MB_LEN_MAX` bytes (from
  `<limits.h>`)---except characters in the basic character set which are
  guaranteed to be one byte.

And probably others I haven't discovered. Let me know what pitfalls
there are out there...

[i[Unicode-->UTF-8]>]

## Different Character Types

I want to introduce more character types. We're used to `char`, right?

But that's too easy. Let's make things a lot more difficult! Yay!

### Multibyte Characters

[i[Multibyte characters]<]

First of all, I want to potentially change your thinking about what a
string (array of `char`s) is. These are _multibyte strings_ made up of
_multibyte characters_.

That's right---your run-of-the-mill string of characters is multibyte.
When someone says "C string", they mean "C multibyte string".

Even if a particular character in the string is only a single byte, or
if a string is made up of only single characters, it's known as
a multibyte string.

For example:

``` {.c}
char c[128] = "Hello, world!";  // Multibyte string
```

What we're saying here is that a particular character that's not in the
basic character set could be composed of multiple bytes. Up to
[i[`MB_LEN_MAX` macro]]`MB_LEN_MAX` of them (from `<limits.h>`). Sure,
it only looks like one character on the screen, but it could be multiple
bytes.

You can throw Unicode values in there, as well, as we saw earlier:

``` {.c}
char *s = "\u20AC1.23";

printf("%s\n", s);  // €1.23
```

But here we're getting into some weirdness, because check this out:

[i[`strlen()` function-->with UTF-8]<]

``` {.c}
char *s = "\u20AC1.23";  // €1.23

printf("%zu\n", strlen(s));  // 7!
```

The string length of `"€1.23"` is `7`?! Yes! Well, on my system, yes!
Remember that `strlen()` returns the number of bytes in the string, not
the number of characters. (When we get to "wide characters", coming up,
we'll see a way to get the number of characters in the string.)

[i[`strlen()` function-->with UTF-8]>]

Note that while C allows individual multibyte `char` constants (as
opposed to `char*`), the behavior of these varies by implementation and
your compiler might warn on it.

GCC, for example, warns of multi-character character constants for the
following two lines (and, on my system, prints out the UTF-8 encoding):

``` {.c}
printf("%x\n", '€');
printf("%x\n", '\u20ac');
```

[i[Multibyte characters]>]

### Wide Characters {#wide-characters}

[i[Wide characters]<]

If you're not a multibyte character, then you're a _wide character_.

A wide character is a single value that can uniquely represent any
character in the current locale. It's analogous to Unicode code points.
But it might not be. Or it might be.

Basically, where multibyte character strings are arrays of bytes, wide
character strings are arrays of _characters_. So you can start thinking
on a character-by-character basis rather than a byte-by-byte basis (the
latter of which gets all messy when characters start taking up variable
numbers of bytes).

[i[`wchar_t` type]<]

Wide characters can be represented by a number of types, but the big
standout one is `wchar_t`. It's the main one. It's like `char`, except
wide.

You might be wondering if you can't tell if it's Unicode or not, how
does that allow you much flexibility in terms of writing code? `wchar_t`
opens some of those doors, as there are a rich set of functions you can
use to deal with `wchar_t` strings (like getting the length, etc.)
without caring about the encoding.

## Using Wide Characters and `wchar_t`

Time for a new type: `wchar_t`. This is the main wide character type.
Remember how a `char` is only one byte? And a byte's not enough to
represent all characters, potentially? Well, this one is enough.

To use `wchar_t`, include `<wchar.h>`.

How many bytes big is it? Well, it's not totally clear. Could be 16
bits. Could be 32 bits.

But wait, you're saying---if it's only 16 bits, it's not big enough to
hold all the Unicode code points, is it? You're right---it's not. The
spec doesn't require it to be. It just has to be able to represent all
the characters in the current locale.

This can cause grief with Unicode on platforms with 16-bit `wchar_t`s
(ahem---Windows). But that's out of scope for this guide.

[i[`L` wide character prefix]<]

You can declare a string or character of this type with the `L` prefix,
and you can print them with the `%ls` ("ell ess") format specifier. Or
print an individual `wchar_t` with `%lc`.

``` {.c}
wchar_t *s = L"Hello, world!";
wchar_t c = L'B';

printf("%ls %lc\n", s, c);
```

[i[`L` wide character prefix]>]

Now---are those characters stored as Unicode code points, or not?
Depends on the implementation. But you can test if they are with the
macro [i[`__STDC_ISO_10646__` macro]] `__STDC_ISO_10646__`. If this is
defined, the answer is, "It's Unicode!"

More detailedly, the value in that macro is an integer in the form
`yyyymm` that lets you know what Unicode standard you can rely
on---whatever was in effect on that date.

But how do you use them?

### Multibyte to `wchar_t` Conversions

So how do we get from the byte-oriented standard strings to the
character-oriented wide strings and back?

We can use a couple string conversion functions to make this happen.

First, some naming conventions you'll see in these functions:

* `mb`: multibyte
* `wc`: wide character
* `mbs`: multibyte string
* `wcs`: wide character string

So if we want to convert a multibyte string to a wide character string,
we can call the `mbstowcs()`. And the other way around: `wcstombs()`.






|Conversion Function|Description|
|-|-|
|[i[`mbtowc()` function]]`mbtowc()`|Convert a multibyte character to a wide character.|
|[i[`wctomb()` function]]`wctomb()`|Convert a wide character to a multibyte character.|
|[i[`mbstowcs()` function]]`mbstowcs()`|Convert a multibyte string to a wide string.|
|[i[`wcstombs()` function]]`wcstombs()`|Convert a wide string to a multibyte string.|

Let's do a quick demo where we convert a multibyte string to a wide
character string, and compare the string lengths of the two using their
respective functions.

[i[`mbstowcs()` function]<]

``` {.c .numberLines}
#include <stdio.h>
#include <stdlib.h>
#include <wchar.h>
#include <string.h>
#include <locale.h>

int main(void)
{
    // Get out of the C locale to one that likely has the euro symbol
    setlocale(LC_ALL, "");

    // Original multibyte string with a euro symbol (Unicode point 20ac)
    char *mb_string = "The cost is \u20ac1.23";  // €1.23
    size_t mb_len = strlen(mb_string);

    // Wide character array that will hold the converted string
    wchar_t wc_string[128];  // Holds up to 128 wide characters

    // Convert the MB string to WC; this returns the number of wide chars
    size_t wc_len = mbstowcs(wc_string, mb_string, 128);

    // Print result--note the %ls for wide char strings
    printf("multibyte: \"%s\" (%zu bytes)\n", mb_string, mb_len);
    printf("wide char: \"%ls\" (%zu characters)\n", wc_string, wc_len);
}
```

[i[`wchar_t` type]>]

On my system, this outputs:

``` {.default}
multibyte: "The cost is €1.23" (19 bytes)
wide char: "The cost is €1.23" (17 characters)
```

(Your system might vary on the number of bytes depending on your
locale.)

One interesting thing to note is that `mbstowcs()`, in addition to
converting the multibyte string to wide, returns the length (in
characters) of the wide character string. On POSIX-compliant systems,
you can take advantage of a special mode where it _only_ returns the
length-in-characters of a given multibyte string: you just pass `NULL`
to the destination, and `0` to the maximum number of characters to
convert (this value is ignored).

(In the code below, I'm using my extended source character set---you
might have to replace those with `\u` escapes.)

``` {.c}
setlocale(LC_ALL, "");

// The following string has 7 characters
size_t len_in_chars = mbstowcs(NULL, "§¶°±π€•", 0);

printf("%zu", len_in_chars);  // 7
```

[i[`mbstowcs()` function]>]

Again, that's a non-portable POSIX extension.

And, of course, if you want to convert the other way, it's
[i[`wcstombs()` function]] `wcstombs()`.

## Wide Character Functionality

Once we're in wide character land, we have all kinds of functionality at
our disposal. I'm just going to summarize a bunch of the functions here,
but basically what we have here are the wide character versions of the
multibyte string functions that we're use to. (For example, we know
`strlen()` for multibyte strings; there's a [i[`wcslen()` function]]
`wcslen()` for wide character strings.)

### `wint_t`

[i[`wint_t` type]<]

A lot of these functions use a `wint_t` to hold single characters,
whether they are passed in or returned.

It is related to `wchar_t` in nature. A `wint_t` is an integer that can
represent all values in the extended character set, and also a special
end-of-file character, `WEOF`.

[i[`wint_t` type]>]

This is used by a number of single-character-oriented wide character
functions.

### I/O Stream Orientation {#io-stream-orientation}

[i[I/O stream orientation]<]

The tl;dr here is to not mix and match byte-oriented functions (like
`fprintf()`) with wide-oriented functions (like `fwprintf()`). Decide if
a stream will be byte-oriented or wide-oriented and stick with those
types of I/O functions.

In more detail: streams can be either byte-oriented or wide-oriented.
When a stream is first created, it has no orientation, but the first
read or write will set the orientation.

If you first use a wide operation (like `fwprintf()`) it will orient the
stream wide.

If you first use a byte operation (like `fprintf()`) it will orient the
stream by bytes.

You can manually set an unoriented stream one way or the other with a
call to [i[`fwide()` function]] `fwide()`. You can use that same
function to get the orientation of a stream.

If you need to change the orientation mid-flight, you can do it with
`freopen()`.

[i[I/O stream orientation]>]

### I/O Functions

Typically include `<stdio.h>` and `<wchar.h>` for these.

|I/O Function|Description|
|-|-|
|[i[`wprintf()` function]]`wprintf()`|Formatted console output.|
|[i[`wscanf()` function]]`wscanf()`|Formatted console input.|
|[i[`getwchar()` function]]`getwchar()`|Character-based console input.|
|[i[`putwchar()` function]]`putwchar()`|Character-based console output.|
|[i[`fwprintf()` function]]`fwprintf()`|Formatted file output.|
|[i[`fwscanf()` function]]`fwscanf()`|Formatted file input.|
|[i[`fgetwc()` function]]`fgetwc()`|Character-based file input.|
|[i[`fputwc()` function]]`fputwc()`|Character-based file output.|
|[i[`fgetws()` function]]`fgetws()`|String-based file input.|
|[i[`fputws()` function]]`fputws()`|String-based file output.|
|[i[`swprintf()` function]]`swprintf()`|Formatted string output.|
|[i[`swscanf()` function]]`swscanf()`|Formatted string input.|
|[i[`vfwprintf()` function]]`vfwprintf()`|Variadic formatted file output.|
|[i[`vfwscanf()` function]]`vfwscanf()`|Variadic formatted file input.|
|[i[`vswprintf()` function]]`vswprintf()`|Variadic formatted string output.|
|[i[`vswscanf()` function]]`vswscanf()`|Variadic formatted string input.|
|[i[`vwprintf()` function]]`vwprintf()`|Variadic formatted console output.|
|[i[`vwscanf()` function]]`vwscanf()`|Variadic formatted console input.|
|[i[`ungetwc()` function]]`ungetwc()`|Push a wide character back on an output stream.|
|[i[`fwide()` function]]`fwide()`|Get or set stream multibyte/wide orientation.|

### Type Conversion Functions

Typically include `<wchar.h>` for these.

|Conversion Function|Description|
|-|-|
|[i[`wcstod()` function]]`wcstod()`|Convert string to `double`.|
|[i[`wcstof()` function]]`wcstof()`|Convert string to `float`.|
|[i[`wcstold()` function]]`wcstold()`|Convert string to `long double`.|
|[i[`wcstol()` function]]`wcstol()`|Convert string to `long`.|
|[i[`wcstoll()` function]]`wcstoll()`|Convert string to `long long`.|
|[i[`wcstoul()` function]]`wcstoul()`|Convert string to `unsigned long`.|
|[i[`wcstoull()` function]]`wcstoull()`|Convert string to `unsigned long long`.|

### String and Memory Copying Functions

Typically include `<wchar.h>` for these.

|Copying Function|Description|
|----|----------------------------------------------|
|[i[`wcscpy()` function]]`wcscpy()`|Copy string.|
|[i[`wcsncpy()` function]]`wcsncpy()`|Copy string, length-limited.|
|[i[`wmemcpy()` function]]`wmemcpy()`|Copy memory.|
|[i[`wmemmove()` function]]`wmemmove()`|Copy potentially-overlapping memory.|
|[i[`wcscat()` function]]`wcscat()`|Concatenate strings.|
|[i[`wcsncat()` function]]`wcsncat()`|Concatenate strings, length-limited.|

### String and Memory Comparing Functions

Typically include `<wchar.h>` for these.

|Comparing Function|Description|
|-------------------|---------------------------------------------------------------|
|[i[`wcscmp()` function]]`wcscmp()`|Compare strings lexicographically.|
|[i[`wcsncmp()` function]]`wcsncmp()`|Compare strings lexicographically, length-limited.|
|[i[`wcscoll()` function]]`wcscoll()`|Compare strings in dictionary order by locale.|
|[i[`wmemcmp()` function]]`wmemcmp()`|Compare memory lexicographically.|
|[i[`wcsxfrm()` function]]`wcsxfrm()`|Transform strings into versions such that `wcscmp()` behaves like `wcscoll()`[^97d0].|

[^97d0]: `wcscoll()` is the same as `wcsxfrm()` followed by `wcscmp()`.

### String Searching Functions

Typically include `<wchar.h>` for these.

|Searching Function|Description|
|-|-|
|[i[`wcschr()` function]]`wcschr()`|Find a character in a string.|
|[i[`wcsrchr()` function]]`wcsrchr()`|Find a character in a string from the back.|
|[i[`wmemchr()` function]]`wmemchr()`|Find a character in memory.|
|[i[`wcsstr()` function]]`wcsstr()`|Find a substring in a string.|
|[i[`wcspbrk()` function]]`wcspbrk()`|Find any of a set of characters in a string.|
|[i[`wcsspn()` function]]`wcsspn()`|Find length of substring including any of a set of characters.|
|[i[`wcscspn()` function]]`wcscspn()`|Find length of substring before any of a set of characters.|
|[i[`wcstok()` function]]`wcstok()`|Find tokens in a string.|

### Length/Miscellaneous Functions

Typically include `<wchar.h>` for these.

|Length/Misc Function|Description|
|-|-|
|[i[`wcslen()` function]]`wcslen()`|Return the length of the string.|
|[i[`wmemset()` function]]`wmemset()`|Set characters in memory.|
|[i[`wcsftime()` function]]`wcsftime()`|Formatted date and time output.|

### Character Classification Functions

Include `<wctype.h>` for these.

|Length/Misc Function|Description|
|-|-|
|[i[`iswalnum()` function]]`iswalnum()`|True if the character is alphanumeric.|
|[i[`iswalpha()` function]]`iswalpha()`|True if the character is alphabetic.|
|[i[`iswblank()` function]]`iswblank()`|True if the character is blank (space-ish, but not a newline).|
|[i[`iswcntrl()` function]]`iswcntrl()`|True if the character is a control character.|
|[i[`iswdigit()` function]]`iswdigit()`|True if the character is a digit.|
|[i[`iswgraph()` function]]`iswgraph()`|True if the character is printable (except space).|
|[i[`iswlower()` function]]`iswlower()`|True if the character is lowercase.|
|[i[`iswprint()` function]]`iswprint()`|True if the character is printable (including space).|
|[i[`iswpunct()` function]]`iswpunct()`|True if the character is punctuation.|
|[i[`iswspace()` function]]`iswspace()`|True if the character is whitespace.|
|[i[`iswupper()` function]]`iswupper()`|True if the character is uppercase.|
|[i[`iswxdigit()` function]]`iswxdigit()`|True if the character is a hex digit.|
|[i[`towlower()` function]]`towlower()`|Convert character to lowercase.|
|[i[`towupper()` function]]`towupper()`|Convert character to uppercase.|

## Parse State, Restartable Functions

[i[Multibyte characters-->parse state]<]

We're going to get a little bit into the guts of multibyte conversion,
but this is a good thing to understand, conceptually.

Imagine how your program takes a sequence of multibyte characters and
turns them into wide characters, or vice-versa. It might, at some point,
be partway through parsing a character, or it might have to wait for
more bytes before it makes the determination of the final value.

This parse state is stored in an opaque variable of type `mbstate_t` and
is used every time conversion is performed. That's how the conversion
functions keep track of where they are mid-work.

And if you change to a different character sequence mid-stream, or try
to seek to a different place in your input sequence, it could get
confused over that.

Now you might want to call me on this one: we just did some conversions,
above, and I never mentioned any `mbstate_t` anywhere.

That's because the conversion functions like `mbstowcs()`, `wctomb()`,
etc. each have their own `mbstate_t` variable that they use. There's
only one per function, though, so if you're writing multithreaded code,
they're not safe to use.

Fortunately, C defines _restartable_ versions of these functions where
you can pass in your own `mbstate_t` on per-thread basis if you need to.
If you're doing multithreaded stuff, use these!

Quick note on initializing an `mbstate_t` variable: just `memset()` it
to zero. There is no built-in function to force it to be initialized.

``` {.c}
mbstate_t mbs;

// Set the state to the initial state
memset(&mbs, 0, sizeof mbs);
```

Here is a list of the restartable conversion functions---note the naming
convension of putting an "`r`" after the "from" type:

* `mbrtowc()`---multibyte to wide character
* `wcrtomb()`---wide character to multibyte
* `mbsrtowcs()`---multibyte string to wide character string
* `wcsrtombs()`---wide character string to multibyte string

These are really similar to their non-restartable counterparts, except
they require you pass in a pointer to your own `mbstate_t` variable. And
also they modify the source string pointer (to help you out if invalid
bytes are found), so it might be useful to save a copy of the original.

Here's the example from earlier in the chapter reworked to pass in our
own `mbstate_t`.

``` {.c .numberLines}
#include <stdio.h>
#include <stdlib.h>
#include <stddef.h>
#include <wchar.h>
#include <string.h>
#include <locale.h>

int main(void)
{
    // Get out of the C locale to one that likely has the euro symbol
    setlocale(LC_ALL, "");

    // Original multibyte string with a euro symbol (Unicode point 20ac)
    char *mb_string = "The cost is \u20ac1.23";  // €1.23
    size_t mb_len = strlen(mb_string);

    // Wide character array that will hold the converted string
    wchar_t wc_string[128];  // Holds up to 128 wide characters

    // Set up the conversion state
    mbstate_t mbs;
    memset(&mbs, 0, sizeof mbs);  // Initial state

    // mbsrtowcs() modifies the input pointer to point at the first
    // invalid character, or NULL if successful. Let's make a copy of
    // the pointer for mbsrtowcs() to mess with so our original is
    // unchanged.
    //
    // This example will probably be successful, but we check farther
    // down to see.
    const char *invalid = mb_string;

    // Convert the MB string to WC; this returns the number of wide chars
    size_t wc_len = mbsrtowcs(wc_string, &invalid, 128, &mbs);

    if (invalid == NULL) {
        printf("No invalid characters found\n");

        // Print result--note the %ls for wide char strings
        printf("multibyte: \"%s\" (%zu bytes)\n", mb_string, mb_len);
        printf("wide char: \"%ls\" (%zu characters)\n", wc_string, wc_len);
    } else {
        ptrdiff_t offset = invalid - mb_string;
        printf("Invalid character at offset %td\n", offset);
    }
}
```

For the conversion functions that manage their own state, you can reset
their internal state to the initial one by passing in `NULL` for their
`char*` arguments, for example:

``` {.c}
mbstowcs(NULL, NULL, 0);   // Reset the parse state for mbstowcs()
mbstowcs(dest, src, 100);  // Parse some stuff
```

For I/O, each wide stream manages its own `mbstate_t` and uses that for
input and output conversions as it goes.

And some of the byte-oriented I/O functions like `printf()` and
`scanf()` keep their own internal state while doing their work.

Finally, these restartable conversion functions do actually have their
own internal state if you pass in `NULL` for the `mbstate_t` parameter.
This makes them behave more like their non-restartable counterparts.

[i[Multibyte characters-->parse state]>]
[i[Wide characters]>]

## Unicode Encodings and C

In this section, we'll see what C can (and can't) do when it comes to
three specific Unicode encodings: UTF-8, UTF-16, and UTF-32.

### UTF-8

[i[Unicode-->UTF-8]<]

To refresh before this section, read the [UTF-8 quick note,
above](#utf8-quick).

Aside from that, what are C's UTF-8 capabilities?

Well, not much, unfortunately.

[i[`u8` UTF-8 prefix]<]

You can tell C that you specifically want a string literal to be UTF-8
encoded, and it'll do it for you. You can prefix a string with `u8`:

``` {.c}
char *s = u8"Hello, world!";

printf("%s\n", s);   // Hello, world!--if you can output UTF-8
```

Now, can you put Unicode characters in there?

``` {.c}
char *s = u8"€123";
```

[i[`u8` UTF-8 prefix]>]

Sure! If the extended source character set supports it. (gcc does.)

What if it doesn't? You can specify a Unicode code point with your
friendly neighborhood `\u` and `\U`, [as noted above](#unicode-in-c).

But that's about it. There's no portable way in the standard library to
take arbirary input and turn it into UTF-8 unless your locale is UTF-8.
Or to parse UTF-8 unless your locale is UTF-8.

So if you want to do it, either be in a UTF-8 locale and:

[i[`setlocale()` function]<]

``` {.c}
setlocale(LC_ALL, "");
```

or figure out a UTF-8 locale name on your local machine and set it
explicitly like so:

``` {.c}
setlocale(LC_ALL, "en_US.UTF-8");  // Non-portable name
```

[i[`setlocale()` function]>]

Or use a [third-party library](#utf-3rd-party).

[i[Unicode-->UTF-8]>]

### UTF-16, UTF-32, `char16_t`, and `char32_t`

[i[Unicode-->UTF-16]<]
[i[Unicode-->UTF-32]<]
[i[`char16_t` type]<]
[i[`char32_t` type]<]

`char16_t` and `char32_t` are a couple other potentially wide character
types with sizes of 16 bits and 32 bits, respectively. Not necessarily
wide, because if they can't represent every character in the current
locale, they lose their wide character nature. But the spec refers them
as "wide character" types all over the place, so there we are.

These are here to make things a little more Unicode-friendly,
potentially.

To use, include `<uchar.h>`. (That's "u", not "w".)

This header file doesn't exist on OS X---bummer. If you just want the
types, you can:

``` {.c}
#include <stdint.h>

typedef int_least16_t char16_t;
typedef int_least32_t char32_t;
```

But if you also want the functions, that's all on you.

[i[`u` Unicode prefix]<]
[i[`U` Unicode prefix]<]

Assuming you're still good to go, you can declare a string or character
of these types with the `u` and `U` prefixes:

``` {.c}
char16_t *s = u"Hello, world!";
char16_t c = u'B';

char32_t *t = U"Hello, world!";
char32_t d = U'B';
```

[i[`char32_t` type]>]
[i[`u` Unicode prefix]>]
[i[`U` Unicode prefix]>]

Now---are values in these stored in UTF-16 or UTF-32? Depends on the
implementation.

But you can test to see if they are. If the macros [i[`__STDC_UTF_16__`
macro]] `__STDC_UTF_16__` or [i[`__STDC_UTF_32__ macro`]]
`__STDC_UTF_32__` are defined (to `1`) it means the types hold UTF-16 or
UTF-32, respectively.

If you're curious, and I know you are, the values, if UTF-16 or UTF-32,
are stored in the native endianess. That is, you should be able to
compare them straight up to Unicode code point values:

``` {.c}
char16_t pi = u"\u03C0";  // pi symbol

#if __STDC_UTF_16__
pi == 0x3C0;  // Always true
#else
pi == 0x3C0;  // Probably not true
#endif
```

[i[`char16_t` type]>]
[i[Unicode-->UTF-16]>]
[i[Unicode-->UTF-32]>]

### Multibyte Conversions

You can convert from your multibyte encoding to `char16_t` or `char32_t`
with a number of helper functions.

(Like I said, though, the result might not be UTF-16 or UTF-32 unless the
corresponding macro is set to `1`.)

All of these functions are restartable (i.e. you pass in your own
`mbstate_t`), and all of them operate character by
character^[Ish---things get funky with multi-`char16_t` UTF-16
encodings.].

|Conversion Function|Description|
|-|-|
|[i[`mbrtoc16()` function]]`mbrtoc16()`|Convert a multibyte character to a `char16_t` character.|
|[i[`mbrtoc32()` function]]`mbrtoc32()`|Convert a multibyte character to a `char32_t` character.|
|[i[`c16rtomb()` function]]`c16rtomb()`|Convert a `char16_t` character to a multibyte character.|
|[i[`c32rtomb()` function]]`c32rtomb()`|Convert a `char32_t` character to a multibyte character.|

### Third-Party Libraries {#utf-3rd-party}

For heavy-duty conversion between different specific encodings, there
are a couple mature libraries worth checking out. Note that I haven't
used either of these.

* [flw[iconv|Iconv]]---Internationalization Conversion, a common
  POSIX-standard API available on the major platforms.
* [fl[ICU|http://site.icu-project.org/]]---International Components for
  Unicode. At least one blogger found this easy to use.

If you have more noteworthy libraries, let me know.

[i[Unicode]>]
