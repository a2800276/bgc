<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Zeichenkette (Strings)

[i[Strings]<]Endlich! Einfache Zeichenketten!

Leider stellt sich herraus, dass es in C gar keine Zeichenketten (eng.
Strings) gibt. Pecht gehabt! Wie hätte es anderes kommen können...
Zeichenketten sind Zeiger! (Anm.d.Üb. ich verwende die dt. und eng.
Begriff, so wie ich sie mündlich verwenden würde: weitesgehend
austtauschbar und wie es sich für mich am besten anfühlt.) 

So wir Arrays, gibt es Strings in C nur ein _kleines Bisschen_.

Schauen wir sie uns trotzdem an -- ist gar nicht schlimm.

## String Literals (Zeichenkettenliterale)

[i[String literals]<]
Lass uns ein Wort über die direkte Darstellung von Zeichenketten in
Programmcode verlieren (dt. Zeichenkettenliteral, ich bevorzuge _string
literal_ oder nur _Literal_). Dabei handelt es sich um eine Sequenz von
Buchstaben in _doppelten_ Anführungszeichen (`"`). (Einfache
Anführungszeichen umgeben einzelne Zeichen (`char`s) und dabei handelt
es sich um etwas vollkommen anderes.)

Beispiele:

``` {.c}
"Hello, world!\n"
"This is a test."
"When asked if this string had quotes in it, she replied, \"It does.\""
```

Der erste schließt mit einen Zeilenvorschub (_newline_, `\n`, ASCII
`0x0A`) ab -- das sieht man recht häufig.

Das letzte Beispiel enthält eingebettete Anführungszeichen die mit einem
Backslash (`\`) angeführt werden müssen (man spricht von maskieren oder
_escape_'n ), um zu zeigen, dass sie Teil der Zeichenkette sind.  So
kann der Copmiler zwichen einem absschließendes Anführungszeichen und
einem, welches gedruckt werden soll, unterscheiden.

## String Variabeln

[i[String variables]<]
Da wir jetzt wissen, wie man _Literals_ zusammenbaut, weisen wir eins
einer Variabel zu damit wir was damit anstellen können.

``` {.c}
char *s = "Hello, world!";
```

Man beachte den Typ: ein Zeiger auf `char`. _Eigentlich_ handelt es
sich bei der String-Variabel `s` um enen Zeiger auf das erster Zeichen
der Zeichenkette, namentlich `H`.

Und nun können wir sie mit dem _Format specifier_ (dt.
Umwandlundsbezeichner) `%s` (steht ein für "String") ausgeben lassen:

``` {.c}
char *s = "Hello, world!";

printf("%s\n", s);  // "Hello, world!"
```
[i[String variables]>]

## String Variabeln als Arrays

[i[String variables-->as arrays]<]
Eine zweite Option entspricht beinah der `char*` Fassung oben:

``` {.c}
char s[14] = "Hello, world!";

// or, if we were properly lazy and have the compiler
// figure the length for us:

char s[] = "Hello, world!";
```

D.h. Du kannst Arraynotation verwenden, um auf einzelne Zeichen im
String zuzugreifen. Das nutzen wir, um alle Zeichen eines Strings auf
der selben Zeile zu drucken:

<!-- Translator Note: shouldn't this read ... print all chars .. on an
individual line ... ? -->

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    char s[] = "Hello, world!";

    for (int i = 0; i < 13; i++)
        printf("%c\n", s[i]);
}
```

Hier verwenden wir den `%c` _Specifier_ um ein einzelnes Zeichen
(c steht für `char`) auszugeben.

Un selbstverständlich funktioniert das immer noch, wenn wir `s` als
`char*` definieren.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    char *s = "Hello, world!";   // char* here

    for (int i = 0; i < 13; i++)
        printf("%c\n", s[i]);    // But still use arrays here...?
}
```

Und trotzdem können wir immer noch Arraynotation verwenden um die
Ausgabe zu bewältigen. Das ist überraschend, aber nur, weil wir die
Array / Zeiger Äquivalenz noch nicht richtig besprochen haben. Noch ein
geheimnisvoller Hinweis, dass Arrys und Zeiger tief im Inneren dasselbe
sind.
[i[String variables-->as arrays]>]

## String _Initializer_ (Initialisierung von Strings)

[i[Strings-->initializers]<]
Wie Strings anhand von _Literals_ initialisiert werden können, habe wir
bereits gesehen:

``` {.c}
char *s = "Hello, world!";
char t[] = "Hello, again!";
```

Die zwei Zeilen sind aber subtil unterschiedlich.

Die hier ist ein Zeiger auf ein Zeichenkettenliteral (als ein Zeiger auf
das erste Zeichen einer Zeichenkette):

``` {.c}
char *s = "Hello, world!";
```

Versuchst Du etwas, den String zu verändern:

``` {.c}
char *s = "Hello, world!";

s[0] = 'z';  // BAD NEWS: tried to mutate a string literal!
```

So handelt es sich um _undefined behavior_. Je nach Laufzeit wird Dein
Programm sehr wahrscheinlich abstürzen.

Deklariert man es aber als Array, so verhält sich das Programm anders.
Dann handelt es sich plötzlich um eine veränderbare _Kopie_ eines
Strings, die wir einfach so verändern können.

``` {.c}
char t[] = "Hello, again!";  // t is an array copy of the string 
t[0] = 'z'; //  No problem

printf("%s\n", t);  // "zello, again!"
```

Als denk dran: ein Zeiger auf ein _string literal_ darf nicht verändert
werden. Aber ... wenn Du eine Zeichenkette in doppelte Anführungszeichen
setzt und das verwendest, um ein Array zu initialisieren ist es
plötzlich kein _string literal_ mehr.
[i[Strings-->initializers]>]

## Die Länge einer Zeichenkette bestimmen

[i[Strings-->getting the length]<] Geht nicht, da C das für Dich nicht
nachhält. Und wenn ich "geht nicht" sagen, meine ich an dieser Stelle
"geht"^[Obwohl es stimmt, dass C das nicht für Dich nachhält].
`<string.h>` enhält eine Funktion namens `strlen()` die verwendet werden
kann, um die Länge von Strings in Bytes gemessen ermittelt^[Wenn Du ein
einfachen Zeichensatz mit 8-bit Zeichen verwendest, entspricht ein
Zeichen einem Byte. Das stimmt aber keinesfalls für alle Zeichensätze.]

``` {.c .numberLines}
#include <stdio.h>
#include <string.h>

int main(void)
{
    char *s = "Hello, world!";

    printf("The string is %zu bytes long.\n", strlen(s));
}
```

Die `strlen()` Funktion gibt den Typ `size_t` zurück, es handelt sich um
einen ganzzahligen Typ, mit dem wir rechnen können. Augeben geschieht
mit `%zu`.

Die Ausgabe des Beispiels von oben sieht so aus:

``` {.default}
The string is 13 bytes long.
```

Toll! Wir können also _doch_ die Länge von Zeichenketten ermitteln!
[i[Strings-->getting the length]>]

Nur ... wenn C sich die Länge nicht merkt, woher weiß es dann, wie lang
die Zeichenkette ist?

## Terminierung von Zeichenketten (String Termination)

[i[Strings-->termination]<]
In C verhalten sich Zeichenketten ein bisschen anders im Vergleich zu
fast allen modernen Programmiersprachen.

Bei der Definition einer neuen Sprache stehen im Prinzip zwei
Implementierungsoption für Zeichenketten im Speicher zur Wahl:

1. Die Bytes werden gemeinsam mit einer Zahl abgelegt, welche die Länge
   des Strings angibt.

2. Die Bytes werden im Speicher abgelegt und das Ende der Zeichenkette
   wird durch ein spezielles Byte, dem _Terminator_, markiert.

Sind Zeichenketten, die länger als 255 Zeichen sind gewünscht, erfordert
die erste Option mindestens 2 Bytes, um die Länge zu notieren. Option 2
benötigt nur ein Byte um den Abschluss zu markieren. Ein bisschen
Platzersparnis.

Heutzutage klingt es lächerlich sich über einen Byte Gedanken zu mache
(oder selbst 3 -- genügend Sprachen erlauben Dir gerne, Gigabyte-grosse
Zeichenketten zu verwenden). Aber _damals_ sah das noch anders aus.

C hat sich also für die zweite Option entschlossen. C definiert eine
"Zeichenkette" anhand von zwei Charaktereigenschaften:

* Ein Zeiger auf das erste Zeichen des Strings.
* Ein Byte mit Wert 0 (oder `NUL` Zeichen^[Etwas vollkommen anderes als
  ein `NULL` Zeiger und ich werde immer `NUL` sagen, wenn ich Zeichen
  meine und `NULL` wenn ich über Zeiger spreche.] im Speicher, irgendwo
  hinter dem Zeiger, der auf den Anfang der Zeichenkette deutet.

Das `NUL` Zeichen kann man in C als `\0` notieren, was aber selten
nötig ist.

Zeichenketten in doppelten Anführungszeichen werden automatisch,
implizit mit `NUL` beendet.

``` {.c}
char *s = "Hello!";  // Actually "Hello!\0" behind the scenes
```

Mit diesem Wissen bewaffnet können wir also unsere eigene
`strlen()` Funktion schreiben, die so lange `char`s in einer
Zeichenkette zählt, bis sie auf ein `NUL` stößt.

Die Prozedur hält einfach nach einem `NUL` Auschau und zählt
dabei^[Später lernen wir eine elegantere Methode mit
Zeiger-Arithmetik.]:

``` {.c}
int my_strlen(char *s)
{
    int count = 0;

    while (s[count] != '\0')  // Single quotes for single char
        count++;

    return count;
}
```

Das ist im wesentlich wir das eingebaute `strlen()` funktioniert.
[i[Strings-->termination]>]

## Strings kopieren

[i[Strings-->copying]<]

String kann man mit dem Zuweisungsoperator (`=`) nicht kopieren. Das
kopiert nur den Zeiger auf das erste Zeichen der Zeichenkette ... und Du
hast zwei Zeiger, die auf dieselben Zeichen zeigen:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    char s[] = "Hello, world!";
    char *t;

    // This makes a copy of the pointer, not a copy of the string!
    t = s;

    // We modify t
    t[0] = 'z';

    // But printing s shows the modification!
    // Because t and s point to the same string!

    printf("%s\n", s);  // "zello, world!"
}
```

Um eine Kopie der Zeichenkette zu erstellen, muss sie Byte für Byte
kopiert werden -- das wird durch die `strcopy()` Funktion
vereinfacht^[Es gibt eine weniger gefährliche Variante namens `strncpy` die Du eigentlich verwenden solltest, mehr dazu später.].

<!-- Translator Note: As there is consensus that strncpy should almost
definitely be used, why introduce strcpy at all? This will just get
beginners into bad habits. IMO, strcpy should be the "of historical
interest " footnote -->

Bevor wir anfangen die Zeichenkette zu kopieren, müssen wir dafür
Sorge tragen, das ausreichend Platz für die Kopie zur Verfügung steht.
Das Zielarray für die Kopie muss mindestens so lang sein, wie die
Zeichenkette, die kopiert wird.

``` {.c .numberLines}
#include <stdio.h>
#include <string.h>

int main(void)
{
    char s[] = "Hello, world!";
    char t[100];  // Each char is one byte, so plenty of room

    // This makes a copy of the string!
    strcpy(t, s);

    // We modify t
    t[0] = 'z';

    // And s remains unaffected because it's a different string
    printf("%s\n", s);  // "Hello, world!"

    // But t has been changed
    printf("%s\n", t);  // "zello, world!"
}
```

Bei `strcpy()` ist das erste Argument das Ziel, in das hineinkopiert
wird, das zweite Argument ist der Zeiger auf die Quelle, die kopiert
wird. Ich merke mir das so: die Reihenfolge entspricht genau der
Reihenfolge, die man bei einer einfache Zuweisung mit `=` verwendet.

[i[Strings-->copying]>][i[Strings]>]
