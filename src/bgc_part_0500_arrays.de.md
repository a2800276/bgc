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

Auf keinen Fall sollte man mehr Element in der Initialisierung angeben
als in den Array passen. Den Fehler fängt aber der Compiler und wird
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

## Out of Bounds!

[i[Arrays-->out of bounds]<]
C doesn't stop you from accessing arrays out of bounds. It might not
even warn you.

Let's steal the example from above and keep printing off the end of the
array. It only has 5 elements, but let's try to print 10 and see what
happens:

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

Running it on my computer prints:

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

Yikes! What's that? Well, turns out printing off the end of an array
results in what C developers call _undefined behavior_. We'll talk more
about this beast later, but for now it means, "You've done something
bad, and anything could happen during your program run."

And by anything, I mean typically things like finding zeroes, finding
garbage numbers, or crashing. But really the C spec says in this
circumstance the compiler is allowed to emit code that does
_anything_^[In the good old MS-DOS days before memory protection was a
thing, I was writing some particularly abusive C code that deliberately
engaged in all kinds of undefined behavior. But I knew what I was doing,
and things were working pretty well. Until I made a misstep that caused
a lockup and, as I found upon reboot, nuked all my BIOS settings. That
was fun. (Shout-out to @man for those fun times.)].

Short version: don't do anything that causes undefined behavior.
Ever^[There are a lot of things that cause undefined behavior, not just
out-of-bounds array accesses. This is what makes the C language so
_exciting_.].
[i[Arrays-->out of bounds]>]

## Multidimensional Arrays

[i[Arrays-->multidimensional]<]
You can add as many dimensions as you want to your arrays.

``` {.c}
int a[10];
int b[2][7];
int c[4][5][6];
```

These are stored in memory in [flw[row-major
order|Row-_and_column-major_order]]. This means with a 2D array, the
first index listed indicates the row, and the second the column.

You can also use initializers on multidimensional arrays by nesting them:

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

For output of:

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

And you can initialize with explicit indexes:

``` {.c}
// Make a 3x3 identity matrix

int a[3][3] = {[0][0]=1, [1][1]=1, [2][2]=1};
```

which builds a 2D array like this:

``` {.default}
1 0 0
0 1 0
0 0 1
```
[i[Arrays-->multidimensional]>]

## Arrays and Pointers

[i[Arrays-->as pointers]<]
[_Casually_] So... I kinda might have mentioned up there that arrays
were pointers, deep down? We should take a shallow dive into that now so
that things aren't completely confusing. Later on, we'll look at what
the real relationship between arrays and pointers is, but for now I just
want to look at passing arrays to functions.

### Getting a Pointer to an Array

I want to tell you a secret. Generally speaking, when a C programmer
talks about a pointer to an array, they're talking about a pointer _to
the first element_ of the array^[This is technically incorrect, as a
pointer to an array and a pointer to the first element of an array have
different types. But we can burn that bridge when we get to it.].

So let's get a pointer to the first element of an array.

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

This is so common to do in C that the language allows us a shorthand:

``` {.c .numberLines}
p = &a[0];  // p points to the array

// is the same as:

p = a;      // p points to the array, but much nicer-looking!
```

Just referring to the array name in isolation is the same as getting a
pointer to the first element of the array! We're going to use this
extensively in the upcoming examples.

But hold on a second---isn't `p` an `int*`? And `*p` gives us `11`, same
as `a[0]`? Yessss. You're starting to get a glimpse of how arrays and
pointers are related in C.
[i[Arrays-->as pointers]>]

### Passing Single Dimensional Arrays to Functions {#passing1darrays}

[i[Arrays-->passing to functions]<]
Let's do an example with a single dimensional array. I'm going to write
a couple functions that we can pass the array to that do different
things.

Prepare for some mind-blowing function signatures!

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

All those methods of listing the array as a parameter in the function
are identical.

``` {.c}
void times2(int *a, int len)
void times3(int a[], int len)
void times4(int a[5], int len)
```

In usage by C regulars, the first is the most common, by far.

And, in fact, in the latter situation, the compiler doesn't even care
what number you pass in (other than it has to be greater than zero^[C11
§6.7.6.2¶1 requires it be greater than zero. But you might see code out
there with arrays declared of zero length at the end of `struct`s and
GCC is particularly lenient about it unless you compile with
`-pedantic`. This zero-length array was a hackish mechanism for making
variable-length structures. Unfortunately, it's technically undefined
behavior to access such an array even though it basically worked
everywhere. C99 codified a well-defined replacement for it called
_flexible array members_, which we'll chat about later.]). It doesn't
enforce anything at all. 

Now that I've said that, the size of the array in the function
declaration actually _does_ matter when you're passing multidimensional
arrays into functions, but let's come back to that.
[i[Arrays-->passing to functions]>]

### Changing Arrays in Functions

[i[Arrays-->modifying within functions]<]
We've said that arrays are just pointers in disguise. This means that if
you pass an array to a function, you're likely passing a pointer to the
first element in the array.

But if the function has a pointer to the data, it is able to manipulate
that data! So changes that a function makes to an array will be visible
back out in the caller.

Here's an example where we pass a pointer to an array to a function,
the function manipulates the values in that array, and those changes are
visible out in the caller.

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

Even though we passed the array in as parameter `a` which is type
`int*`, look at how we access it using array notation with `a[i]`!
Whaaaat. This is totally allowed.

Later when we talk about the equivalence between arrays and pointers,
we'll see how this makes a lot more sense. For now, it's enough to know
that functions can make changes to arrays that are visible out in the
caller.
[i[Arrays-->modifying within functions]>]

### Passing Multidimensional Arrays to Functions

[i[Arrays-->passing to functions]<]
The story changes a little when we're talking about multidimensional
arrays. C needs to know all the dimensions (except the first one) so it
has enough information to know where in memory to look to find a value.

Here's an example where we're explicit with all the dimensions:

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

But in this case, these two^[This is also equivalent: `void
print_2D_array(int (*a)[3])`, but that's more than I want to get into
right now.] are equivalent:

``` {.c}
void print_2D_array(int a[2][3])
void print_2D_array(int a[][3])
```

The compiler really only needs the second dimension so it can figure out
how far in memory to skip for each increment of the first dimension. In
general, it needs to know all the dimensions except the first one.

Also, remember that the compiler does minimal compile-time bounds
checking (if you're lucky), and C does zero runtime checking of bounds.
No seat belts! Don't crash by accessing array elements out of bounds!
[i[Arrays-->passing to functions]>] [i[Arrays]>]
