<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Auzählungstypen: `enum`

[i[`enum` enumerated types]<]

C bietet uns eine weitere Möglichkeit, konstante Integer-Werte zu
verwenden: `enum`.

Zum Beispiel:

``` {.c}
enum {
  ONE=1,
  TWO=2
};

printf("%d %d", ONE, TWO);  // 1 2
```

In vielerlei Hinsicht kann das besser - oder anders - sein als
`#define`s zu verwenden.

Die haupt Unterschiede:
 
* `enum` kann nur ein Integer-Typ sein.
* `#define` kann alles definieren.
* Enums können mit ihren symbolischen Namen in Debuggern angezeigt
  werden.
* "#define"-Werte werden nur als rohe Zahlen angezeigt, deren Bedeutung
  beim Debuggen schwieriger zu erkennen ist.
 
Da es sich um Integer-Typen handelt, können Enums überall verwendet
werden, wo Integers eingesetzt werden, z.B. in `case` Anweisungen
und sogar als Array-Dimensionen. 

Schauen wir uns da nochmal genauer an.

## Behavior of `enum`
## `enum` Verhalten

### Nummerierung

[i[`enum` enumerated types-->numbering order]<]

`enum`s werden automatisch durchnummeriert, sofern man nichts
Abweichendes angibt.


Sie beginnen bei "0" und von dort aus erhöhen sie sich standardmäßig.

``` {.c}
enum {
    SHEEP,  // Value is 0
    WHEAT,  // Value is 1
    WOOD,   // Value is 2
    BRICK,  // Value is 3
    ORE     // Value is 4
};

printf("%d %d\n", SHEEP, BRICK);  // 0 3
```

Wie wir bereits gesehen habe, kann man spezifische Werte erzwingen:

``` {.c}
enum {
  X=2,
  Y=18,
  Z=-2
};
```

Duplikate sind kein Problem:


``` {.c}
enum {
  X=2,
  Y=2,
  Z=2
};
```

werden Werte ausgelassen, wird die Nummerierung hinter dem zuletzt
vorgegebenen Wert in positiver Reihenfolge weitergeführt. Zum Beispiel:

``` {.c}
enum {
  A,    // 0, default starting value
  B,    // 1
  C=4,  // 4, manually set
  D,    // 5
  E,    // 6
  F=3   // 3, manually set
  G,    // 4
  H     // 5
}
```

[i[`enum` enumerated types-->numbering order]>]

### Nachgestellte Kommas

Vollkommen akzeptabel, wenn das Deinen Vorlieben entspricht:

``` {.c}
enum {
  X=2,
  Y=18,
  Z=-2,   // <-- Trailing comma
};
```

In letzter Zeit wird das in anderen Sprachen beliebter, vielleicht
freust Du Dich, es hier wiederzusehen.

### Scope

[i[`enum` enumerated types-->scope]<]

Der Scope von `enum`s verhält sich wie erwartet. Bei Datei Scope sind
sie überall sichbar im Block Scope ist die Gültigkeit auf den Block
beschränkt.

Typischerweise werden `enum`s aber in Header Dateien definiert so, dass
sie mit Datei Scope `#include`d werden können.

[i[`enum` enumerated types-->scope]>]

### Stilistisches

Wie Dir sicherlich aufgefallen ist, deklariert man `enum` Symbol häufig
in Großbuchstaben mit Unterstrichen.

Das ist nicht zwingend erforderlich, aber ziemlich weit verbreitet.

## Deine `enum` ist ein Typ

Eine wichtige Erkenntnis ist, dass `enum`s ein neuer Typ sind, genau
wie es sich bei `struct`s um eigene Typen handelt.

Man kann sie benennen, damit man später auf den Typ zugreifen kann und
Variabeln mit dem neuen Typ zu deklarieren.

Jetzt fragt man sich natürlich warum man `enum`s verwenden soll, wenn
es sich eigentlich nur um `int`s handelt?

In C ist die Hauptmotivation tatsächlich Übersichtlichkeit -- es ist
eine gute Art die Motivation der Entwicklerin klarer auszudrücken. Im
Gegensatz zu C++ wird der Wertenereich von `enum`s nämlich gar nicht
überprüft.

Im folgenden Beispiel deklarieren wir eine Variabel `r` vom Typ `enum
resource` die solche Werte annehmen kann:

``` {.c}
// Named enum, type is "enum resource"

enum resource {
    SHEEP,
    WHEAT,
    WOOD,
    BRICK,
    ORE
};

// Declare a variable "r" of type "enum resource"

enum resource r = BRICK;

if (r == BRICK) {
    printf("I'll trade you a brick for two sheep.\n");
}
```

`enum`s können selbstverständlich auch ge`typedef`t werden, aber ich
persönlich mag das nicht.^[Anmerkung des Übersetzers: ich schon.]


``` {.c}
typedef enum {
    SHEEP,
    WHEAT,
    WOOD,
    BRICK,
    ORE
} RESOURCE;

RESOURCE r = BRICK;
```

Eine weiterere seltene aber erlaubte Abkürzung besteht darin, Variabeln
gleichzeitig mit dazugehörigen `enum`s zu deklarieren.

``` {.c}
// Declare an enum and some initialized variables of that type:

enum {
    SHEEP,
    WHEAT,
    WOOD,
    BRICK,
    ORE
} r = BRICK, s = WOOD;
```

Man kann `enum`s benennen um sie später wiederzuverwenden. Das ist in
der allermeisten Fällen auch sinnvoll:


``` {.c}
// Declare an enum and some initialized variables of that type:

enum resource {   // <-- type is now "enum resource"
    SHEEP,
    WHEAT,
    WOOD,
    BRICK,
    ORE
} r = BRICK, s = WOOD;
```

Kurz und Gut, `enum`s sind eine gut Art schönen, gescopeten,
typisierten, saubern Code zu schreiben.

[i[`enum` enumerated types]>]
