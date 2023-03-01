<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Gültigkeit (Scope) {#scope}

[i[Scope]<]
Scope is all about what variables are visible in what contexts.
Bei Scope dreht sich alles darum, welche Variabeln wann sichbar und
gültig sind. ( Je nach Kontext wird Scope als Geltungsbereich oder
Gültigkeit übersetzt )

## Block Scope

[i[Scope-->block]<]
Dieser Geltungsbereich umfasst beinah alle Variablen, die eine
Entwicklerin definiert und umfasst auch das, was in anderen Sprachen
"Funktion Scope" bezeichnet wird, also Variabeln, die innerhalb
Funktionen deklariert werden.

Die Regel lautet, dass Variabeln, die in einem mit geschweiften Klammern
umfassten Block deklariert werden innerhalb von dem Block gültig sind.

Wenn der Block einen weiteren Block enthält, so sind die im _inneren_
Block deklarierten Block auf diesen inneren Block beschränkt und im
äußeren nicht sichtbar.

Sobald der Scope eine Variabeln endet, kann sie nicht mehr verwendet
werden und Du musst davon ausgehen, dass ihr Wert im grossen [flw[bit
bucket|Bit_bucket]] im Himmel gelandet ist.

Ein Beispiel mit eingebetteten Scope:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int a = 12;         // Local to outer block, but visible in inner block

    if  (a == 12) {
        int b = 99;     // Local to inner block, not visible in outer block

        printf("%d %d\n", a, b);  // OK: "12 99"
    }

    printf("%d\n", a);  // OK, we're still in a's scope

    printf("%d\n", b);  // ILLEGAL, out of b's scope
}
```

### Wo sollte man Variabeln definieren?

Lustigerweise darf man --mit unwichtigen Einschränkungen-- überall
innerhalb eines Blocks definieren. Bei Scope handelt es sich um den
Block, sie können aber nicht vor ihrer Definition verwendet werden.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i = 0;

    printf("%d\n", i);     // OK: "0"

    //printf("%d\n", j);   // ILLEGAL--can't use j before it's defined

    int j = 5;

    printf("%d %d\n", i, j);   // OK: "0 5"
}
```

Früher erforderte C, dass alle im Block verwendeten Variabeln vor
jeglichem Code definiert worden sind. Das ist seit dem C99 Standard
nicht mehr der Fall.

### Variabeln verstecken
[i[Variable hiding]<]

### Variable Hiding
Wird in einem inneren Scope ein Variabel so benannt wie in dem
umfassenden, äußeren Scope, hat die Variabeln im innern Scope Vorrang.
Das heißt, sie _überdeckt_ die gleichnamige Variabeln im äußeren Scope
für den Zeitraum ihrer Gültigkeit.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i = 10;

    {
        int i = 20;

        printf("%d\n", i);  // Inner scope i, 20 (outer i is hidden)
    }

    printf("%d\n", i);  // Outer scope i, 10
}
```

Dir mag aufgefallen sein, dass ich auf Zeile 7 einfach mal einen Block
ins Rennen geschickt habe, obwohl weit und breit kein `for` oder `if`
Vorhanden ist. Das ist erlaubt. Gelegentlich gruppieren Entwickler so
lokale Variabeln zusammen. Generell ist das aber unüblich.
[i[Variable hiding]>]
[i[Scope-->block]>]

## Dateiweiter Scope

[i[Scope-->file]<]
Wird eine Variabel ausserhalb eines Blockes deklariert, handelt es sich
um _dateiweiten Scope_ (_file scope_). Einunddieselbe Variabel ist in allen
folgenden Funktionen der Datei sichtbar. Auch dateiweite Variablen
werden von gleichnamigen lokalen Variabeln überdeckt. 

Das entspricht grob "globalen" Scope in anderen Sprachen.

Zum Beispiel:

``` {.c .numberLines}
#include <stdio.h>

int shared = 10;    // File scope! Visible to the whole file after this!

void func1(void)
{
    shared += 100;  // Now shared holds 110
}

void func2(void)
{
    printf("%d\n", shared);  // Prints "110"
}

int main(void)
{
    func1();
    func2();
}
```

Wäre `shared` unten in der Datei deklariert, würde sie nicht
kompilieren. Variabeln müssen _vor_ jeglicher Verwendung deklariert
sein.

Es gibt noch weitere Möglichkeiten, dateiweite Elemente zu modifiziere,
und zwar mit
[static](#static) und [extern](#extern), darüber sprechen wir später
noch ausführlicher.
[i[Scope-->file]>]

## Scope von `for` Schleifen

[i[Scope-->`for` loop]<]
Ich weiß nicht wirklich, wie ich das hier nennen soll, leider hat
C11 §6.8.5.3¶1 es versäumt hier einen Namen zu vergeben. Das Konstrukt
kennen wir aber schon. Es kommt zum Einsatz, wenn Variabeln innerhalb
der ersten Klausel einer `for`-Schleife deklariert wird:

``` {.c}
for (int i = 0; i < 10; i++)
    printf("%d\n", i);

printf("%d\n", i);  // ILLEGAL--i is only in scope for the for-loop
```

In dem Beispiel fängt `i`s Gültigkeitsdauer in dem Augenblick, wo es
definiert wird und dauert bis zum Ende der Schleife an.

Wenn der Körper der Schleife aus einem Block besteht, sind alle im `for`
deklarierten Variablen innerhalb des Blocks sichtbar.

Es sei denn, sie werden im inneren Scope überdeckt, so wie in diesem
irrsinnigen Beispiel, dass fünfmal `999` ausgibt.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    for (int i = 0; i < 5; i++) {
        int i = 999;  // Hides the i in the for-loop scope
        printf("%d\n", i);
    }
}
```
[i[Scope-->`for` loop]>]

## Ein Wort bezügliche Funktion Scope

[i[Scope-->function]<]
Der C Standard erwähnt auch _function scope_, dieser wird aber nur mit
_Labels_ verwendet, ein Konstrukt, das wir noch nicht angesprochen
haben. Ein anders mal mehr dazu.
[i[Scope-->function]>]
[i[Scope]>]
