<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Projekte auf Dateien aufteilen

[i[Multifile projects]<]

Bislang haben wir nur Spielzeugprogamm betrachtet die größtenteils in
eine einzelne Datei passen. Komplexe C Programme bestehen aber aus
mehreren Dateien die gemeinsam kompiliert und zu einem ausführbaren
Programm verlinkt werden.

Dieses Kapitel betracht üblich Muster und Verfahren die verwendet werden
um mit großen Projekten umzugehen.

## Includes und Prototypen von Funktionen  {#includes-func-protos}

[i[Multifile projects-->includes]<]
[i[Multifile projects-->function prototypes]<]

Sehr häufig kommt vor, dass Funktionen, die in einer Datei definiert
sind, von einer anderen Datei ausgeführt werden sollen.

Das funktioniert ohne weiteres, erzeugt aber eine Warnung. Probieren wir
es und versuchen anschließend, die Warnung zu beseitigen.

Bei diesen Beispielen führen wir die jeweilgen Dateinamen als Kommentar
auf.

Um zu kompilieren, müssen alle Source-Dateien auf der Kommandozeile
genannt werden.

``` {.zsh}
# output file   source files
#     v            v
#   |----| |---------|
gcc -o foo foo.c bar.c
```

Im obigen Beispiel wird aus `foo.c` und `bar.c` ein Program names `foo`
gebaut.

Schauen wir uns den Quelltext von `bar.c` an:

``` {.c .numberLines}
// File bar.c

int add(int x, int y)
{
    return x + y;
}
```

`foo.c` enthält `main()` und sieht so aus:

``` {.c .numberLines}
// File foo.c

#include <stdio.h>

int main(void)
{
    printf("%d\n", add(2, 3));  // 5!
}
```

<!-- Note: main in backticks? -->

Von `main()` rufen wir zwar `add()` auf --- aber `add()` befindet sich
in einer ganz anderen Datei! Und zwar `bar.c` und das obwohl sich der
Aufruf in `foo.c` befindet! See how from `main()` we call `add()`---but
`add()` is in a completely different source file! It's in `bar.c`, while
the call to it is in `foo.c`!

Bauen wir das Ganze wie folgt:

``` {.zsh}
gcc -o foo foo.c bar.c
```

erhalten wir diese Fehlermeldung:

``` {.default}
error: implicit declaration of function 'add' is invalid in C99
```

(Vielleicht auch nur eine Warnung. Die sollte man nicht ignorieren. Bei
C NIE Warnungen ignorieren; man muss auf alle eingehen.)


Vielleicht erinnerst Du Dich noch an den [Abschnitt über
Prototypen](#prototypes). Dort wurde erwähnt, dass implizite
Deklarationen aus modernem C verbannt worden sind und es keinen
legitimen Grund gibt, sie in neuem Code zu verwenden. Wir sollten das
fixen.

Mit `implizierte Deklaration` is gemeint, das wir eine Funktion
verwenden, in diesem Fall `add()` ohne C darüber Informationen zu
liefern. C möchte aber wissen wie der Rückgabetyp lautet, welche
Argumente erwartet sind, usw.

Wir habe bereits gesehen, wie man das mit _Protoypen_ für die Funktion
lösen kann. Und, siehe da, wenn wir `foo.c` einen Prototyp verpassen,
funktioniert alles.

``` {.c .numberLines}
// File foo.c

#include <stdio.h>

int add(int, int);  // Add the prototype

int main(void)
{
    printf("%d\n", add(2, 3));  // 5!
}
```

Keine Fehler!

Leider nervt es ständig Protypen tippen zu müssen, wenn man eine
Funktion verwenden möchte. Immerhin haben wir auch `printf()` verwendet
und mussten keinen Prototyp abtippen. 

Wenn Du Dich bitte an `hello.c` am Anfang des Buches zurück erinnern
würdest. Dort _haben wir tatsächlich den Protypen von `printf()`
eingefügt_! Der befindet sich in der Datei `stdio.h` und die wiederrum
wurde mit Hilfe von `#include` eingefügt!

Geht das auch mit unserem `add()`? Können wir ein Protyp erstellen und
in eine Header Datei stecken?

Klar doch!

Per Default haben Header Dateien in C die Endung `.h`. Und häufig, aber
nicht immer, heissen sie ansonsten so wie die dazugehörige `.c` Datei.
Erstellen wir also eine `bar.h` Datei als Brüderchen für `bar.c` und
stopfen einen Prototypen rein:

``` {.c .numberLines}
// File bar.h

int add(int, int);
```

Jetzt muss nur noch `foo.c` angepasst werden um die diese Header-Datei
einzufügen. Dazu verwenden wir die `#include` Direktive mit mit
doppelten Anführungszeichen statt eckigen Klammern:

``` {.c .numberLines}
// File foo.c

#include <stdio.h>

#include "bar.h"  // Include from current directory

int main(void)
{
    printf("%d\n", add(2, 3));  // 5!
}
```

Und siehe da: keine Protyp mehr nötig -- der wurde aus `bar.h` ergänzt.
Jetzt können _alle_ Dateien, von denen aus `add()` verwendet werden soll
den Protype mittles `#include "bar.h"` einfügen und sparen sich das
Tippen.

Wie Du Dir vermutlich denken kann, führt das `#include` dazu, dass die
benannte Datei _genau an dieser Stelle_ in Deinen Quelltext
eingefügt^[An.d.Üb. ja "eingefügt"! "inkludiert" ist kein richtiges
Wort, klingt Scheisse und bedeutet, wenn überhaupt, etwas anderes.], so
als hättest Du die Datei selber abgetippt.

Und jetzt der mit Spannung erwartete Augenblick:

``` {.zsh}
./foo
5
```

Tatsache, das Ergebnis von$2+3$! Hurra.

Wenn Du schon ein Bier ausgepackt hast, hast Du Dich allerdings zu früh
gefreut. Wir haben es aber fast geschafft, nur noch ein bisschen
Boilerplate drum herum.

[i[Multifile projects-->function prototypes]>]

## Mit Wiederholtem Einfügen von Headern umgehen

Header Dateien fügen häufige weitere Header, die von der zugehörigen C
Datei benötigt werden mit `#include` zusammen. Und warum auch nicht?

Und es kann passieren, dass eine Header Datei mehrfach eingefügt wird.
Vielleicht kein Problem, aber vielleicht resultieren daraus Fehler. Und
wir haben natürlich keinen Einfluss darauf, wie häufig andere unsere
Header mit `#include` irgendwo einfügen.

Noch schlimmer wäre eine Situation in der `a.h` den Header `b.h` einfügt
und -- halt Dich fest -- `b.h` wiederrum `a.h` einfügt. Das wäre eine
Endlosschleife!

Der Versuch, so etwas zu bauen, resultiert in einem Fehler:

``` {.default}
error: #include nested depth 200 exceeds maximum of 200
```

Wir benötigen einen Mechanismus der verhindert, dass ein Header
eingefügt werden kann, sobald er bereits mit `#incude` eingefügt wurde.

** Das folgende Geraffel ist so üblich, dass Du es verinnerlichen
solltest und bei jeder Header Datei, die Du schreibst verwenden
solltest! **

Eine übliche Art damit umzugehen ist es, im Rahmen des ersten
`#include`-Vorgang eine Präprozessor Variabel zu definieren, um bei
nachfolgenden `#include`s prüfen zu können, ob die Datei bereits
eingefügt worden ist.

Der geläufigste Variabelnamen für diesen Zweck ist der Dateiname,
grossgeschrieben und mit Unterstrich statt Punkt. Aus `bar.h` wird also:
`BAR_H`.

Man prüft also ganz oben in der Header Datei, ob sie bereits eingefügt
worden ist, und wenn ja, kommentiert man den Rest sozusagen aus.

(Der Variabelnname sollte nicht mit einem anführenden Unterstricht
beginnen, weil das reserviert ist. Auch nicht mit zwei Unterstrichen.
Auch reserviert.)

``` {.c .numberLines}
#ifndef BAR_H   // If BAR_H isn't defined...
#define BAR_H   // Define it (with no particular value)

// File bar.h

int add(int, int);

#endif          // End of the #ifndef BAR_H
```

Das verhindert wirksam, dass der Header mehrfach eingeführt wird, egal
wir häufig `#include` verwendet wird.

<!--Note: I realize it's non standard, but it may be worth it to mention
#pragma once round about here ...-->

[i[Multifile projects-->includes]>]

## `static` und `extern`

[i[`static` storage class]<]
[i[`extern` storage class]<]
[i[Multifile projects-->`static` storage class]<]
[i[Multifile projects-->`extern` storage class]<]

Man verwendet das Schlüsselwort `static`, um sicherzustellen, das
Variabeln und Funktionen _nicht_ ausserhalb der aktuellen Datei sichtbar
sind, wenn ein Projekt aus mehreren Dateien besteht.

Andererseits kann man auf Objekte in anderen Dateien mittels `extern`
verweisen.

Für Näheres beachte bitte die Abschnite zu
[`static`](#static) und [`extern`](#extern) _storage-class Specifier_ (dt. Speicherklassenbezeichner).

[i[`static` storage class]>]
[i[`extern` storage class]>]
[i[Multifile projects-->`static` storage class]>]
[i[Multifile projects-->`extern` storage class]>]

## Mit Objekt-Dateien kompilieren

[i[Object files]<]

Object Datein kommen in dem Standard nicht vor, sind aber Usus in
99.999% der C Welt.

Man C Dateien zu eine Zwichendarstellung names _Object Datei_ (eng.
_object file_) kompilieren. Darin befindet sich kompilierter
Maschinencode der allerdings noch nicht zu einem ausführbaren Progamm
gemacht worden ist.

Unter Windows haben Object Dateien die Endung `.OBJ`; auf Unix-artigen
Systemen enden sie `.o`.

[i[`gcc` compiler]<]

Mit `gcc` können wir sie mit dem `-c` (nur _compilieren_) Flag erzeugen.

``` {.zsh}
gcc -c foo.c     # produces foo.o
gcc -c bar.c     # produces bar.o
```

Schlussendlich können Objekt Dateien dann zu einem Programm ge_link_t
werden:

``` {.zsh}
gcc -o foo foo.o bar.o
```

_Voila_, ein ausführbares Progamm `foo` zusammengezaubert aus zwei
Objektdateien!

Und jetzt fragst Du Dich .. na und? Wir können doch auch einfach:

``` {.zsh}
gcc -o foo foo.c bar.c
```

machen und zwei [flw[boids|Boids]] gleichzeitig erschlagen?

[i[`gcc` compiler]>]

Für kleine Programm geht das wunderbar. Ich mache das ständig.

Bei großen Programmen können wir es uns zu Nutzen machen, dass
Objekt-Dateien Kompilieren ziemlich zeitaufwendig ist während
Objekt-Datei Linken ziemlich flott geht.

Der Vorteil zeigt sich deutlich, wenn man das `make` Werkzeug verwendet,
welches nur Sourcen kompiliert, die neuer sind als bereits erzeugte
Object-Dateien.

Gehen wir mal von 1000 C Dateien aus. Die können wir anfangs alle
kompilieren (lahm!) und dann die resultierenden Objekt-Dateien zu einem
Progamm zusammenlinken (flott!).

Nehmen wir nun an, Du nimmst nur eine klitzekleine Änderung an einer
einzigen C Datei vor. Und jetzt der Trick: _Du musst nur diese eine
Objekt-Datei neu kompilieren_! Und dann flott das Programm
zusammenbauen. Die übrigen C Dateien benötigt man gar nicht mehr.

Anders ausgedrückt verringern wir radikal die zum kopmilieren benötigte
Zeit in dem wir uns auf die Objekt-Dateien beschränken, die gerade
benötigt werden. (Es sei denn, wir führen ein `clean` Build durch, bei
dem alle Objekt-Dateien neu erstellt werden.)

[i[Object files]>]
[i[Multifile projects]>]
