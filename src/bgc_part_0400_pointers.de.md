<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->
# Pointers---Cower In Fear! {#pointers}

> _"How do you get to Carnegie Hall?"_ \
> _"Practice!"_
>
> ---20th-century joke of unknown origin

[i[Pointers]<]

Zeiger (Pointer) sind eines der meist gefürchteten Konzepte in C. Zeiger
sind vermutlich die eine Sache, welche diese Sprache überhaupt erst
herausfordernd macht. Aber warum?

Vermutlich weil sie Stromschläge durch die Tastatur schicken können, die
in der Lage sind die Arme von Programmieren permanent mit der Tastatur
zu verschweissen und sie dadurch zu einer Ewigkeit mit dieser elenden
70er Jahre Sprache zu verfluchen.

Nicht wirklich. Ich versuche nur, Sie mental auf Erfolg einzustimmen.

Je nach Deiner Erfahrung mit anderen Sprachen, kennst Du vielleicht bereits 
das Konzept der _Referenzen_, bei dem eine Variable auf ein Objekt 
irgendeines Typs verweist.

Das ist in etwa dasselbe, nur dass wir in C expliziter sagen müssen, ob wir über die Referenz oder das referenzierte Objekt sprechen.

## Memory and Variables {#ptmem}

Der Arbeitsspeicher eines Computers speichert ja alle möglichen Arten
von Daten.  Er speichert `float`'s, `int`'s, oder was auch immer. Damit
Speicher leicht zu handhaben bleibt, wird jedes Byte im Speicher durch
eine ganze Zahl gekennzeichnet. Diese Zahlen erhöhen sich sequentiell,
wenn man sich im Speicher nach oben bewegt^[Typischerweise. Bestimmt
gibt es Ausnahmen in den dunklen Korridoren der Computergeschichte]. Man
kann sich Speicher als einen Haufen nummerierter Kästchen vorstellen,
wobei jedes Kästchen ein Byte Daten enthält ^[Ein Byte ist ein
Zahlenwert, der aus nicht mehr als 8 binären Ziffern besteht, oder kurz
_Bits_ (binary digits). In altmodischen Dezimalzahlen ausgedrückt, kann
ein Byte Werte zwischen 0 und 255 einnehmen.]. Oder stell Dir Speicher
als großes Array vor, in dem jedes Element ein Byte ist, wenn Dir Arrays
geläufiger sind. Die Zahl, die jedes Kästchen repräsentiert, wird seine
_Adresse_[i[Memory Address]] genannt.
 
Selbstverständlich sind Datentypen nicht nur auf einen Byte Länge
beschränkt. Zum Beispiel besteht ein `int` oft aus vier Bytes, ebenso
wie ein `float`, aber das hängt sehr von der eingesetzten Plattform ab.
Mit dem `sizeof` Operator kann bestimmt werden, wieviele Bytes ein
Datentype verwendet.

``` {.c}
// %zu is the format specifier for type size_t

printf("an int uses %zu bytes of memory\n", sizeof(int));

// That prints "4" for me, but can vary by system.
```

> **Memory Fun Facts**: When you have a data type (like your typical
> `int`) that uses more than a byte of memory, the bytes that make up
> the data are always adjacent to one another in memory. Sometimes
> they're in the order that you expect, and sometimes they're not^[The
> order that bytes come in is referred to as the _endianness_ of the
> number. The usual suspects are _big-endian_ (with the most significant
> byte first) and _little-endian_ (with the most-significant byte last),
> or, uncommonly now, _mixed-endian_ (with the most-significant bytes
> somewhere else).]. While C doesn't guarantee any particular memory
> order (it's platform-dependent), it's still generally possible to
> write code in a way that's platform-independent where you don't have
> to even consider these pesky byte orderings.

> **Speicher Fun Facts**: Wenn ein Datentyp vorliegt (z.B. ein `int`),
> der mehr als ein einziges Byte Speicher belegt, befinden sich die
> Bytes, aus denen das Datum besteht, immer nebeneinander im Speicher.
> Manchmal sind sie in der erwarteten Reihenfolge, und manchmal
> nicht^[Die Reihenfolge der Bytes wird als _endianness_ der Zahl
> bezeichnet. Die üblichen Verdächtigen sind _big-endian_ (wobei das
> höchstwertige Byte an erster Stelle steht) und _little-endian_ (mit
> dem höchstwertigen Byte zuletzt), oder, heutzutage ungewöhnlich,
> _mixed-endian_ (mit den höchstwertigen Bytes irgendwo anders).].
> Während C keine bestimmte Speicherordnung garantiert (die Reihenfolge
> ist plattformabhängig), ist es dennoch möglich, plattformabhängigem
> Code zu schreiben, der lästigen Byte-Reihenfolgen nicht
> berücksichtigen muss.

Also: Trommelwirbel und ahnungsvolle Musik für die Definition von
Zeiger. _Ein Zeiger ist eine Variabel, die eine Adress enhält_. Stell
Dir an dieser Stelle die Filmusik von 2001: Odysee im Weltraum vor.
Ba bum ba bum ba bum BAAAAH!

Zugegeben, das war vielleicht ein bisschen übertrieben. Grundsätzlich
sind Zeiger aber nicht geheimnisvoll. Sie enthalten Adressen in den
Speicher. So wie eine `int` die Zahl `12` abgelegt werden kann, wird in
einer Zeiger Variabel eine Speicheradresse abgelegt.

D.h., dass alle der folgenden Dinge dasselbe bedeuten, eine Zahl die
einen Ort im Speicher darstellt:

* Index in den Speicher (Wenn Du Dir Speicher wie ein grosses Array
  vorstellst.)
* Adresse
* Speicherort

Ich werde diese Begriffe austauschbar verwenden. Und ja, ich habe noch
"Speicherort" der Liste zugefügt, da man nie genug Begriffe für dasselbe
haben kann.

Und eine Zeiger Variabel enthält diese Adresse. Genau wie ein `float`
z.B. den Wert `3.14159` enthalten kann.

Stell Dir vor, Du hättest einen Haufen durchnummerierter gelber
Klebezettel. (Auf dem ersten steht `0` auf dem zweiten `1` usw. Das sind
die Adressen.)

Neben der Zahl, welche die Position angibt, kann man noch eine Zahl
notieren. Die Anzahl Hunde, die Du besitzt, wieviel Monde um Mars
kreisen ...

... Oder _Du könntest die Position eines anderen Klebezettel
draufschreiben!_

<!-- Translator Note: Not sure about this metaphor, I think it may be
confusing that the index is written on the post-it, the index is an
inherint property of the post-in, give by it's position among the other
notes ... -->

Wenn Du die Anzahl Deiner Hunde notiert hast, handelt es sich um eine
stinknormale Variabel. Aber wenn Du die Adresse eines anderen
Klebezettel notiert hast, hast Du _einen Zeiger erschaffen_. Er zeigt
auf einen anderen Zettel.

Eine weitere Analogie wäre die Adresse eines Hauses. Stell Dir ein Haus
mit gewissen Eigenschaften vor: Garten, Dach, Solaranlage, usw. Oder Du
kannst die Adresse notieren. Die Adresse ist nicht dasselbe wie das
Haus. Das eine ist ein ganzes Haus, das andere ein Verweis auf das Haus,
sozusagen ein _Zeiger_. Nicht das eigentliche Haus, aber ein
Beschreibung, wo es sich befindet.

Dasselbe können wir mit Computerdaten machen. Dir liegt ein Variabel
vor, in der Daten abgelegt sind. Die Daten befinden sich an irgendeiner
Adresse im Speicher. Nun kann eine weitere _Zeiger Variabel_ erzeugt
werden, in der diese Adresse abgelegt wird.

Dabei handelt es sich nicht um die Daten, stattdessen sagt uns die
Adresse, wo die Daten gefunden werden können, genau wie bei der Adresse
eines Hauses.

Liegt uns eine solche Adresse vor, sagen wir, dass wir über einen
"Zeiger auf" die Daten verfügen (_a pointer to the data_). Und anhang
des Zeigers können wir auf die hinterlegten Daten zugreifen.

(So wirklich nützlich erscheint das noch nicht, aber es wird
unumgänglich, wenn es um Funktionsaufrufe geht. Noch ein bisschen
Geduld)

Angenommen wir haben ein `int` und hätten gerne eine Zeiger darauf. Wir
suchen also nach einem Mechanismus, um die Adresse des `int` Wertes zu
ermitteln, denn eine Zeigervariabel enthält einfach die _Adresse_ einer
Stelle im Speicher. Welchen Operator nutzen wir, um die _Adresse_ von
einem `int` zu ermitteln?

[i[`&` address-of operator]<]
Nicht wirklich überraschend heisst der Operator, der verwendet wird um
die Adresse eines Speicherorts zu ermitteln `address-of` Operator und
wird als kaufmännisches "Und" geschrieben: "`&`". (eng. _Ampersand_) 

Für die nächsten Beispiel stellen wir nun einen neuen `printf()`
_format specifier_ vor, der Zeiger formatiert. Du kennst bereits `%d` um
Dezimalewert zu drucken? Entsprechend druckt `%p`[i[`printf()` function-->with
pointers]] Zeiger (P wie Pointer). Die so gedruckten Zeiger sehe ein
bisschen wunderlich aus (vermutlich werden sie als Hexadezimal Ziffer
ausgegeben^[d.h. Zahlen zur Basis 16, dargestellt durch die Ziffer
0,1,2..,9, A, B, C, ... , F] statt als Dezimalwerte). Aber es handelt
sich um den Speicherort, an dem unsere Daten abgelegt sind. (Oder bei
multi-Byte Daten den Speicherort, an dem der erste Byte der Daten
abgelegt ist.) Unter fast allen Umständen ist der genau Wert den Zeigers
irrelevant. Auch jetzt, das folgende Beispiel dient nur dazu, den
`address-of` Operator zu demonstrieren.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i = 10;

    printf("The value of i is %d\n", i);
    printf("And its address is %p\n", (void *)&i);

    // %p expects the argument to be a pointer to void
    // so we cast it to make the compiler happy.
}
```

Auf meinem Rechner sieht die Ausgabe wie folgt aus:

``` {.default}
The value of i is 10
And its address is 0x7ffddf7072a4
```
[i[`&` address-of operator]>]

Nur aus Neugier: die Hexadezimalzahl hat in altmodische Basis 10
Notation den Wert: 140,727,326,896,068. Das ist ein Index an die
Speicherstelle, wo sich die Variabel `i` befindet, die Adresse von `i`,
der Speicherort von `i`. Ein Zeiger auf `i`.

Es handelt sich um einen Zeiger weil es Dir mitteilt, wo sich `i` im
Speicher befindet. So, wie eine Adresse auf einem Schmierzettel Dir
mitteilt, wo Du ein bestimmtes Haus finden kannst, gibt diese Zahl
Auskunft darüber, wo der Wert von `i` liegt. Er zeigt auf `i`.

Und nochmal, i.d.R. ist uns der spezifische Wert der Adresse egal. Es
zählt nur, dass es sich um einen Zeiger auf `i` handelt.

## Pointer Types {#pttypes}
## Zeiger Typen (_Pointer Types_) {#pttypes}

[i[Pointer types]<]
Schön und gut, dass wir nun die Adresse eine Variabel ermitteln und
ausdrucken können. Macht sich gut auf dem Lebenlauf. Aber vermutlich
würdest Du mich an dieser Stelle gerne Schütteln und verlangen zu
erfahren, wozu das Ganze dient.

Ausgezeichnete Frage und wir kommen darauf direkt nach einer kurzen
Werbung zurück.

> `ACME ROBOTIC HOUSING UNIT CLEANING SERVICES. YOUR HOMESTEAD WILL BE
> DRAMATICALLY IMPROVED OR YOU WILL BE TERMINATED. MESSAGE ENDS.`

> `1A ROBOTER WOHNEINHEIT REINIGUNGS DIENST. IHRE WOHNEINHEIT WIRD
> DRAMATISCH VERBESSERT ODER SIE WERDEN TERMINIERT. ENDE DER DURCHSAGE.`

Willkommen zurück zu Beej's Handbuch. Als wir uns zuletzt begegnet sind,
sprachen wir darüber wozu Zeiger eigentlich gut sind. Als erstes werden
wir einen Zeiger wegspeichern, um ihn später nutzen zu können. Man kann
am Sternchen (`*`) (eng. asterisk) erkennen, dass es sich um einen Wert
vom Type _Zeiger_ handelt. Das Sternchen befindet sich hinter dem Typ
und vor dem Variablennamen.


``` {.c .numberLines}
int main(void)
{
    int i;  // i's type is "int"
    int *p; // p's type is "pointer to an int", or "int-pointer"
}
```

Uns liegt als eine Variabel vom Typ Zeiger vor, der auf andere `int`s
verweisen kann. D.h. sie enthält de Adresse anderer `int`s. Wir wissen,
dass er auf `int`s zeigt, da ihr type `int *` lautet. (liest sich "int
Zeiger" oder "int Pointer")

Wenn man einem Zeiger einen Wert zuweist, muss der Type der rechten
Seite der Zuweisung dem der Zeiger Variabel entsprechen. Zum Glück ist
das der Fall, wenn man den `address-of` Operator verwendet, die folgende
Zuweisung ist also gold-richtig.

``` {.c}
int i;
int *p;  // p is a pointer, but is uninitialized and points to garbage

p = &i;  // p is assigned the address of i--p now "points to" i
```

Auf der Linken Seite der Zuweisung befindet sich eine Variable vom Type
Zeiger-auf-`int` (`int*`), und der rechte Ausdruck hat ebenfalls den
Typ Zeiger-auf-`int`, da `i` ein `int` ist und wir darauf den
`address-of` Operator angewendet haben. Die Adresse eines Dingens kann in einem Zeiger auf das Dingens abgelegt werden. 

Alles Verstanden? Ich befürchte, dass ergibt an dieser Stelle noch nicht
wirklich viel Sinn, da wir Zeiger noch nicht in Aktion erlebt habe. Wir
gehen aber in Baby-Schritte voran um keinen Abzuhängen. Als nächstes
Stellen wir den anti-address-of Operator vor. Den `address-of` aus der
Unterwelt.
[i[Pointer types]>]

## Dereferencing {#deref}
## Dereferenzierung {#deref}
[i[Dereferencing]<]
Eine Zeiger Variabel verweist, oder _referenziert_ eine andere Variabel
in dem sie darauf zeigt. Die Begrifflichkeit _referenzieren_ hört man
nur selten in Gesprächen über C. Ich erwähne das an dieser Stelle aber,
damit der Name des Operators ein bisschen mehr Sinn ergibt.

<!-- Translator Note: possibly make a note concerning "references" in
CPP? -->

Liegt ein Zeiger auf eine Variable vor ( also grob eine "Referenz" auf
eine Variabel) kann man auf die ursprüngliche Variabel zugreifen, indem
man den Zeiger _Dereferenziert_. (Du kannst es Dir als Entzeigern
vorstellen, aber niemand sagt "Entzeigern")

Um zu unserer Hausnummer Analogie zurückzukehren, entspricht der Vorgang
sich eine Haus Adresse anzuschauen und daraufhin zum Haus zu gehen.

Was genau ist mit "auf die ursprüngliche Variabel zugreifen" gemeint?
Nun, wenn eine Variabel `i` vorliegt sowie ein Zieger auf `i`, nennen
wir ihn `p`, kann man den dereferenzierten Zeiger `p` _genau so
verwenden, als würde es sich um die ursprüngliche Variabel `i` handelt_!


[i[`*` indirection operator]<]
Ich habe Dich beinah soweit, dass Du ein Beispiel verkraften kannst. Das
letzte Puzzelstück ist: was genau ist der Dereferenzierungs-Operator?
Tatsächlich heisst er offiziell _indirection operator_ weil indirekt auf
Werte zugegriffen wird, nämlich über Zeiger. Und geschrieben wird der
Operator wieder mit einem Sternchen `*`. Bitte nicht mit dem Sternchen
aus der Zeiger Typ-Deklaration verwechseln. Es handelt sich zwar um
dasselbe Zeichen, aber das hat je nach Kontext unterschiedliche
Bedeutungen.^[Und die Bedeutungsvielfalt ist noch nicht abgeschlossen,
Sternchen werden auch in `/* Kommentaren*/` und Multiplikation und in
Funktionsprototypen mit Arrays verwendet. Alles dasselbe `*` aber durch
Bedeutung je nach Kontext.]

Hier also das vollständige Beispiel:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i;
    int *p;  // this is NOT a dereference--this is a type "int*"

    p = &i;  // p now points to i, p holds address of i

    i = 10;  // i is now 10
    *p = 20; // the thing p points to (namely i!) is now 20!!

    printf("i is %d\n", i);   // prints "20"
    printf("i is %d\n", *p);  // "20"! dereference-p is the same as i!
}
```

Merke das `p` die Adresse von `i` enthält, was Du anhand der Zuweisung
auf Zeile 8 erkennen kannst. Der _indirection operator_ sagt dem
Computer er soll das _Objekt verwenden, auf das gezeigt wird_ und nicht
den Zeiger selbst. So gesehen ist `*p` so etwas wie ein Alias von `i`.
[i[Dereferencing]>][i[`*` indirection operator]>]

Great, but _why_? Why do any of this?
Super, aber _warum_? Warum tun wir das?

## Passing Pointers as Arguments {#ptpass}
## Zeiger als Argumente übergeben {#ptpass}

[i[Pointers-->as arguments]<]
Jetzt sollte Dir langsam klar werden, dass Du jede Menge über Zeiger
weisst, nur nicht deren Anwendung. Wozu `*p` Schreiben, wann man
genausogut `i` schreiben kann?

Nun gut, mein Freund, die wahre Macht von Zeigern offenbart sich erst,
wenn wir anfangen, sie an Funktionen zu übergeben. Warum ist das so
toll? Vielleich erinnerst Du Dich, dass wir gesagt haben, dass man alle
Möglichen Argumente and Funktionen übergeben kann und diese dann brav in
Parameter kopiert werden. Daraufhin kann man die lokalen Kopien
bearbeiten und einen einzigen Wert zurückgeben.

Was aber, wenn mehr als nur ein Wert von der Funktion zurückgegeben
werden soll? Man kann ja nur ein Dinge zurückgenen, richtig? Was, wenn
ich diese Frage mit einer anderen Frage beantworte? ... Also ... zwei
Frage?

Was passiert, wenn ein Zeiger einer Funktion als Argument übergeben
wird? Wird der Zeiger in den korrespondierenden Parameter kopiert? _Aber
ja!_ Erinnerst Du Dich an mein endloses Geschwafel, dass _IMMER UND
AUSNAHMSLOS_ die Argumente in Parameter kopiert werden und die Funktion
nur Zugriff auf Kopien hat? Das trifft auch in diesem Fall zu: die
Funktion erhält nur eine Kopie des Zeigers.

Aber, und das ist der Trick: der Zeiger verweist ja auf eine Variabel
... und innerhalb der Funktion kann die Kopie des Zeigers dereferenziert
werden um Zugriff auf die ursprüngliche Variabel zu ermöglichen. Die
Funktion kann zwar nicht direkt die Variabel sehen, kann aber den Zeiger
auf die Variabel dereferenzieren.

Um das Hausnummerbeispiel weiter zu strapazieren: das Szenario
entspricht dem Fall, dass wir die Adresse auf einen zweiten Zettel
kopieren. Daraufhin gibt es zwei Zeiger auf Dein Haus, aber beide
verweisen auf dasselbe Haus.

Beispiel! Schauen wir uns nochmal die `increment()` Funktion an, nur
diesmal soll auch tatsächlich der Ausgnagswert inkrementiert werden.

``` {.c .numberLines}
#include <stdio.h>

void increment(int *p)  // note that it accepts a pointer to an int
{
    *p = *p + 1;        // add one to the thing p points to
}

int main(void)
{
    int i = 10;
    int *j = &i;  // note the address-of; turns it into a pointer to i

    printf("i is %d\n", i);        // prints "10"
    printf("i is also %d\n", *j);  // prints "10"

    increment(j);                  // j is an int*--to i

    printf("i is %d\n", i);        // prints "11"!
}
```

Ok, hier gibt es einiges zu betrachten. Z.B., dass die `increment()`
Funktion nun ein `int*` als Argument erwartet. Wir übergeben ein `int*`
in dem wir mit dem `address-of` Operator die Variabeln `i` in eine
`int*` umwandeln.

Die `increment()` Funktion erhält eine Kopie des Zeigers. Sowohl der
Originalzeiger `j` (in `main()`) und die Kopie `p` (der Parameter von
`increment()`) verweisen auf dieselbe Adresse, nämlich die, wo sich der
Wert von `i` im Speicher befindet. (Zwei Zettelchen, beide mit derselben
Hausnummer ...).  Egal welche der beiden Zeiger man dereferenziert, wird
in beiden Fälle der Wert hinter `i` modifiziert! So kann die Funktion
auf Werte in einem anderen Gültigkeitsbereich (Scope) zugreifen. Wuhu! 

Das Beispiel oben wird häufig kompakter geschrieben in dem man den
`address-of` Operator direkt in der Liste der Argument verwendet.

``` {.c}
printf("i is %d\n", i);  // prints "10"
increment(&i);
printf("i is %d\n", i);  // prints "11"!
```

Pointer-afficionados werden sich eine vorangegangene Stelle erinnern,
als wir die Funktion `scanf()` verwendet haben, um Tastatureingaben zu
lesen. Und obwohl das zu diesem Punkt noch nicht klar was, haben wir den
`address-of` Operator verwendet, um einen Zeiger an `scanf()` zu
übergeben. Das war notwendig, weil `scanf()` typischerweise von der
Tastatur liest und das Ergebnis in eine Variabel speichert. Und die
einzige Art wie `scanf()` auf Variabeln im Geltungsbereich der
aufrufenden Funktion zugreifen kann ist mittels Zeiger.

``` {.c}
int i = 0;

scanf("%d", &i);         // pretend you typed "12"
printf("i is %d\n", i);  // prints "i is 12"
```

`scanf()` dereferenziert den Zeiger, den wir übergeben um der
zugrundliegenden Variabel den gelesen Wert zuzuweisen. Und jetzt sollte
klar sein, warum sich da ein kaufmännischen "Und" rumtreibt.
[i[Pointers-->as arguments]>]

## Der `NULL` Zeiger
[i[`NULL` pointer]<]
Jedem Zeiger kann der spezielle Wert `NULL` zugewiesen werden. Das
bedeutet, dass der Zeiger auf nichts zeigt.

``` {.c}
int *p;

p = NULL;
```

Da so ein Zeiger auf keinen Wert zeigt, ist das Ergebnis undefiniert,
sollten wir versuchen einen solchen Zeiger zu dereferenzieren.
Vermutlich stürzt unser Programm ab.

``` {.c}
int *p = NULL;

*p = 12;  // CRASH or SOMETHING PROBABLY BAD. BEST AVOIDED.
```

Obwohl der `NULL`-Pointer von seinem Erfinder als [flw[eine Millarde
Dollar Fehler|Null_pointer#History]] bezeichnet wurde, ist er ein guter 
[flw[Abschlusswert|Sentinel_value]] und weist darauf hin, dass ein
Zeiger noch nicht initialisiert wurde.

(Allerdings verweisen Zeiger wie alle anderen Variabeln auf Müll wenn
man ihnen nicht explizit eine Adresse oder `NULL` zuweist.)
[i[`NULL` pointer]>]

## A Note on Declaring Pointers
## Zeiger Deklarieren.

[i[Pointers-->declarations]<]
Die Syntax die benötigt ist, um Zeiger zu deklarieren kann ein bisschen
komisch sein. Schauen wir uns ein Beispiel an:

``` {.c}
int a;
int b;
```

Das können wir mit dem Komma Operator zu einer Zeile zusammenfügen:

``` {.c}
int a, b;  // Same thing
```

So sind `a` und `b` beide `int`s. Kein Problem.

Aber, wie verhält es sich mit:

``` {.c}
int a;
int *p;
```

Können wir das zu eine Zeile zusammenfügen? Können wir. Aber wo kommt
das `*` hin?

Die Regel besagt, das `*` kommt vor Variabeln vom Typ Zeiger. D.h. das
`*` ist nicht Teil vom `int` sondern gehört zur Variabel `p`.

Damit gewappnet können wir folgendes schreiben:

``` {.c}
int a, *p;  // Same thing
```

Es ist wichtig zu erkennen, dass die folgende Zeile _nicht_ zwei Zeiger
deklariert:

``` {.c}
int *p, q;  // p is a pointer to an int; q is just an int.
```

Das kann besonders heimtückisch sein, wenn eine Programmiererin die
folgende (vollkommen gültige) Code-Zeile verfasst, die funktional
identicsh ist:

``` {.c}
int* p, q;  // p is a pointer to an int; q is just an int.
```

Also, schau mal auf folgende List und bestimme, welche Variabeln Zeiger
sind und welche nicht.

``` {.c}
int *a, b, c, *d, e, *f, g, h, *i;
```

Die Antworten stopfe ich in eine Fussnote^[Die Zeifer Variabeln sind
`a`, `d`, `f`, und `i` weil sie ein Sternchen vor sich haben.]
[i[Pointers-->declarations]>]

## `sizeof` und Zeiger

[i[Pointers-->with `sizeof`]<]
Noch ein bisschen Syntax der verwirrend sein kann und man gelegentlich
sieht.

`sizeof` wirkt sich auf den _Typ_ eines Ausdrucks aus.

``` {.c}
int *p;

// Prints size of an 'int'
printf("%zu\n", sizeof(int));

// p is type 'int *', so prints size of 'int*'
printf("%zu\n", sizeof p);

// *p is type 'int', so prints size of 'int'
printf("%zu\n", sizeof *p);
```

Sowas mag Dir in freier Wildbahn über den Weg laufen. Bedenke einfach,
dass es `sizeof` um den Typ der Anweisung geht, nicht um die Variabeln
in der Anweisung.
[i[Pointers-->with `sizeof`]>][i[Pointers]>]
