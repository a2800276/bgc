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

Das klingt nervig, schauen wir, ob man das auch einfacher geht.

[i[File I/O-->text files, reading]>]

## End of File: `EOF`

[i[`EOF` end of file]<]
Es gibt ein besonders Zeichen names `EOF`, das als Macro definiert ist.
Es handelt sich um das Zeichen, das `fgetc()` zurück gibt, wenn es am
Ende einer Datei angelangt ist, und man versucht, weiterzulesen.

<!-- Translator Note: have macros been introduced ? -->

Wir wär's mit einem weiteren Fun Fact? Es stellt sich herruas, das `EOF`
die Ursache dafür ist, dass `fgetc()` und Freunde ein `int` zurückgeben
müssen statt einem `char`. Bei `EOF` handelt es sich nicht wirklich um
ein richtiges Zeichen. Sein Wert liegt nicht im Bereich von `char`, da
`fgetc()` in der Lage sein muss, sowohl beliebige Byte-Werte als auch
`EOF` zurückzugeben, muss der Rückgabetyp umfangreicher sein, um mehr
Werte enthalten zu können. Also `int`. Du kannst Dich aber getrost
darauf verlassen, dass mit Ausnahme von Vergleichen mit `EOF`, es sich
immer um `char`s handeln wird.

Zurück in die Gegenwart! Mit diesem Wissen gewappnet lesen wir nun die
gesammte Datei in einer Schleife.

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

(Falls Dir Zeile 10 zu komisch vorkommen, versuche sie von innen nach
außen aufzubrechen, angefangen bei der innersten Klammer. Als erstes
weisen wir den Rückgabewert von `fgetc()` der Variabel `c` zu and
vergleich _anschliessend_ das Ergbenis mit `EOF`. Wir haben das halt
alles in eine Zeile gestopft. Das sieht jetzt noch vielleicht
umständlich aus, aber gewöhne Dich dran -- es handelt sich um
idiomatisches C.)

[i[`fgetc()` function]>]

Wenn wir das nun laufen lassen, erhalten wir:

``` {.default}
Hello, world!
```

Allerdings schauen wir uns immer noch jedes Zeichen einzeln an, und die
meisten Text Dateien betrachtet man sinnvoller zeilenweisen. Probieren
wir das mal.
[i[`EOF` end of file]>]

### Zeilenweise lesen

[i[File I/O-->line by line]<]

Wie kommt man also an eine ganze Textzeile? Mit [i[`fgets()`
function]<]`fgets()`! Zu den Argumenten: die Funktion erwartet einen
Zeiger auf ein `char` Buffer um die gelesenen Bytes zu halten, die
maximale Anzahl Bytes, die gelesen werden dürfen und ein `FILE*`, der
gelesen werden soll. Der Rückgabewert ist `NULL` wenn das Ende der Datei
erreicht ist oder ein Fehler auftritt. `fgets()` ist sogar so
zuvorkommend, dass es abschliessend die gelesene Zeichenkette mit `NUL`
terminiert^[Sollte der Puffer nicht ausreichen, um die ganze Zeile
einzulesen, macht es an dieser Stelle eine Pause und der nächste Aufruf
von `fgets()` setzt an der Stelle wieder an, weiterzulesen.].
[i[`fgets()` function]>]

<!-- Translator Note: shouldn't this read "parameter"? -->

Schreiben wir jetzt eine ähnliche Schleife wir eben, nur, dass wir
zeilenweise eine mehrzeilige Datei lesen.

Hier die Datei: `quote.txt`:

``` {.default}
A wise man can learn more from
a foolish question than a fool
can learn from a wise answer.
                  --Bruce Lee
```

Und hier ist ein bisschen Code, der die Datei zeilenweise liest und jede
Zeile durchnummeriert ausgibt:

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

Das Ergebnis:

``` {.default}
1: A wise man can learn more from
2: a foolish question than a fool
3: can learn from a wise answer.
4:                   --Bruce Lee
```
[i[File I/O-->line by line]>]

## Formattierte Eingabe

[i[File I/O-->formatted input]<]
Erinnerst Du Dich, wie wir mit `printf()` Ausgaben formatiert haben (und
in Erweiterung mit `fprintf()`, wie wir noch sehen werden?)

[i[`fscanf()` function]<]
Dasselbe geht mit `fscanf()`

Schauen wir uns eine Datei mit einer Reihe von Datenpunkten an. In
diesem Fall handelt es sich um Wale. Enthalten sind ihre Namen, Länge in
Metern und Gewicht in Tonnen. `whales.txt`:

``` {.default}
blue 29.9 173
right 20.7 135
gray 14.9 41
humpback 16.0 30
```

Wir _könnten_ das Ganze mit [i[`fgets()` function]]`fgets()` einlesen
und das Ergebnis mit`sscanf()` parsen (und in mancher Hinsicht wäre
dieses Vorgehen robuster im Umgang mit Fehlern), aber für dieses
Beispiel benutzten wir einfach `fscanf()` and lesen die Daten
unvermittelt.

`fscanf()` überspringt anführende Leerzeichen (eng. Whitespace) beim
lesen und gibt `EOF` zurück, wenn das Dateiende erreicht ist oder ein
Fehler auftritt. 

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

Das Ergbenis:

``` {.default}
blue whale, 173 tonnes, 29.9 meters
right whale, 135 tonnes, 20.7 meters
gray whale, 41 tonnes, 14.9 meters
humpback whale, 30 tonnes, 16.0 meters
```
[i[File I/O-->formatted input]>]

## In Text Dateien schreiben

[i[File I/O-->text files, writing]<]
[i[`fputc()` function]<]
[i[`fputs()` function]<]
[i[`fprintf()` function]<]

So wie wir  `fgetc()`, `fgets()`, and `fscanf()` verwenden, um aus
Text _Streams_ zu lesen, kann man `fputc()`, `fputs()`, and `fprintf()`
verwenden, um auf Sie zu schreiben.

Dazu müssen wir an `fopen()` ein `"w"` als zweites Argument übergeben,
um die Datei zu schreiben (eng. write) zu öffnen. Öffnet man eine
bestehende Datei im `"w"` Modus führt dazu, dass diese auf eine Länge
von 0 Bytes gekürzt wird und somit vollständig überschrieben.

Wir schmeissen ein einfaches Beispiel zusamen, das mittels verschiedener
Funktionen in `output.txt` schreibt.

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

Das erzeugt die Datei, `output.txt` mit dem folgenden Inhalt:

``` {.default}
B
x = 32
Hello, world!
```

Fun fact: da es sich bei `stdout` auch um eine Datei handelt, könnte man
Zeile 8 mit:

``` {.c}
fp = stdout;
```

ersetzten und das Programm hätte die Ausgabe auf die Konsole geschrieben
statt in eine Datei. Probier's man.
[i[File I/O-->text files, writing]>]

## Binary I/O mit Dateien

[i[File I/O-->binary files]<]

Bis jetzt sprechen wir nur über Text Dateien. Wir hatten aber vorhin
noch ein Tierchen erwähnt, nämlich _Binärdateien_ oder _binary Streams_.

Diese funktionieren ähnlich wie Text Dateien, nur das keinerlei Änderungen
vom I/O Subsystem vorgenommen werden, wie es mit Textdateien vorkommen
kann. Bei Binärdatein bekommt man rohe Bytes im _Stream_, das war's.

Bei Öffnen muss man nur beachten, dass man dem "Mode" ein `"b"` anfügt.
D.h. zu Lesen einer Binärdatei muss man sie mit `"rb"` öffnen, zu
Schreiben mit `"wb"`.

Da es sich um es sich um einen _Stream_ Bytes handelt der NUL Zeichen
enthalten kann und NUL Zeichen wiederrum in C als
Zeichenkettenterminierung dienen, wird die `fprintf()`-usw-Funktionen
nur selten mit Binärdateien verwendet.

[i[`fwrite()` function]<] Die üblichsten Funktionen sind stattdessen
[i[`fread()` function]]`fread()` und `fwrite()`, die eine vorgegebene
Anzahl Bytes in den _Stream_ schreiben. 

Zu Demonstrationszwecken schreiben wir ein paar Programme: Eins schreibt
eine Bytesequenz am Stück in eine Datei. Das zweite liest eine Datei
byteweise und gibt sie Byte für Byte aus^[Effizienter wäre es, im
zweiten Programm die gesammten am Stück zu lesen und sie _anschließend_
in einer Schleife auszugeben. Das zweite Programm soll als Beispiel
dienen.].

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

Die beide mittleren Argumente von `fwrite()` stechen ein bisschen raus.
Im Grunde genommen wollen wir der Funktion folgendes mitteilen: "Wir
wollen Elemente ausgeben die _so_ groß sind, und zwar _so_ viele davon."
So kann man ohne Umstände ein Array mit gleichgroßen Elementen am Stück
wegschreiben. Man muss nur die Größe und Anzahl angeben.

Im angeführten Beispiel geben wir an, dass jeder Eintrag so gross wie
`char` ist, und es sechs Stück davon gibt.

Das Ergebnis von Programm ist eine Datei names `output.bin`, die, wenn
man die in einem Texteditor öffnet uns nicht gerade freundlich grüßt. Es
handelt sich um Binärdaten, nicht Text, und noch dazu um zufällige
Daten, die ich mir ausgedacht habe.


Mit einem [flw[hex dump|Hex_dump]] Programm können wir uns die Rohdaten
anschauen:

``` {.default}
05 25 00 58 ff 0c
```

Und es stellt sich herraus, dass die Wert in Hexadezimal mit den
geschrieben Werten (in Dezimalschreibweise) übereinstimmen.

Vesuchen wir sie nun mit einem anderen Programm wieder einzulesen. Das
muss die Datei im Binäremodus zu lesen öffnen (`"rb"` Mode) und ließt
byteweise in einer Schleife:

[i[`fread()` function]<]
`fread()` hat die nützliche Eigenschaft, die Anzahl gelesener Bytes
zurückzugeben. Oder `0` am Dateiende. Die Schleife kann also laufen, bis
wir die die Null sehen.

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

Und siehe da, unsere urspünglichen Zahlen!

``` {.default}
5
37
0
88
255
12
```

Wuhu!
[i[File I/O-->binary files]>]

### Vorsicht bei `struct`s und Zahlen!

[i[File I/O-->with `struct`s]<]
Wie wir im Abschnitt über `struct`s gesehen habe, darf der Compiler
`struct`s beliebig mit Padding auffüllen. Unterschiedliche Compiler
können dafür andere Strategien nutzen. Sogar derselbe Compiler, auf eine
anderen Architektur betrieben könnte anderes vorgehen. Sogar derselbe
Compiler auf der selben Architektur. "Beliebig" halt.

Worauf ich hinaus will: einfach mit `fwrite()` eine gesamte `struct` zu
schreiben ist nicht portabel wenn man sich nicht sicher ist, wo Padding
landet.
[i[File I/O-->with `struct`s]>]

Wie geht man damit um. Einen Moment noch --- das betrachten wir, nachdem
wir uns ein verwandtes Problem angeschaut haben.

[i[File I/O-->with numeric values]<]
Zahlen!

Es stellt sich heraus, dass nicht alle Architekturen Zahlen auf die
selbe Art im Speicher darstellen.

Schauen wir uns an, was ein einfaches `fwrite()` mit einer 2-Byte langen
Zahl anstellt. Nutzen wir Hexadezimaldarstellung um jedes Byte deutlich
zu weigen. Der höchstwertige Byte (eng. _most significant_) nimmt den Wert
`0x12` and und der niederwertigste Byte (eng. _least significant_) den
Wert `0x34`.

``` {.c}
unsigned short v = 0x1234;  // Two bytes, 0x12 and 0x34

fwrite(&v, sizeof v, 1, fp);
```

Was landet im _Stream_?

Man würde meinen, dass dort ein `0x12` gefolgt von einem `0x34` steht
sollte, oder? Aber auf meinem Rechner ergibt der Hexdump folgendes:

``` {.default}
34 12
```

Warum falschrum?

Das hat mit dem Konzept von [i[Endianess]][flw[_endianess_|Endianess]] (dt. laut Wikipedia Byte-Reihenfolge eng. auch mbyte order) zu tun, und ist je nach Architektur unterschiedlich ausgeprägt. Manche schreiben den höchstwertigen Byte zuerst und andere den niederwertigsten.

Einen Zahltyp, der länger als ein Byte ist einfach wegzuschreiben ist
demzufolge nicht portabel^[Nebenbei erwähnt ist das auch der Grund warum
ich geschickt nur individuelle Bytes in den Beispielen zu `fwrite()`
und `fread()` verwendete.].

Eine ähnliches Problem besteht mit Fliesskommazahlen. Die meisten System
nutzten das selbe Format zu Darstellung von Fliesskommazahlen, das ist
aber leider nicht festgelegt.

<!-- Translator Note: possibly mention IEEE 754 here in a footnot to give people
somehting to google?-->

[i[File I/O-->with `struct`s]<]

Wie also gehen wir mit Zahlen und `struct`s um, um unsere Daten
portabel zu sichern?

Die Kurzform lautet [i[Data serialization]]_Serialisierung_ der Daten
(eng. _data serialization_). Dabei handelt es sich um einen allgemeinen
Begriff, der meint, dass man sich die Daten schnappt und explizit in
einer bestimmten, wohldefinierten Form schreibt, die auf allen
Plattformen genutzt werden kann.

Es gibt bereits Lösungen für das Problem, wie du dir sicher vorstellen
kannst. Bestehende Serialisierungs-Bibliotheken, wie beispielsweise
Google's [flw[protocol buffers|Protocol_buffers]], kümmern sich um die
komplizierten Details und ermöglichen sogar die Zusammenarbeit mit
anderen Programmiersprachen, die dieselben Mechanismen unterstützen.

Tu Dir (und allen Mitmenschen) einen Gefallen und serialisiere Daten
immer, wenn Du sie auf einen _Stream_ schreibst! So bleiben Dinge schöne
portabel, selbst wenn Dateien auf eine andere Architektur verschoben
werden.
[i[File I/O-->with `struct`s]>]
[i[File I/O-->with numeric values]>]
[i[File I/O]>]
