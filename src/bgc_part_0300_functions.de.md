<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Funktionen {#functions}

> _"Sir, not in an environment such as this. That's why I've also been
> programmed for over thirty secondary functions that---"_
>
> ---C3PO[i[C3PO]], before being rudely interrupted, reporting a
> now-unimpressive number of additional functions, _Star Wars_ script

[i[Functions]<]
So, wie in andern Sprachen gibt es in C auch das Konzept von
_Funktionen_

Funktionen können eine Vielzahl von _Argumenten_[i[Funktionsargumente]]
annehmen und geben einen Wert zurück. Eine wichtige Sache jedoch: Die
Typen der Argumente und des Rückgabewerts müssen deklariert werden -
denn so mag es C!

Schauen wir uns eine Funktion an. Hier eine Funktion die ein `int` als
Argument erwartet und ein `int` zurückgibt (_return't_)[i[`return`
statement]].

``` {.c .numberLines}
#include <stdio.h>

int plus_one(int n)  // The "definition"
{
    return n + 1;
}
 
```

Das `int` vor `plus_one` gibt des Typ des Rückgabewerts an.

Das `int n` deklariert, dass die Funktion ein `int` Argument erwartet,
welches im _Parameter_ `n`[i[Function parameters]] gespeichert wird. Bei
einem Parameter handelt es sich um eine besondere Art lokaler Variabel
in welche Argumente kopiert werden. 

Ich beharre an diese Stelle bisschen darauf, dass die Argumente in
Parmeter kopiert werden. Vieles in C kann man einfacher verstehen, wenn
man weiss, dass Parameter immer eine _Kopie_ der Argumente sind, nicht
das Argument selber. Aber gleich noch mehr dazu.

Folgen wir das Program bis nach `main()`, sehen wir den Funktionsaufruf,
der den Rückgabewert der lokalen Variabel `j` zuweist. 

``` {.c .numberLines startFrom="8"}
int main(void)
{
    int i = 10, j;
    
    j = plus_one(i);  // The "call"

    printf("i + 1 is %d\n", j);
}
```

> Kurz erwähnt, wichtig ist, dass die Funktion definiert worden ist,
> bevor sie aufgerufen wurde. Hätte ich das nicht so gemacht, würde sich
> der Copmiler nicht mit dem Aufruf in `main` anfangen können und würde
> sich mit einem "unknown function call" Fehler melden. Es gibt eine
> strukturiertere Art Funktionen bekannt zu machen, und zwar _function
> prototypes_, aber später mehr dazu.

> Before I forget, notice that I defined the function before I used it.
> If I hadn't done that, the compiler wouldn't know about it yet when it
> compiles `main()` and it would have given an unknown function call
> error. There is a more proper way to do the above code with _function
> prototypes_, but we'll talk about that later.

Ebenfalls beachtenswert, dass `main()`[i[`main()` function]] auch eine ganz normale Funktion ist!

Sie gibt `int` zurück.

Und was ist mit `void`[i[`void` type]]? es handelt sich um einen
reservierten Begriff (_keyword_) der angibt, dass ine Funktion keine
Argumente erwartet.

Man kann `void` auch als Rückgabetype von Funktionen angeben, die keinen
Wert zurückliefern.

``` {.c .numberLines}
#include <stdio.h>

// This function takes no arguments and returns no value:

void hello(void)
{
    printf("Hello, world!\n");
}

int main(void)
{
    hello();  // Prints "Hello, world!"
}
```

## Passing by Value {#passvalue}
## _Pass by Value_ {#passvalue}

[i[Pass by value]()]

Ich habe eben erwähnt, dass Argumente die an Funktionen übergeben werden
kopiert und in den entsprechenden Parametern ablegt werden.

Handelt es sich beim Argument um eine Variabel wird eine Kopie von dem
Wert der Variabel in dem Parameter gespeichert.

Noch allgemeiner bedeutet dass, das der gesamte übergebene Ausdruck des
Arguments vollständig ausgewertet wird und das Ergebnis in den Parameter
kopiert wird.

In jeden Fall ist der Wert der Parameter also unabhängig und hat keinen
Einfluss auf die Werte oder Variabeln die für den Funktionsaufruf als
Argument verwendet worden sind.

Schauen wir und ein Beispiel an. Spiel es im Kopf durch und versuche das
Ergebnis zu erraten, bevor Du es laufen lässt.

``` {.c .numberLines}
#include <stdio.h>

void increment(int a)
{
    a++;
}

int main(void)
{
    int i = 10;

    increment(i);

    printf("i == %d\n", i);  // What does this print?
}
```

Auf ersten Blick sieht es so aus, als ist der Wert von `i` anfangs `10`,
wird dann and `increment()` übergeben und dort um eins erhöht. Wenn ihr
`i` ausdrucken sollte es als `11` sein, oder?

> _"Get used to disappointment."_
>
> ---Dread Pirate Roberts, _The Princess Bride_

But it's not `11`---it prints `10`! How?
`11` stimmt aber nicht -- das Programm gibt `10` aus! Wie denn das?

Das resultiert aus der Tatsache, dass Ausdrücke, die an Funktionen
übergeben werden in Parameter _kopiert_ werden. Beim Parameter handelt
es sich um eine Kopie, nicht um das Original. 

Das `i` in `main()` hat den Wert `10`. Dann übergeben wir es an
`increment()`. Der entsprechende Parameter in der Funktion heißt `a`.

Die Kopie geschieht wir bei einer Zuweisung. Grob gesagt `a = i`. An
dieser Stellen hat `a` der Wert `10`. Und drüben in `main()` hat `i`
auch den Wert `10`. Beide haben aber nichts miteinander zu tun.

<!-- Translator Note: semantics of assignment was not discussed at this
point. I'm not sure what the intuition of someone who doesn't know the
sematics would be, but I could imagine them being just as suprised
about: a = 0; i=a ; i+=1; a!=1 -->

<!-- Translator Note: I explicitly stated that a and i have nothing to
do with each other and store the same vlaue only coincidentally -->

Wenn wir dann `a` auf `11` erhöhen, bleibt `i` von der Änderung unberührt!
`i` bleibt `10`.

Zum Schluss ist die Funktion fertig, alle lokale Variabeln werden
entsorgt (Tschüss, `a`!) und wir kehren zu `main()` zurück wo `i` immer
noch den Wert `10` hat.

Das drucken wir dann als `10` aus, und sind fertig!

Aus diesem Grund haben wir in dem vorangegangenen Beispiel von der
`plus_one()` Funktion, einen Wert mit `return` zurückgegeben und der
lokalen Variabel zugewiesen, um sie in `main()` verwenden zu können.

Wirkt ein bisschen einschränkende, oder? Dass man nur ein Stückchen
Daten von einer Funktion zurückerhalten kann. Es gibt aber noch einen
weiteren Kanal, um an Daten zu kommen. C Leute nennen ihn _pass by
reference_ aber darauf gehen wir ein andermal ein.

Aber keine noch so hochtrabender Name soll davon ablenken, dass _ALLES_
was an eine Funktion übergeben wird _AUSNAHMSLOS_ in entsprechende
Parameter kopiert wird und Funktionen immer nur mit ihren lokal Kopien
arbeiten können _EGAL WAS PASSIERT_. Behalt's im Hinterkopf für wenn wir
_pass by reference_ besprechen.
[i[Pass by value]>]

## Function Prototypes {#prototypes}

[i[Function prototypes]<]

Wenn Du Dich eine Ewigkeit ein paar Abschnitte zurück erinnerst, fällt
Dir vielleicht ein, dass ich erwähnte, dass Funktionen definiert sein
müssen bevor man sie verwenden kann. Sonst kennt der Compiler sie nicht
und würde vor Fehlern explodieren.

Das stimmt nicht ganz. Man kann dem Compiler im Vorhinein Hinweise
geben, dass eine Funktion Verwendung finden wird, die einen bestimmten
Rückgabewerte und Parameter Liste hat. So kann die Funktion an
beliebiger Stelle definiert werden. Sogar in einer ganz Datei. Die
einzige Vorraussetzug ist, dass der _function prototype_ (Der Prototyp
der Funktion der sich aus Parameter Liste und Rückgabetyp ergibt, die
Typ der Funktion) bekannt ist, bevor die Funktion aufgerufen wird.

<!-- Translator Note: define - declare, maybe explain define = implement -->

Glücklicherweise ist es einfach einen Protypen zu erstellen: einfach die
erste Zeile der Funktion kopiert nud ein Semikolon drantackern. Z.B.
ruft der folgende Code eine Funktion auf, die erst später definiert
wird. Das geht, weil der Protoyp der Funktion vorab definiert wird:

``` {.c .numberLines}
#include <stdio.h>

int foo(void);  // This is the prototype!

int main(void)
{
    int i;
    
    // We can call foo() here before it's definition because the
    // prototype has already been declared, above!

    i = foo();
    
    printf("%d\n", i);  // 3490
}

int foo(void)  // This is the definition, just like the prototype!
{
    return 3490;
}
```

Wenn eine Funktion vor ihrer Verwendung nicht deklariert wird (entweder
mit einem Prototype oder durch die eigentliche Definition) wird eine
_implicit declaration_[i[Implicit declaration]] ausgeführt. Das war im
ersten C Standard (C89) erlaubt, und auch mit einem Regelwerk
beschrieben. Das gilt aber heutzutage nicht mehr. Und ehrlich gesagt,
gibt es auch keinen Grund sich auf solches Verhalten zu verlassen.

Vielleicht ist Dir in diesem Zusammenhang etwas and unseren
Codebeispielen aufgefallen ... Wir benutzen andauerend die `printf()`
Funktion ohne sie zu definieren oder einen Prototyp zu deklarieren.
Wieso funktioniert solch ein Frevel? Tun wir gar nicht. Es gibt einen
Prototyp; der befindet sich in der Header Datei  `stdio.h` die wir immer
mittels `#include` reinkopieren.
[i[Function prototypes]>]

## Leere Parameter Listen

[i[Empty parameter lists]]
... sieht man immer mal wieder in altem Code, sollten aber nie neu
geschrieben werden. Verwende stattdessen lieber `void`[i[`void` type]]
um explizit anzugeben, dass keine Parameter erwartet werden. Es gibt nie^[Sag niemals
nie".] einen Grund, dies in modernem Code zu tun.

Sofern Du Dir merken kannst, immer brav `void` für leere Parameter
Listen in Funktionen und Prototypen zu tippen, kannst Du den Rests des
Abschnitts getrost sparen.

Es gibt zwei Situation wo leere Parameter auftreten könnten:

* In Definitionen von Funktionen
* In Prototypen von Funktionen

Schauen wir uns zuerst ein Definition an:

``` {.c}
void foo()  // Should really have a `void` in there
{
    printf("Hello, world!\n");
}
```

Die Spezifikation schreibt eindeutig vor, dass sich die Funktion in
dieser Situation so verhält, _als ob_ `void` verwendet wurde (C11
§6.7.6.3¶14), gibt es aus guten Grund `void`. Benutze `void`. 

Aber bei Funktions Prototypen ergibt sich ein _erheblicher_ Unterschied
ob `void`[i[`void` type-->in function prototypes]] verwendet wird oder
nicht:

``` {.c}
void foo();
void foo(void);  // Not the same!
```

Wird `void` im Protype weggelassen deutet das den Compiler darauf hin,
dass keine weiteren Informationen über die Parameter diser Funktion
vorliegen. Damit wird jeglich überprüfung von Typen deaktiviert.

Bei Prototype also **auf jeden Fall** `void` verwenden, um die leer
Parameter Liste explizit zu deklarieren.

[i[Functions]>]
