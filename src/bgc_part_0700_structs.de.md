<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Structs (strukturierte Datentypen){#structs}

[i[`struct` keyword]<]
In C gibt es ein Gebilde namens `struct`. Dabei handelt es sich um einen
frei-definierbaren Datentype der aus anderen Daten zusammengesetzt ist,
die unterschiedliche Typen haben dürfen.

Dabei handelt es sich um eine bequeme Möglichkeit, artverwandte
Variabeln zu einer einzigen Variabel zusammezufassen. Das kann nützlich
sein, um Variabeln an eine Funktion zu übergeben (so muss nur eine
Variabel übergeben werden) und, um Daten zu strukturieren und Code so
lesbarer zu gestalten.

Aus anderen Sprachen ist das Konzept von _Klassen_ und _Objekten_
vielleicht bekannt. Die gibt es in C nativ nicht ^[Auch wenn einzelne C
Elemente als "Objekt" bezeichnet werden, handelt es sich nicht um Objekt
im Sinne von Objekt-orientierter Programmierung.]. Man kann `struct`s
als Klassen, die nur Daten Member enthalten betrachten, als über keine
eigenen Methoden verfügen.

## `struct`s deklarieren. 
 
[i[`struct` keyword-->declaring]<]
Ein `struct` wird wie folgt deklariert:

``` {.c}
struct car {
    char *name;
    float price;
    int speed;
};
```

Das geschieht gewöhnlich im globalen _Scope_ ausserhalb von Funktionen,
um sicherzustellen, das die `struct` auch universell einsetzbar ist.

Wenn man das macht, erstellt man einen neuen _Typ_. Dessen vollständiger
Name lautet in diesem Fall: `struct car`. (Nicht nur `car` -- das
funktioniert nicht.)

Bislang gibt es noch keine Variabel mit diesem Typ, aber wir können
welche deklarieren:

``` {.c}
struct car saturn;  // Variable "saturn" of type "struct car"
```

Und schon haben wir ein Variabeln names `saturn`, von Type `struct car`,
die nicht initialisiert ist^[Der Saturn war eine beliebte Marke
günstiger Autos in den Vereinigten Staaten, bis sie im 2008 Börsencrash
in den Ruin getrieben wurden, zum Bedauern von uns Fans.].

Wir sollten sie initialisieren! Aber wie weisen wir den individuellen
Feldern Werte zu?

Wie in vielen anderen Sprachen, die von C geklaut haben, nutzen wir den
Punkt Operator (`.`) um auf einzelne Felde zuzugreifen.

``` {.c}
saturn.name = "Saturn SL/2";
saturn.price = 15999.99;
saturn.speed = 175;

printf("Name:           %s\n", saturn.name);
printf("Price (USD):    %f\n", saturn.price);
printf("Top Speed (km): %d\n", saturn.speed);
```

In den ersten Zeilen setzen wir die Werte von `struct car` und
anschließend geben wir diese Werte aus.
[i[`struct` keyword-->declaring]>]

## Intialisierung von `struct`s (Struct Initializers) {#struct-initializers}

[i[`struct` keyword-->initializers]<]
Das letzte Beispiel war ein bisschen unhandlich. Es muss einen besseren
Weg geben, `struct` Variabeln zu initialisieren!

Das geht in dem man bei der Initialisierung der Variabel die Werte der
Felder _in der Reihenfolge wie sie im `struct` aufgeführt sind_ angibt.
( Das unfunktioniert nicht mehr, wenn die Variabel bereits definiert ist
-- es muss während der Definition stattfinden ).

``` {.c}
struct car {
    char *name;
    float price;
    int speed;
};

// Now with an initializer! Same field order as in the struct declaration:
struct car saturn = {"Saturn SL/2", 16000.99, 175};

printf("Name:      %s\n", saturn.name);
printf("Price:     %f\n", saturn.price);
printf("Top Speed: %d km\n", saturn.speed);
```

Dass die Felder in der Initialisierung einer speziellen Reihenfolge
unterliegen ist ein bisschen ... speziell. Solle jemand die Reihenfolge
in `struct car` ändern, macht es den ganzen anderen Code kaputt.

Daher können wir die Initialisierung etwas expliziter gestalten:

``` {.c}
struct car saturn = {.speed=175, .name="Saturn SL/2"};
```

Die Initialisierung der `struct` ist nun unabhängig von deren
Deklaration. Und das ist ganz sicher sicherer als zuvor.

So wie bei der Initialisierung von Arrays, werden alle fehlenden Felder
durch Null Werte ergänzt (das wäre in diesem Fall `.price`, den ich
ausgelassen habe).
[i[`struct` keyword-->initializers]>]

## `struct`s an Funktionen übergeben

[i[`struct` keyword-->passing and returning]<]

Man kann auf ein paar Arten `structs` an Funktionen übergeben:

1. Die `struct` an sich übergeben.
2. Einen Zeiger auf die `struct` übergeben.

Du magst Dich erinnern, dass wenn irgendwas an eine Funktion übergeben
wird, eine Kopie erstellt wird mit der die Funktion arbeitetk ob es sich
nun um eine Kopie eines Zeigers, eines `int` oder einer `struct`
handelt.

Es gibt im wesentlich zwei Situationen, in den Du einen Zeiger auf eine
`struct` übergeben möchtest:

1. Die Funktion soll Änderung an der `struct` vornehmen können, die der
   Aufrufende Code mitbekommt.
2. Die `struct` is so gross, dass es aufwendiger ist, die Kopie auf dem
   Stack zu erstellen, als den Zeiger zu kopieren^[Ein Zeiger ist auf
   einem 64-Bit System vermutlich 8 Byte lang.].

Aus diesen beiden Gründen ist es viel weiter verbreitet, Zeiger auf
`structs` an Funktionen zu übergeben, wobei es keinesfalls verboten ist,
die `struct` and sich zu übergeben,

<!-- Translator Note: I don't think the stack - heap concept has been
introduced yet. This may be a good place to allude to it. (TBH: I'm not
sure to what extent stack and heap are a "thing" in the standard -->

Versuchen wir mal einen Zeiger zu übergeben, dafür brauchen wir eine
Funktion die es ermöglicht das `.price` Feld des `struct car` zu setzen.

``` {.c .numberLines}
#include <stdio.h>

struct car {
    char *name;
    float price;
    int speed;
};

int main(void)
{
    struct car saturn = {.speed=175, .name="Saturn SL/2"};

    // Pass a pointer to this struct car, along with a new,
    // more realistic, price:
    set_price(&saturn, 799.99);

    printf("Price: %f\n", saturn.price);
}
```

Die Signatur für `set_price()` solltest Du bestimmen können, in dem Du
Typen der Argumente betrachtest.

`saturn` ist ein `struct car`, also muss `&saturn` eine Addresse eines
`struct car` sein, auch bekannt als Zeiger auf ein `struct car`,
schlussendlich nämlich ... ein `struct car*`.

Und `799.99` ist ein `float`.

Die Deklaration der Funktion muss also so aussehen:

``` {.c}
void set_price(struct car *c, float new_price)
```

Jetzt müssen wir die Funktion nur noch implementieren. Erster Versuch:

``` {.c}
void set_price(struct car *c, float new_price) {
    c.price = new_price;  // ERROR!!
}
```

Das geht nicht, weil der Punkt Operator nur mit `struct`s
funktioniert... und nicht mit _Zeigern_ auf `strcut`s.

Also dereferenzieren wir die `struct` um sie zu ent-zeigern und an die
eigentlich `struct` zu gelangen. Ein dereferenziertes `struct car*`
ergibt das `struct car` auf das Zeiger zeigt, und _darauf_ sollten wir
den Dot Operator anwenden können:

``` {.c}
void set_price(struct car *c, float new_price) {
    (*c).price = new_price;  // Works, but is ugly and non-idiomatic :(
}
```

Und das funktioniert! Ist aber ein bisschen umständlich mit dem ganzen
Klammer- und Sternchengerumpel. Um Abhilfe zu schaffen gibt dafür in C
eine Kurzform, den Pfeil Operator.

[i[`struct` keyword-->passing and returning]>]

##  Der Pfeil Operator (Arrow)

[i[`->` arrow operator]<]
Der Pfeil Operator (eng. _Arrow_) hilft dabei, mittels Zeiger auf Felder
in `struct`s zu verweisen.

``` {.c}
void set_price(struct car *c, float new_price) {
    // (*c).price = new_price;  // Works, but non-idiomatic :(
    //
    // The line above is 100% equivalent to the one below:

    c->price = new_price;  // That's the one!
}
```

Was wird also benutzt, um auf Fehler zuzugreifen, Punkt oder Pfeil?

* Hast Du eine `struct`, verwende den Punkt (`.`).
* Verwende den Pfeil (`->`), um auf Zeiger auf `struct`s zuzugreifen.

[i[`->` arrow operator]>]

## `struct`s kopieren und zurückgeben

[i[`struct` keyword-->copying]<]
Kopieren ist zur Abwechselung einfach.

Einfach eins dem anderen zuweisen!

``` {.c}
struct car a, b;

b = a;  // Copy the struct
```

Und verwendet man eine `struct` (nicht einen Zeiger auf eine
`struct`) als Rückgabewert einer Funktion, wird sie ebenso in die
aufnehmende Variabel kopiert.

Es handelt sich dabei nicht um eine "deep copy"^[Eine _deep copy_ würde
auch Zeiger der `struct` folgen und den dahinterliegenden Speicher
kopieren. (An.d.Ü. das wird gelegentlichen als "tiefe Kopie" bezeichnet,
klingt aber Scheisse. Wenn überhaupt würde ich auf "vollständige Kopie"
setzen. Gleich kommt _shallow copy_ die man schon eher mit "flache
Kopie" übersetzen könnte ...). Das Gegenstück ist eine _shallow copy_
welche zwar die ganzen Zeiger kopiert, nicht aber das, auf was gezeigt
wird. C hat keine eingebauten Mechanismen um _deep copies_ zu erstellen.]. Alle Felder werden eins-zu-eins kopiert, auch Zeiger.
[i[`struct` keyword-->copying]>]

## `struct`s vergleichen
[i[`struct` keyword-->comparing]<]
Es gibt nur eine zuverlässige Art, `struct`s zu vergleichen: ein Feld
nach dem Anderen.

Du denkst vielleicht, dass Du 
[fl[`memcmp()`|https://beej.us/guide/bgclr/html/split/stringref.html#man-strcmp]],
benutzen könntest, der kann aber nicht mit eventuell vorhandenen [padding
Bytes](#struct-padding-bytes) (dt. Füller) umgehen.

Man würde meinen, es _könnte_ funktionieren, wenn Du immer gewissenhaft
alle `struct`s mittels
[fl[`memset()`|https://beej.us/guide/bgclr/html/split/stringref.html#man-memset]],
mit Nullen ausspülst, aber selbst dann kann es noch [fl[unerwartete Ausnahmen
geben|https://stackoverflow.com/questions/141720/how-do-you-compare-structs-for-equality-in-c]].
<!-- Translator Note: wouldn't it be better to append a "You might think
that it _might_ work to zero out the struct ... but even then there
could be". The current formulation could me misconstrued as you
advocating this in _some_ circumstances (while I regard it as invariably
wrong) -->
[i[`struct` keyword-->comparing]>] [i[`struct` keyword]>]
