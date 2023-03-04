<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Arrays {#arrays}

> _“Should array indices start at 0 or 1?  My compromise of 0.5 was
> rejected without, I thought, proper consideration.”_
>
> ---Stan Kelly-Bootle, computer scientist

[i[Arrays]<] Zum Glück hat C Arrays.^[Hinweis des Übersetzers: es gibt
des deutschen Begriff "Feld", aber in meiner Erfahrung wird der selten
verwendet und wenn, nur von Sprachpuristen, die sich über die vielen
englischen Begriff im Alltag aufregen.] Ich weiss ja, dass es als
"low-level" Sprache angesehen wird^[Zumindest heutzutag] aber zumindest
sind Arrays eingebaut. Und da C's Syntax viele Nachfolger inspiriert
hat, kennst Du vermutlich schon die eckigen Klammern `[` und `]` um auf
Array's zuzugreifen und sie zu deklarieren.

Allerdings hat C nur _gerade so_ Arrays. Wir beschreiben später noch,
dass Arrays eigentlich nur eine vereinfachte Syntax^[_syntactic sugar_]
für den Umgang mit Zeigern und anderem Krempel tief in den Innereien
ist. _Waaah!_ Aber fangen wir an damit, einfach nur Arrays zu verwenden.
_Puh_.

## Ein einfaches Beispiel

Lass uns einfach mal ein Beispiel hier hinprügeln:

[i[Arrays-->indexing]<]
``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i;
    float f[4];  // Declare an array of 4 floats

    f[0] = 3.14159;  // Indexing starts at 0, of course.
    f[1] = 1.41421;
    f[2] = 1.61803;
    f[3] = 2.71828;

    // Print them all out:

    for (i = 0; i < 4; i++) {
        printf("%f\n", f[i]);
    }
}
```

Bei der Deklaration eines Arrays muss die Größe angegeben werden. Und
die Größe muss feststehen^[mal wieder nicht wirklich, aber _variable
length Arrays_ -- die ich nicht wirklich leiden kann -- werden in einem
eigenen Kapitel besprochen.]

Das Beispiel oben erzeugt ein Array aus 4 `float`s. Der Wert `4` in den
eckigen Klammern deutet darauf hin.

In den nachfolgenden Zeilen benutzen wir das Array, entweder um Werte
zuzuweisen, oder um auf Werte zuzugreifen, jeweils mit eckigen Klammern.
[i[Arrays-->indexing]>]

Ich kann hoffentlich vorraussetzen, dass Dir das bekannt vorkommt.

## Größe eines Arrays bestimmen

[i[Arrays-->getting the length]<] Das geht nicht ... so richtig. C merkt
sich die Information nicht^[Da es sich aus technischer Sicht bei Arrays
lediglich um Zeiger auf das erste Element im Array handelt, gibt es
keine Längenangabe]. Die Länge muss in einer seperaten Variabel
verwaltet werden.

Wenn ich "geht nicht" sage meine ich eigentlich, dass es unter Umständen
_doch_ geht. Es gibt einen Trick mit der es erlaubt innerhalb des Scope
in dem das Array deklariert wurde, die Anzahl enthaltener Elemente
festzustellen. Aber im allgemeinen geht das nicht, da man ein Array ja
auch an Funktionen weitergeben will^[Und wenn ein Array an eine Funktion
übergeben wird, wird der Zeiger auf das erste Array Element in das
entsprechende Argument kopiert, nicht das gesammte Array.].

<!-- Translator note: I elaborated the Footnote to: when an array is
passed to a function a pointer to the first element of the array is
being copied to the corresponding argument ... to drive down that point
-->

Schauen wir uns diesen Trick genauer an. Die Grundidee ist, dass man die
[i[`sizeof` operator-->with arrays]<]`sizeof` Länge des Arrayspeicher durch die
größe eines enthaltenen Elements teilt um die Länge des Arrays gemessen
in Anzahl enthaltener Elemente zu erhalten. Als Beispiel: sagen wir ein
`int` ist 4 Bytes lange und das Array belegt insgesamt 32 Bytes
Speicher, so enthält es Platz für $\frac{32}{4}$ or $8$ `int`s.


``` {.c}
int x[12];  // 12 ints

printf("%zu\n", sizeof x);     // 48 total bytes
printf("%zu\n", sizeof(int));  // 4 bytes per int

printf("%zu\n", sizeof x / sizeof(int));  // 48/4 = 12 ints!
```

Besteht das Array aus `char`s, dann entspricht die Arraylänge dem
`sizeof` Wert da `sizeof(char)` per Definition 1 ist. Bei allen anderen
Typen musst Du aber durch die Bytegröße der enthaltenen Elemente teilen.

Leider funktioniert dieser Trick nur innerhalb des Sichtbarkeitsbereich
(Scope) in dem das Array definiert wurde. Sobald ein Array an eine
Funktion übergeben wird klappt es nicht mehr. Selbst wenn Du versuchst,
die Länge anzugeben.

``` {.c}
void foo(int x[12])
{
    printf("%zu\n", sizeof x);     // 8?! What happened to 48?
    printf("%zu\n", sizeof(int));  // 4 bytes per int

    printf("%zu\n", sizeof x / sizeof(int));  // 8/4 = 2 ints?? WRONG.
}
```

Das liegt daran dass Arrays immer und ausnahmslos als Zeiger auf ihr
erstes Element übergeben werden. Und dass ist es, was `sizeof`
misst. Mehr dazu weiter unten im Abschnitt [Eindimensionale Arrays an Funktionen
übergeben](#passing1darrays).

Eine weiterer Anwendungsfall von `sizeof` mit Arrays ist die größe eines
Arrays mit einer feststehenden Anzahl von Elementen zu ermitteln, ohne
das Array zu deklarieren. Das verhält sich so, als ob man die Größe
eines `int` anhand des Typs `sizeof(int)` ermittelt.

Um beispielweise zu schauen, wieviel Bytes ein Array aus 48 `double`s
einnehmen würde, kann man folgendes tun:

``` {.c}
sizeof(double [48]);
```
[i[`sizeof` operator-->with arrays]>]
[i[Arrays-->getting the length]>]

## Array Initialisierung

[i[Array initializers]<]
Arrays können direkt mit Konstanten initialisiert werden:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i;
    int a[5] = {22, 37, 3490, 18, 95};  // Initialize with these values

    for (i = 0; i < 5; i++) {
        printf("%d\n", a[i]);
    }
}
```

Der Haken: bei den Werten muss es sich um Konstanten handeln! Variabeln
sind an dieser Stelle nicht erlaubt.

Man sollte auf keinen Fall mehr Element in der Initialisierung angeben
als in das Array passen. Den Fehler fängt aber der Compiler und wird
ungemütlich:

``` {.zsh}
foo.c: In function ‘main’:
foo.c:6:39: warning: excess elements in array initializer
    6 |     int a[5] = {22, 37, 3490, 18, 95, 999};
      |                                       ^~~
foo.c:6:39: note: (near initialization for ‘a’)
```

Aber (fun fact) man darf sehr wohl _weniger_ Elemente in der
Initialisierung angeben. Die übrigen Elemente werden dann automatisch
mit Null-Werten gefüllt. So verhalten sich im allgemeinen alle
Initialisierungen: wenn initialisiert wird, werden alle nicht explizit
angegebenen Werte auf Null gesetzt.

``` {.c}
int a[5] = {22, 37, 3490};

// is the same as:

int a[5] = {22, 37, 3490, 0, 0};
```

Ein gewöhnlich Trick ein gesammtes Array mit Nullen zu füllen ist:

``` {.c}
int a[100] = {0};
```

Was soviel heisst wie: "Sehe zu, dass das erste Element Null ist, und
dann den ganzen Rest automatisch auch."

Man kann sogar spezifische Array Elemente während der Initialisierung
setzen, in dem man deren Index angibt! Auch dann wird C alle
nachfolgenden Wert mit `0` auffüllen.

Dazu muss ein Indexwert in eckige Klammern von einem `=` gefolgt werden
und dann dem gewünschtem Wert.

Das sieht so aus:

``` {.c}
int a[10] = {0, 11, 22, [5]=55, 66, 77};
```

Da wir 5 als Index für die `55` angegeben haben, sieht das resultierende
Array wie folgt aus:

``` {.default}
0 11 22 0 0 55 66 77 0 0
```

An der Stelle dürfe sogar einfach konstante Ausdrücke stehen.

``` {.c}
#define COUNT 5

int a[COUNT] = {[COUNT-3]=3, 2, 1};
```

Das ergibt:

``` {.default}
0 0 3 2 1
```

Zu guter letzte kann C auch die Arraygröße anhand der Initialisierung
ermitteln. Dazu lässt man einfach die Größenangabe im Typ weg.

``` {.c}
int a[3] = {22, 37, 3490};

// is the same as:

int a[] = {22, 37, 3490};  // Left the size off!
```
[i[Array initializers]>]

## Out of Bounds! (klare Grenzen)


[i[Arrays-->out of bounds]<]
C verhindert nicht, dass man auf Stellen zugreift, die ausserhalb des
Arrays liegen^[Anm.d.Ü. Im englischen werden solche Fehler _out of
bounds violations_ bezeichnet.]. Im Zweifelsfall gibt es nicht mal eine
Warnung.

Schnappen wir uns das letzte Beispiel und drucken einfach hinter dem
Ende des Arrays weiter. Das Array enthält nur 5 Elemente, aber versuchen
wir einfach 10 zu drucken und schauen was passiert.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i;
    int a[5] = {22, 37, 3490, 18, 95};

    for (i = 0; i < 10; i++) {  // BAD NEWS: printing too many elements!
        printf("%d\n", a[i]);
    }
}
```

Auf meinem Rechner passiert folgendes:

``` {.default}
22
37
3490
18
95
32765
1847052032
1780534144
-56487472
21890
```

Hoppla! Ausdrucken von Array Elementen, die sich hinter dem Ende des
Arrays befinden, erzeugt was C Programmierer _undefined behavior_
(undefiniertes Verhalten) nennen. Dieses Verhalten hat einen besonderen
Stellenwert in C auf das wir später noch zu sprechen kommen. Als
aktuelles Verständnis reicht: "Du warst böse und das kann beliebige
Folgen haben, wenn Dein Programm läuft".

Typischerweise heißt "beliebig": unerwatet nullen zu finden, Müll
vorzufinden oder abstürzen. Aber die C Spezifikation gibt an, das
Compiler in solchen Fällen tatsächlich _beliebigen_ Code ausgeben
dürfen^[Zu DOS-Zeiten bevor neumodischer _memory protection_ eingeführt
worden sind, schrieb ich an etwas besonders abenteuerlichem C, der sich
mit allem möglichen _undefined behavior_ vergnügte. Aber ich weiss ja
was ich tue und alles funktioniert ziemlich gut. Bis mir ein Fehlerchen
unterlaufen ist, der alles eingefror, und,  wie ich nach einem Neustart
feststellte, alle meine BIOS-Einstellungen löschte. (Gruss an @man, das
waren gute Zeiten.)]

Kurz: nicht tun, was zu _undefined behavior_ führt. Nie^[Lauter Krempel
erzeugt _undefined behavior_ nicht nur illegale Array Zugriffe. Das
macht C so _aufregend_].

[i[Arrays-->out of bounds]>]

## Mehrfdimensionale Arrays

[i[Arrays-->multidimensional]<]
Man kann seinen Arrays beliebig vielen Dimensionen hinzufügen.


``` {.c}
int a[10];
int b[2][7];
int c[4][5][6];
```

Die Daten werden im Speicher zeilenweise abgelegt^[An.d.Ü. englisch:  [flw[row-major
order|Row-_and_column-major_order]] ]. D.h. das z.B. im Falle eines
zweidimensionalem Array das erste Index die Reihe, bzw. Zeile beschreibt
und das zweite Index die Spalte.

<!-- Translator Note: May expand on the fact that the elements actually
get layed out this way in memory. e.g. accessing the array with a single
index. This would a) help drive home the "pointer to first element"
point and b) can have major performance/cache implication when
traversing multi-dim arrays--> 

Man kann mehrdimensionale Arrays ganz einfach verschachtelt
initialisieren:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int row, col;

    int a[2][5] = {      // Initialize a 2D array
        {0, 1, 2, 3, 4},
        {5, 6, 7, 8, 9}
    };

    for (row = 0; row < 2; row++) {
        for (col = 0; col < 5; col++) {
            printf("(%d,%d) = %d\n", row, col, a[row][col]);
        }
    }
}
```

Was foglende Ausgaben erzeugt:

``` {.default}
(0,0) = 0
(0,1) = 1
(0,2) = 2
(0,3) = 3
(0,4) = 4
(1,0) = 5
(1,1) = 6
(1,2) = 7
(1,3) = 8
(1,4) = 9
```

Und man kann unter Angabe der Indices initialisieren:

``` {.c}
// Make a 3x3 identity matrix

int a[3][3] = {[0][0]=1, [1][1]=1, [2][2]=1};
```

was so einen zweidimensionalen Index ergibt:

<!-- Translator Note: I'd consider fully spelling out "two dimensional
array", this may just be me personally, but 2D sets my brain into
computer graphics mode-->

``` {.default}
1 0 0
0 1 0
0 0 1
```
[i[Arrays-->multidimensional]>]

## Arrays und Zeiger

[i[Arrays-->as pointers]<]
_So ganz unter uns .._ Ich hab' ein bisschen erwähnt das Arrays tief im
Inneren nur Zeiger sind? An dieser Stelle möchte ich ein bisschen Licht
ins dunkele bringen. Wirklich nur ein bisschen, später schauen wir uns
die _wirklichen_ Zusammenhänge an. Aber hier erkläre ich erstmal, wir
man Arrays an Funktionen übergibt.

### Zeiger an Funktionen übergeben.

Ich verate ein kleines Geheimbis. Im allgemeinen meint eine C
Programmiererin, wenn sie über Zeiger auf Arrays spricht genaugenommen
einen Zeiger auf das _erste Arrayelement_^[... was aus technischer Sicht
falsch ist, da ein Zeiger auf einen Array und ein Zeiger auf's erste
Arrayelement einen anderen Typ haben.].

Schnappen wir uns also einen Zeiger auf das erste Element im Array.

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int a[5] = {11, 22, 33, 44, 55};
    int *p;

    p = &a[0];  // p points to the array
                // Well, to the first element, actually

    printf("%d\n", *p);  // Prints "11"
}
```

Das ist so üblich, dass C uns folgende Abkürzung erlaubt:

``` {.c .numberLines}
p = &a[0];  // p points to the array

// is the same as:

p = a;      // p points to the array, but much nicer-looking!
```

Einfach nur den Array Bezeichner losgelöst zu erwähnen entspricht also
schon einen Zeiger auf das erste Element! Wir nutzen das ausführlich in
kommenden Beispielen.

Moment mal -- `p` ist dohc ein `int*`? Und `*p` ergibt `11`, genau wie
`a[0]`? Genau. Langsam wird der Zusammenhang zwischen Arrays und Zeiger
in C deutlicher.
[i[Arrays-->as pointers]>]

### Passing Single Dimensional Arrays to Functions {#passing1darrays}
### Eindimensionale Arrays an Funktionen übergeben {#passing1darrays}

[i[Arrays-->passing to functions]<]
Ein Beispiel mit eindimensionalen Arrays: Ich schreibe ein paar
Funktionen, an die wir Arrays können, die unterschiedliche Verhalten
verdeutlichen.

Bereite Dich auf ein paar sensationelle Funktionssignaturen vor!

``` {.c .numberLines}
#include <stdio.h>

// Passing as a pointer to the first element
void times2(int *a, int len)
{
    for (int i = 0; i < len; i++)
        printf("%d\n", a[i] * 2);
}

// Same thing, but using array notation
void times3(int a[], int len)
{
    for (int i = 0; i < len; i++)
        printf("%d\n", a[i] * 3);
}

// Same thing, but using array notation with size
void times4(int a[5], int len)
{
    for (int i = 0; i < len; i++)
        printf("%d\n", a[i] * 4);
}

int main(void)
{
    int x[5] = {11, 22, 33, 44, 55};

    times2(x, 5);
    times3(x, 5);
    times4(x, 5);
}
```

Alle drei Arten Arrays zu übergeben sind identisch.

``` {.c}
void times2(int *a, int len)
void times3(int a[], int len)
void times4(int a[5], int len)
```

Die erste Übergabe Art ist bei Eingeweihten mit Abstand am beliebtesten.

Im letzten Beispiel ist es tatsächlich so, dass es dem Compiler egal
ist, welche Zahl beim Array steht (abgesehen, dass sie größer null sein
muss^[C11 §6.7.6.2¶1 erfordert, dass die Zahl größer Null sein muss.
Aber man sieht gelegentlich Code mit Arrays der Länge Null als
abschliessendes `struct` Element. GCC ist da besonders grosszügig wenn
man nicht mit dem `-pedantic` Flag kompiliert. Solch ein
Länge-Null-Array war eine Bastelei, um Datenstrukturen von variabeler
Länge zu erzeugen. Bedauerlicherweise handelt es sich dabei im
_undefined behavior_ obwohl das eigentlich überall funktioniert. Ab C99
gibt es einen wohl-definierten Ersatz names _flexible array member_ die
wird später uns noch vornehmen]

Now that I've said that, the size of the array in the function
declaration actually _does_ matter when you're passing multidimensional
arrays into functions, but let's come back to that.
[i[Arrays-->passing to functions]>]

### Arrays in Funktoinen modifizieren

[i[Arrays-->modifying within functions]<]
Wie gesagt sind Arrays nur verkappte Zeiger. Wenn ein Array an eine
Funktion übergeben wird, heisst das eigentlich das ein Zeiger auf das
erste Arrayelement übergeben wird.

<!-- Translator Note: This means that if
you pass an array to a function, you're likely passing a pointer ...
"likely"? -->

Aber wenn die Funktionen einen Zeiger auf die Daten erhält kann sie die
Daten auch verändern! Änderungen, die eine Funktion an Arrays vornimmt
sind also auch für den Aufrufer sichtbar.

Hier ein Beispiel in dem wir einen Zeiger auf ein Array an eine Funktion
übergeben, welche die Wert im Array manipuliert. Und diese Änderungen
sind für den Aufrufer sichtbar.

``` {.c .numberLines}
#include <stdio.h>

void double_array(int *a, int len)
{
    // Multiply each element by 2
    //
    // This doubles the values in x in main() since x and a both point
    // to the same array in memory!

    for (int i = 0; i < len; i++)
        a[i] *= 2;
}

int main(void)
{
    int x[5] = {1, 2, 3, 4, 5};

    double_array(x, 5);

    for (int i = 0; i < 5; i++)
        printf("%d\n", x[i]);  // 2, 4, 6, 8, 10!
}
```

Obwohl wir den Array als Parameter `a` vom Typ `int*` übergeben haben
können wir mittels Arraynotation auf ihn zugreifen.. Das ist erlaubt.

Wenn wir später über die Äquivalenz von Arrays und Zeigern sprechen wird
das mehr Sinn ergeben. An dieser Stelle reicht es zu wissen, dass
Änderungen, die von Funktionen an Arrays vorgenommen werden, draußen
beim Aufrufer sichtbar sind.
[i[Arrays-->modifying within functions]>]

### Multidimensionale Arrays an Funktionen übergeben

[i[Arrays-->passing to functions]<]
Die Story verhält sich ein bisschen anders, wenn sie sich um
mehrdimensionale Arrays dreht. C muss über die ganzen Dimensionen
Bescheid wissen (bis auf die erste) um ermitteln zu können, wo im
Speicher sich ein Wert befindet.

Hier ein Beispiel, in dem wir explizit alle Dimensionen angeben:

``` {.c .numberLines}
#include <stdio.h>

void print_2D_array(int a[2][3])
{
    for (int row = 0; row < 2; row++) {
        for (int col = 0; col < 3; col++)
            printf("%d ", a[row][col]);
        printf("\n");
    }
}

int main(void)
{
    int x[2][3] = {
        {1, 2, 3},
        {4, 5, 6}
    };

    print_2D_array(x);
}
```

In dem Fall wären die folgenden beiden Beispiele äquivalent^[Auch: `void
print_2D_array(int (*a)[3]` geht, aber das geht an dieser Stelle zu
weit.]:

``` {.c}
void print_2D_array(int a[2][3])
void print_2D_array(int a[][3])
```

Der Compiler muss eigentlich nur die zweite Dimension kennen. Daran kann
er erkennen, wieviel Speicher er für jede Erhöhung der ersten Dimension
überspringen muss um auf ein Element zuzugreifen. Verallgemeinert auf
mehrere Dimensionen kann man sagen, dass alle Dimensionen bis auf die
erste bekannt sein müssen.

<!-- Translator note: 

:The compiler really only needs the second dimension so it can figure out
:how far in memory to skip for each increment of the first dimension. In
:general, it needs to know all the dimensions except the first one.

This is related to my previous note about multidim arrays and layout in
memory, it might be helpful for understanding here to show that you can
access the multidim array as a one.dimensional one but would need the
magnitudes of the dimensions to calculate the index.

This may also be confusing. 

-->

Nicht vergessen: der Compiler beschränkt sich auf minimale Checks was
Array Überschreitungen angeht (und das auch nur, wenn Du 
Glück hast). Zur Laufzeit gibt es gar keinen Schutz davor! Denk immer
dran, dass es keinen Anschnallgurt gibt, vermeide also Unfälle mit
längenüberschreitungen bei Arrays!
[i[Arrays-->passing to functions]>] [i[Arrays]>]
