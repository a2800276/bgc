<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# `typedef`: Neue Typen erzeugen

[i[`typedef` keyword]<]
Genaugenommen nicht wirklich _neue_ Type, eher bestehenden Typen neue
Namen zuteilen. Kling oberflächlich betrachtet eher sinnlos, kann aber
sinnvoll sein, um unseren Code etwas aufzuräumen.

## `typedef` theoretisch

Grob gesagt nimmt man sich ein bestehenden Typ und erzeugt dafür ein
Alias mittels `typedef`.

So wie hier:

``` {.c}
typedef int antelope;  // Make "antelope" an alias for "int"

antelope x = 10;       // Type "antelope" is the same as type "int"
```

Das geht mit allen bestehenden Typen. Man kann sogar mit dem Komma
Operator mehrere Typen gleichzeitig erzeugen: 

``` {.c}
typedef int antelope, bagel, mushroom;  // These are all "int"
```

Ist das nicht wahnsinnig nützlich? Jetzt kann Du `mushroom` tippen statt
`int`. I spüre förmlich Deine Aufregung.

Ok, Professor Sarkasmus -- wir besprechen gleich ein paar übliche
Einsatzgebiete.

### Gültigkeitsbereich (Scoping)

[i[`typedef` keyword-->scoping rules]<]
`typedef` unterliegt den üblichen Regel bezüglich [Scope](#scope).

Daher ist es üblich, dass sich `typedef`s auf Dateiebene finden, also
globalen Scope haben, so dass alle Funktionen auf die neu geschaffenen
Typen Zugriff haben.

## `typedef` Praxis

Wir haben bereits erkannt, das `int` Umbenennen nicht wirklich
attraktiv ist. Schauen wir wie `typedef` überlicherweise in Erscheinung
tritt.

### `typedef` und `struct`s {#typedef-struct}

[i[`typedef` keyword-->with `struct`s]<]

Manchmal wird eine `struct` auf eine neuen Namne ge`typedef`t um sich
das ständige `struct` tippen zu ersparen.

``` {.c}
struct animal {
    char *name;
    int leg_count, speed;
};

//  original name      new name
//            |         |
//            v         v
//      |-----------| |----|
typedef struct animal animal;

struct animal y;  // This works
animal z;         // This also works because "animal" is an alias
```

Persönlich halte ich da nicht viel von. I bevorzuge die Klarheit, die
entsteht wenn das Wort `struct` einem Typ beigefügt ist; Entwicklerinnen
wissen so genau, was ihnen aufgetischt wird. Es ist aber weit
verbreitet, daher erwähne ich es an dieser Stelle.

Jetzt nochmal das selbe Beispiel, so, wie man es häufig antrifft. Wir
stopfen nun das `struct animal` _in_ den `typedef`:

``` {.c}
//  original name
//            |
//            v
//      |-----------|
typedef struct animal {
    char *name;
    int leg_count, speed;
} animal;                         // <-- new name

struct animal y;  // This works
animal z;         // This also works because "animal" is an alias
```

Genau dasselbe wie im letzen Beispiel, nur etwas kompakter.

[i[`typedef` keyword-->with anonymous `struct`s]<]
Das war aber noch nicht alles! Eine weitere geläufige geläufige
Abkürzung nett sich _anonymous structures_^[Mehr dazu später.] Es stellt
sich herraus, dass die struct gar nicht mehrfach benannt werden muss.
Ein Weg das zu vermeiden sind `typedefs`. 

Wiederholen wir das Beispiel noch einmal mit einer anonymen Struct.

``` {.c}
//  Anonymous struct! It has no name!
//         |
//         v
//      |----|
typedef struct {
    char *name;
    int leg_count, speed;
} animal;                         // <-- new name

//struct animal y;  // ERROR: this no longer works--no such struct!
animal z;           // This works because "animal" is an alias
```

Noch ein Beispiel. Vielleicht finden wir sowas vor:

``` {.c}
typedef struct {
    int x, y;
} point;

point p = {.x=20, .y=40};

printf("%d, %d\n", p.x, p.y);  // 20, 40
```
[i[`typedef` keyword-->with anonymous `struct`s]>]
[i[`typedef` keyword-->with `struct`s]>]
[i[`typedef` keyword-->scoping rules]>]

### `typedef` und andere Typen

Es ist auch keinesfalls so, dass der Einsatz von `typedef` mit
Basistypen wir `int` vollkomen nutzlos ist. Es hilf dabei, Datentypen
etwas abstrakter zu gestalten um sie später einfacher ändern zu können.

Hast Du Beispielsweise überall im Code `float` verwendet, wird es
schmerzhaft, alle auf `double` umzuändern, wenn das später mal notwendig
werden sollte. 

Hättest Du dich aber ein kleines Bisschen vorbereitet:

``` {.c}
typedef float app_float;

// and

app_float f1, f2, f3;
```

dann müsstest Du später nur den `typedef` Anpassen, wenn der Type z.B.
auf `long double` umgestellt werden muss.

``` {.c}
//        voila!
//      |---------|
typedef long double app_float;

// and no need to change this line:

app_float f1, f2, f3;  // Now these are all long doubles
```

### `typedef` und Zeiger

[i[`typedef` keyword-->with pointers]<]
Man kann einen Zeiger Type erzeugen:

``` {.c}
typedef int *intptr;

int a = 10;
intptr x = &a;  // "intptr" is type "int*"
```

Dieses Vorgehen gefällt mir nicht. Es verbirgt, dass es sich bei `x` um
einen Zeiger handelt, da man kein `*` in  Deklarationen sieht.

Meiner Meinung nach sollte man bevorzugt explizit darauf Hinweisen, dass
man einen Zeiger deklariert, damit es anderen Entwicklern sofort
deutlich wird und sie `x` nicht für einen nicht Zeiger verwechseln.

Aber als ich das letzte mal nachgeschaute, waren ca. 832.007 Menschen
anderer Meinung.
[i[`typedef` keyword-->with pointers]>]

### `typedef`, Groß- und Kleinschreibung

Ich habe schon alle denkbaren Arten der Großschreibung von `typedef`
gesehen.

``` {.c}
typedef struct {
    int x, y;
} my_point;          // lower snake case

typedef struct {
    int x, y;
} MyPoint;          // CamelCase

typedef struct {
    int x, y;
} Mypoint;          // Leading uppercase

typedef struct {
    int x, y;
} MY_POINT;          // UPPER SNAKE CASE
```

Die C11-Spezifikation schreibt nichts vor und enthält
Beispiele ausschliesslich in Groß- oder Kleinbuchstaben.


K&R2 verwendet überwiegend führende Großbuchstaben, zeigt aber einige Beispiele in
snake case (mit `_t`)^[_snake case_ bezeichnet
das\_verwenden\_von\_unterstrichen].

Wenn es in Deinem Umfeld ein Style Guide gibt, halte Dich dran. Sonst
such Dir eine Variante aus und halte Dich dran.

## Arrays und `typedef`

[i[`typedef` keyword-->with arrays]<]
Die Syntax ist gewöhnungsbedürftig und wird in meiner Erfahrung selten verwendet, aber man kann `typedefs` von Arrays mit fester Länger erzeugen.

``` {.c}
// Make type five_ints an array of 5 ints
typedef int five_ints[5];

five_ints x = {11, 22, 33, 44, 55};
```

Ich mag diese Methode nicht, weil sie den Arraycharakter der Variablen verbirgt, aber
die Möglichkeit besteht.
[i[`typedef` keyword-->with arrays]>]
[i[`typedef` keyword]>]
