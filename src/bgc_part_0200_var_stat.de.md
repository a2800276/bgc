<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Variabeln und Anweisungen (Statements)

> _"Es braucht alle Arten, um eine Welt zu schaffen, nicht wahr, Padre?"_ \
> "So ist es, mein Sohn, so ist es."_
>
> ---Piratenkapitän Thomas Bartholomew Red an den Padre, Pirates
 
Ein C-Programm besteht ja schon aus jeder Menge Krempel.
 
Jupp.
 
Und aus verschiedenen Gründen wird es einfacher sein, wenn wir wenn wir
einige der Arten von Dingen, die in einem Programm vorkommen können,
klassifizieren. So, dass wir eindeutig wissen, worüber wir reden.


## Variabeln

[i[Variables]<]Man sagt, "Variablen enthalten Werte". Oder anders
betrachtet handelt bei einer Variabel um einen von Menschen lesbaren
Namen, der auf auf Daten im Speicher verweist.

Lass uns kurz einen Blick auf die Abgründe von Zeigern (Pointer) werden.
Keine Sorge. 

Stell Dir Speicher als grosses Byte-Array vor ^[Beim "Byte handelt es
sich typischerwiese um ein 8-bit lange binäre Zahl. Stell Dir eine
ganze Zahl vor, die nur die Wert von 0 bis 255 (einschliesslich)
annehmenen kann. Aus technischer Sicht erlaubt C Bytes mit einer
beliebigen Anzahl Bits und wenn Du vollkommen unzweideutig von einer 8 Bit
lange Zahl sprechen möchtest, solltest Du den Begriff _Oktettt_
verwenden. Aber Programmierer werden immer annehmen, dass 8 Bits gemeint
sind, wenn von "Byte" die Rede es, zumindest sofern nicht anderes
erwähnt wurde.] Daten werden in diesem konzeptuellen "Array" abgelegt
^[Das ist eine sehr starke Vereinfachung von der eigentlichen
Funktionsweise von modernem Speicher. Als Modell funktioniert es aber
korrekt, also entschuldige, bitte.] Sofern ein Zahl einen größeren Wert
annimmt als in einen einzelnden Byte passen würde, wird er auf mehrere
Bytes aufgeteilt. Da sich Speicher wie ein Array verhält, kann über sein
Index auf jedes Byte zugegriffen werden. Dieser Index in den Speicher
wird auch als _Adresse_, _Zeiger oder _Pointer bezeichnet_.

Wenn wir von einer Variabel in C sprechen, liegt der Wert der Variable
_irgendwo_ im Speicher, an irgendeiner _Adresse_. Selbstredend, denn wo
sonst sollte der Wert verweilen? Da es umständlich ist, alle benötigten
Wert mit ihrer Speicheradresse anzusprechen, weisen wir ihnen Namen zu,
und dieser Name ist dann die Variabel.

Aus zwei Gründe erwähne ich das an dieser Stelle:

1. Es erleichtert es uns, später Pointer Variabeln zu verstehen -- dabei
   handelt es sich um Variabeln, deren Wert die Adresse einer anderen
   Variabel ist.

2. Ausserdem wird es das Verständnis von Zeiger später vereinfachen.

<!-- Translation Note: I find myelf using Zeiger and Pointer
interchangeable. This feels like the right approach, because they are
used interchangeably when talking about them in German. We should make a
not of this somewhere though to not confuse readers. -->

Kurz gesagt handelt es sich bei einer Variabel um einen Namen für Daten
die an irgendeiner Stelle im Speicher angelegt sind.

### Variabeln Namen

[i[Variables]<]Namen von Variablen dürfen alle Zeichen von 0-9, A-Z, a-z
sowie den Unterstrich enthalten solange folgende Regeln eingehalten
werden:

* Der Name darf nicht mit einer Zahl beginnen
* Der Name darf nicht mit zwei Unterstrichen beginnen
* Der Name darg nicht mit einem Unterstrich, gefolgt von einem
  Grossbuchstaben beginnen.

Was Unicode angegeht, musst Du rumexperimentieren. Die Spezifikation
enthält in §D.2 Regeln über welche Codepoint Bereiche an welcher stellen
in Bezeichnern erlaubt sind. Aber die Regeln sind zu umfangreich, um sie
hier zu besprechen. Und in der Praxis selten angewandt.
[i[Variables]>]

### Typen von Variabeln

[i[Types]] Je nachdem mit welchen Sprachen Du Erfahrungen gemacht hast,
bis Du mit dem Konzept von Typen vertraut. C ist allerdings ein bisschen
eigen, was Typen angeht, daher hier kurz zur Wiederholung:

Ein paar Beispieltypen, einiger der Grundlegendsten:

[i[`int` type]][i[`float` type]][i[`char` type]][i[`char *` type]]

|Typ|Beispiel|Name des C Typ|
|:---|------:|:-----|
|Integer|`3490`|`int`|
|Fliesskomma|`3.14159`|`float`^[Hier mogel ich ein bisschen. Genaugenommen handelt es sich bei `3.14159` um den Type `double`, aber soweit sind wir noch nicht, I möchte, dass Du `float` mit Fliesskomma assoziierst and C wird diesen Typ sowieso problemlos in `float` umwandeln. Kurz, wir kümmern uns später drum.]|
|Buchstabe/Zeichen|`'c'`|`char`|
|Zeichenkette/String|`"Hello, world!"`|`char *`^[Liest sich als "Zeider auf einen char" oder "char Pointer". "Char" steht für _character_. Es gibt verschiedene Möglichkeiten `char` auszusprechen, Anekdotische sagen die meisten Leute "tscharr", einige Sprechen es wie das Auto also "car" aus. Und eine kleine Minderheit sagt "care". Über Zeiger später mehr.]|

C gibt sich Mühe wenn gefordert, automatisch zwischen den meisten numerischen Type zu
konvertieren. Davon abgesehen müssen alle konvertierungen "händisch"
vorgenommen werden, insbesonders zwischen Zeichenketten und numerischen
Typen.

Fast alle Typen in C sind Varianten dieser Basistypen.

Bevor eine Variabel benutzt werden kann, muss sie deklariert werden, um
mitzuteilen, welchen Typ Daten darin gespeichert wird. Einmal deklariert
kann sich der Type einer Variabel später nicht mehr ändern. So wie man
sie deklariert bleibt die Variabel bis sie am Ende ihres
Geltungsbereichs (_Scope_) wieder ins Universum aufgesogen wird.

Nehmen wir unser "Hello, world" Programm und fügen ihm ein paar
Variabeln zu:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i;    // Holds signed integers, e.g. -3, -2, 0, 1, 10
    float f;  // Holds signed floating point numbers, e.g. -3.1416

    printf("Hello, World!\n");  // Ah, blessed familiarity
}
```
Zack! Wir haben ein Paar  Variablen deklariert. Wir haben sie noch nicht benutzt,
und sie sind beide nicht initialisiert. Die eine enthält eine Integer-Zahl, und die
andere eine Fließkommazahl (mathematisch gesehen im Grunde eine reelle Zahl.)


[i[Variables-->uninitialized]]Uninitialisierte Variablen haben einen unbestimmten
Wert^[Umgangssprachlich sagt man, sie haben "zufällige" Werte, aber sie sind es nicht wirklich - nicht mal pseudo-Zufallsz ahlen]. Sie müssen
initialisiert werden, sonst muss man davon ausgehen, dass sie Unsinn enthalten.

> Dies ist eine der Stellen, an denen C Dich "erwischen" kann. In meiner
> Erfahrung ist der unbestimmte Wert häufig 0... aber das kann von Lauf
> zu Lauf variieren! Gehe nie davon aus, dass der Wert Null ist, auch
> wenn du siehst, dass er es ist. Initialisiere Variablen _immer_
> explizit auf einen Wert, bevor du sie verwendest^[Wenn wir uns mit
> statische Speicherdauer beschäftigen, werden wir feststellen, dass das
> nicht ganz stimmt da gewisse Variablen automatisch auf Null
> initialisiert werden. Aber um sicher zu gehen, sollte man alle
> Variablen initialisieren.].


What's this? You want to store some numbers in those variables? Insanity!
Was'n'das? Du möchtest den Variabeln irgendwelche Zahlenwert Zuweisen?
Bekloppt.

Na dann legen wir mal los::
[i[`=` assignment operator]]

``` {.c .numberLines}
int main(void)
{
    int i;

    i = 2; // Assign the value 2 into the variable i

    printf("Hello, World!\n");
}
```

Amock. Wir haben einen Wert abgespeichert. Lass uns den Wert ausdrucken.

[i[`printf()` function]<]Wir tun das, indem wir _zwei_ erstaunliche
Argumente an die Funktion `printf()` übergeben. Beim ersten Argument
handelt es sich um eine Zeichenkette die beschreibt, wie die Ausgabe
formatiert  werden soll  (genannt  _format string_), und das zweite ist
der zu druckende Wert, nämlich der Wert der in der Variablen `i` steht. 

printf()` durchssucht den _format String_ nach einer Reihe von
speziellen Sequenzen, die jeweils mit einem Prozentzeichen (`%`)
eingeleitet werden. Diese Anweisungen teilen der Routine mit, was
gedruckt wird. Wenn zum Beispiel ein `%d` gefunden wird, wird der
nächsten übergebene Parameter als Ganzzahl ausgegeben. Wird ein `%f`
gefunden, als Float. Ein `%s`, führt dazu, dass der Parameter als
Zeichenkette ausgegeben wird.


Daher drucken wir unsere Variabeln wir folgte aus:

``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int i = 2;
    float f = 3.14;
    char *s = "Hello, world!";  // char * ("char pointer") is the string type

    printf("%s  i = %d and f = %f!\n", s, i, f);
}
```

Was die folgende Ausgaben bewirkt:

``` {.default}
Hello, world!  i = 2 and f = 3.14!
```

So kann es sein, dass `printf()` gewisse Ähnlichkeiten zu
Formatierungsfunktionen, die Du aus anderen Sprachen kennst aufweist.
[i[`printf()` function]>]

### Boolsche Type

[i[Boolean types]<]C verfügt über Boolesche Typen: wahr oder falsch?

`1`!

Früher gab es in C keine Boolesche Type und manche mögen argumentieren,
dass da noch immer so ist.

In C wird `0` als falsch/false gedeutet und alles andere ist wahr/true.

Deshalb ist `1` true. Und `-37` auch. Aber `0` ist false.

<!-- Translator Note: Sprachregelung Keywords vs. allgemeinsprachliche
verwendung. Keine Ahnung was sinnvoll wäre -->
 
Du kannst Boolesche Typen einfach als `int` deklarieren:

``` {.c}
int x = 1;

if (x) {
    printf("x is true!\n");
}
```

Sofern Du mit `#include <stdbool.h>`[i[`stdbool.h` header file]] den entsprechende Header einfügst, erhälst Du Zugriff auf symbolische Name, die alles vertauter erscheinen lassen.
Ein Typ names `bool`[i[`bool` type]] und die Werte `true`[i[`true` value]] und
`false`[i[`false` value]]:

``` {.c .numberLines}
#include <stdio.h>
#include <stdbool.h>

int main(void) {
    bool x = true;

    if (x) {
        printf("x is true!\n");
    }
}
```

Diese Werte sind identisch mit den entsprechenden Integer Pedants für
true und false. Es handelt sich nur um eine aufgehübschte Fassade.
[i[Boolean types]>]

## Operatoren and Ausdrücke (Expressions) {#operators}

C Operatoren entsprechen im wesentlichen denen aus anderen Sprachen.
Lass und eben durchnudeln.

(Es gibt bei der Verwendung jede Menge Feinheiten, wir betrachten die
Operatoren erstmal oberflächlich um weitermachen zu können.)



### Arithmetik

[i[Arithmetic Operators]()] Sollen hoffentlich bekannt sein.
[i[`+` addition operator]] [i[`-` subtraction operator]]
[i[`*` multiplication operator]] [i[`/` division operator]]
[i[`%` modulus operator]]

``` {.c}
i = i + 3;  // Addition (+) and assignment (=) operators, add 3 to i
i = i - 8;  // Subtraction, subtract 8 from i
i = i * 9;  // Multiplication
i = i / 2;  // Division
i = i % 5;  // Modulo (division remainder)
```

Es gibt eine abgekürzte Form für all diese Beispiele. Jede Zeile hätte
auch kürzer geschrieben werden:
[i[`+=` assignment operator]] [i[`-=` assignment operator]]
[i[`*=` assignment operator]] [i[`/=` assignment operator]]
[i[`%=` assignment operator]]

``` {.c}
i += 3;  // Same as "i = i + 3", add 3 to i
i -= 8;  // Same as "i = i - 8"
i *= 9;  // Same as "i = i * 9"
i /= 2;  // Same as "i = i / 2"
i %= 5;  // Same as "i = i % 5"
```

Es gibt keine Potenzierung. Man muss eine der
`pow()`[i[pow()]T] Funktionsvarianten aus `math.h` verwenden.

Kommen wir nun zu den seltsameren Dingen, die aus anderen Sprachen
vielleicht nicht bekannt sind! [i[Arithmetic Operators]>]

### Ternary Operator

[i[`?:` ternary operator]<]C kennt den _ternary operator_. Dabei handelt
es sich um einen Ausdruck der eine Bedingung erhält und dessen eigener
Wert von dieser Bedingung abhängt.

``` {.c}
// If x > 10, add 17 to y. Otherwise add 37 to y.

y += x > 10? 17: 37;
```

What a mess! You'll get used to it the more you read it. To help out a
bit, I'll rewrite the above expression using `if` statements:

Was ein Schlamassel! Man gewöhnt sich früher oder später dran. Um dem
Verständnis auf die Sprünge zu helfen, hier dasselbe als `if`-Anweisung.

``` {.c}
// This expression:

y += x > 10? 17: 37;

// is equivalent to this non-expression:

if (x > 10)
    y += 17;
else
    y += 37;
```

Vergleiche die Varianten, bist Du die jeweiligen Komponeten des _ternary
operator_ erkennst.

<!-- Translator Note: es hilf der Sache natürlich nicht weiter, dass der
Begriff "ternary operator" nicht wirklich ein englischer Begriff ist.
Altmodische Kollegen versuchen dies it "Ternärer Operator" zu
übersetzen, was ich allerdings als noch unüblicher empfinde -->

Also noch ein weiteres Beispiel welches "even" oder "odd" ausgibt,
jenachdem ob der Wert der Variabel `x` gerade oder ungerade ist.

``` {.c}
printf("The number %d is %s.\n", x, x % 2 == 0? "even": "odd")
```

<!-- Translator Note: this has not been mentioned yet but may need to be
considered: a statement is an "Anweisung" and an expression is an
"Ausdruck". The difference being that one has a value, and the other
doesn't, should probably be explained to beginners -->

Der `%s` _format specifier_ (dt. Umwandlungsanweisung) im `printf()`[i[printf()]T] Aufruf weist die Routine, an den Wert als Zeichenkette zu drucken. 
string. Sofern der Ausdruck `x % 2` als `0` ausgewertet wird, ist der
Wert der gesammten _ternary expression_ die Zeichenkette `"even"`. Sonst
wird der Ausdruck als `"odd"` ausgewertet. Ziemlich Klasse!

Es ist wichtig zu erwähnen, dass es sich beim _ternary Operator_ nicht
um Flusskontrolle handelt wie einer `if`-Anweisung. Es handelt sich
lediglich um einen Ausdruck, der zu einem Wert ausgewertet wird. 
[i[`?:` ternary operator]>]

### Prä- und Post. Inkrement und Dekrement

[i[`++` increment operator]<] [i[`--` decrement operator]<]
<!-- Translator Note: Noch ein unerträgliches sprachliches Tohubabowu-->

Jetzt schauen wir eine weitere Sache an, die Du vielleicht noch nicht kennst.

Das sind die legendären Post-Increment- und Post-Dekrement-Operatoren:

``` {.c}
i++;        // Add one to i (post-increment)
i--;        // Subtract one from i (post-decrement)
```

Sehr üblich verwendet, handelt es sich um Kurzhand für:

``` {.c}
i += 1;        // Add one to i
i -= 1;        // Subtract one from i
```

allerdings mit subtilen Unterschieden.

Betrachte ihre Varianten, Präinkrement and Prädekrement:

``` {.c}
++i;        // Add one to i (pre-increment)
--i;        // Subtract one from i (pre-decrement)
```

Mit Präinkrement und Prädekrement wird der Wert der Variablen
inkrementiert oder dekrementiert, _bevor_ der gesamte Ausdruck
ausgewertet wird. Anschliessend wird der Ausdruck mit dem neuen Wert
ausgewertet.

Bei Postinkrement und Postdekrement wird der Wert des gesamten Ausdrucks
zunächst mit dem aktuellen Wert berechnet, und _dann_ erst wird der Wert
der Variabel verändert.

Man kann sie tatsächlich wie folgt in Ausdrücke einbetten.
You can actually embed them in expressions, like this:

``` {.c}
i = 10;
j = 5 + i++;  // Compute 5 + i, _then_ increment i

printf("%d, %d\n", i, j);  // Prints 11, 15
```

Zum Vergleich der Präinkrement Operator:

``` {.c}
i = 10;
j = 5 + ++i;  // Increment i, _then_ compute 5 + i

printf("%d, %d\n", i, j);  // Prints 11, 16
```

Diese Technik wird häufig bei Array- und Zeigerzugriff und -manipulation verwendet.
Daraus ergibt sich die Manipulation.
gibt Ihnen die Möglichkeit, den Wert in einer Variablen sowohl zu verwenden als auch
den Wert vor oder nach seiner Verwendung zu inkrementieren oder dekrementieren.

Aber mit Abstand am häufigsten wird das Konstrukt in `for` Schleifen
verwendet:

``` {.c}
for (i = 0; i < 10; i++)
    printf("i is %d\n", i);
```

Mehr dazu später.
[i[`++` increment operator]>] [i[`--` decrement operator]>]

### Der Komma Operator

[i[`,` comma operator]<]
Es handelt sich hierbei um eine selten verwendete Art, Ausdrücke zu trennen, und wird
von links nach rechts lausgewertet:

``` {.c}
x = 10, y = 20;  // First assign 10 to x, then 20 to y
```

Wirkt ein wenig albern, da man das Komma einfach durch ein
Semikolon ersetzen könnte, oder?

``` {.c}
x = 10; y = 20;  // First assign 10 to x, then 20 to y
```

Es gibt aber einen kleinen Unterschied. Letzteres sind zwei getrennte
Ausdrücke, während es sich beim ersten Beispiel um einen einzigen
Ausdruck handelt!

Benutzt man den Komma-Operator entspricht der Wert des gesamten
Komma-Ausdrucks dem Wert des Ausdrucks ganz rechts:

``` {.c}
x = (1, 2, 3);

printf("x is %d\n", x);  // Prints 3, because 3 is rightmost in the comma list
```

Aber selbst das wirkt ziemlich konstruiert. Komma-Operators findet man
häufig in in "for"-Schleifen, um in jedem Abschnitt der Anweisung
mehrere Dinge Anweisung auszuführen:

``` {.c}
for (i = 0, j = 10; i < 100; i++, j++)
    printf("%d, %d\n", i, j);
```

Wir kommen später nochmal drauf zurück.
[i[`,` comma operator]>]

### Vergleichende Operatoren 

[i[Conditional Operators]<]
Für Boolesche Werte gibt es jede Menge Operatoren: 
[i[Comparison operators]] [i[`==` equality operator]]
[i[`!=` inequality operator]] [i[`<` less than operator]]
[i[`>` greater than operator]] [i[`<=` less or equal operator]]
[i[`>=` less or equal operator]]

``` {.c}
a == b;  // True if a is equivalent to b
a != b;  // True if a is not equivalent to b
a < b;   // True if a is less than b
a > b;   // True if a is greater than b
a <= b;  // True if a is less than or equal to b
a >= b;  // True if a is greater than or equal to b
```

Bei Zuweisung `=` und Vergleich `==`  besteht Verwechselungsgefahr! Zwei
Gleichheitszeichen vergleichen, ein einzelndes ist eine Zuweisung.

Vergleichende Operatoren können z.B. in `if` Ausdrücken verwendet
werden:

``` {.c}
if (a <= 10)
    printf("Success!\n");
```
[i[Conditional Operators]>]

### Boolesche Operatoren

[i[Boolean Operators]<]
Logische Ausdrücke, also solche, die einen Booleschen Wert ergeben,
können mit den Booleschen Operatoren für _and/und_. _oder/or_ and
_nicht/not_ verkettet werden.

[i[`&&` boolean AND]]
[i[`!` boolean NOT]]
[i[`||` boolean OR]]

|Operator|Boolesche Bedeutung|
|:------:|:-------------:|
|`&&`|und|
|`||`|oder|
|`!`|nicht, Umkehr, Inversion|

Ein Beispiel für das boolesche "und":

``` {.c}
// Do something if x less than 10 and y greater than 20:

if (x < 10 && y > 20)
    printf("Doing something!\n");
```

Ein Beispiel der boolschen Inversion:

``` {.c}
if (!(x < 12))
    printf("x is not less than 12\n");
```

`!` hat einen höheren Priorität als die anderen booleschen Operatoren,
daher müssen wir in diesem Fall Klammern verwenden.

Das Beispiel kann man natürlich auch wie folgt ausdrücken:

``` {.c}
if (x >= 12)
    printf("x is not less than 12\n");
```

aber ich brauchte ein Beispiel!
[i[Boolean Operators]>]

### Der `sizeof` Operator {#sizeof-operator}

[i[`sizeof` operator]<] Dieser Operator gibt die Größe (in Bytes) an,
die eine bestimmte Variable oder ein bestimmter Datentyp im Speicher
verwendet.

Insbesondere gibt er die Größe (in Bytes) an, die der _Typ eines
bestimmten Ausdrucks_ (wobei es sich auch um eine einzelne Variable
handeln kann) im Speicher belegt.

Die Größe kann auf verschiedenen Systemen variieren, außer bei
`char`[i[`char` type]] und seinen Varianten (die immer 1 Byte sind).

Das mag jetzt noch nicht sehr nützlich erscheinen, aber wir werden hier
und da darauf verweisen, daher lohnt es sich an dieser Stelle
anzusprechen.

Da `sizeof` die Anzahl notwendiger Bytes berechnet, könnte man meinen,
es würde `int` zurückgeben. Oder... da die Größe nicht negativ sein
kann, vielleicht ein `unsigned` (ohne Vorzeichen)?

<!-- Translator Note: unsigned not introduced at this point -->

[i[`size_t` type]<]
Tatsächlich verfügt C über einen besonderen Typ, um Rückgabewerte von
`sizeof` darzustellen. Nämlich `size_t`, gesprochen "_zeihz_ti_"^[Das
`_t` steht für Type.] Wir wissen nur, dass es sich um einen `unsigned`
Type handelt, dessen Größe ausreicht um jeglichen Rückgabewert von
`sizeof` zu halten.

<!-- Translator Note: perhaps an aside that this corresponds to the
addressable memory and is thus dependant on memory arch? -->

`size_t` taucht immer wieder an Stellen auf, wo die Anzahl von Dinge
rumgereicht werden. Betrachte es als Wert der eine Anzahl darstellt. 
[i[`size_t` type]>]

Du kannst `sizeof` von einer Variabel oder einem Wert ermitteln:

``` {.c}
int a = 999;

// %zu is the format specifier for type size_t

printf("%zu\n", sizeof a);      // Prints 4 on my system
printf("%zu\n", sizeof(2 + 7)); // Prints 4 on my system
printf("%zu\n", sizeof 3.14);   // Prints 8 on my system

// If you need to print out negative size_t values, use %zd
```

Merke: Es geht um die Größe des _Typs_ des Ausdrucks in Bytes, nicht die
Größe des Ausdrucks selbst. Deshalb ist die Größe von `2+7` die gleiche
wie die Größe von `a` --  beide sind vom Typ `int`. Wir gehen auf diese
Zahl `4` im nächsten Beispiel näher ein...

...Dort  sehen wir, dass man die `sizeof` eines Typs ermitteln kann
(beachte, dass im Gegensatz zu Ausdrücken, bei Typen Klammern zwingend
erforderlich sind):

``` {.c}
printf("%zu\n", sizeof(int));   // Prints 4 on my system
printf("%zu\n", sizeof(char));  // Prints 1 on all systems
```

Man sollte wissen, dass `sizeof` eine _compile-time_ Operation
ist^[Außer bei Arrays mit variabler Länge---aber die heben wir uns für
ein anderes Mal auf.]. Das Ergebnis des Ausdrucks wird im Rahmen der
Kompilierung ermittelt, nicht zur Laufzeit.

Davon werden wir später Gebrauch machen.

[i[`sizeof` operator]>]


## Ablaufsteuerung (Flow Control)

[i[Flow Control]<]

Boolesche Verknüpfungen sind zwar prima, aber wir sind natürlich
aufgeschmissen, wenn wir sie nicht nutzen könnten,  um den Programmfluss
zu kontrollieren. Werfen wir einen Blick auf eine Reihe von Konstrukten:
`if`, `for`, while" und "do-while".
 
Zunächst ein allgemeiner Hinweis zu Anweisungen und Blöcken von
Anweisungen, von ihrem freundlichen, ortansässigen C-Entwickler:
 
Auf eine `if`- oder `while`-Anweisung folgt entweder eine einzelne
Anweisung, oder ein in geschweiften Klammern zusammengefasster Block aus
Anweisungen, die in Reihenfolge ausgeführt werden.

[i[`if` statement]<]
Fangen wir mit der einzelnen Anweisung an:

``` {.c}
if (x == 10) printf("x is 10\n");
```

Das wird auch manchmal auf zwei Zeilen aufgeteilt. (Leerzeichen sind, im
Gegensatz zu Python, in C weistesgehend irrelevant)

``` {.c}
if (x == 10)
    printf("x is 10\n");
```

Aber was ist, wenn mehrere Schritte in Abhängigkeit einer Kondition
ausgeführt werden sollen? Einfach, mehrere Anweisung können mit
geschweiften Klammern zu einem _Block_ oder _compound statment_
(einer zusammengesetzten Anweisung) zusammengeführt werden. 

``` {.c}
if (x == 10) {
    printf("x is 10\n");
    printf("And also this happens when x is 10\n");
}
```

In der Tat ist es ziemlich üblich geschweifte Klammern zu nutzen,
selbst, wenn Sie genaugenommen nicht notwendig wären:

``` {.c}
if (x == 10) {
    printf("x is 10\n");
}
```

Einige Entwickler meinen, der Code sei einfacher zu lesen und vermeide
Fehler wie den folgenden, bei der die Einrückung vortäuscht, dass beide
Ausdrücke teil des `if` Blocks wären:

``` {.c}
// BAD ERROR EXAMPLE

if (x == 10)
    printf("This happens if x is 10\n");
    printf("This happens ALWAYS\n");  // Surprise!! Unconditional!
```

`while` und `for` und weitere Schleifenkonstrukte verhalten sich
ähnlich. Wenn mehrere Anweisungen ausgeführt werden sollen, müssen sie
in geschweifte Klammern gepackt werden. 

Anders ausgedrückt, führt `if` genau die eine Anweisung Anweisung die
ihr folgt aus, und dabei kann es sich um eine Einzelanweisung oder einen
Block handeln.

[i[`if` statement]>]


### Die `if`-`else` Anweisung {#ifstat}


[i[`if`-`else` statement]<]
We've already been using `if` for multiple examples, since it's likely
you've seen it in a language before, but here's another:

Wir haben `if` bereits in mehreren Beispielen verwendet, da das
Konstrukt vermutlich aus anderen Sprachen bekannt ist. Hier ist noch ein
Beispiel:

``` {.c}
int i = 10;

if (i > 10) {
    printf("Yes, i is greater than 10.\n");
    printf("And this will also print if i is greater than 10.\n");
}

if (i <= 10) printf("i is less than or equal to 10.\n");
```

<!-- Translator Note: this is weirdly repetitive of the previous section
and the either/or seems confusing, especially the context of if/else.
It's also confusing because the execution doesn't "continue on the next
line" otherwise, but after the block.-->

In dem Beispiel werden die Meldungen gedruckt, wenn `i` größer als 10
ist. Sonst läuft das Programm nach dem Block weiter. Wie bereits gesagt
kann nach dem `if` entweder eine einzelne Anweisung oder ein Block
stehen. Diese Art Blöck sind bei allen Anweisungen üblich.

Und weil's Spaß macht, kann man natürlich auch etwas tun, wenn die
Bedingung falsch ist, und zwar mit einer `else`-Klausel
nach dem `if`: 

``` {.c}
int i = 99;

if (i == 10)
    printf("i is 10!\n");
else {
    printf("i is decidedly not 10.\n");
    printf("Which irritates me a little, frankly.\n");
}
```

Und Du kannst `else` sogar kaskadieren, um mehrere Bedingungen zu
testen:

``` {.c}
int i = 99;

if (i == 10)
    printf("i is 10!\n");

else if (i == 20)
    printf("i is 20!\n");

else if (i == 99) {
    printf("i is 99! My favorite\n");
    printf("I can't tell you how happy I am.\n");
    printf("Really.\n");
}
    
else
    printf("i is some crazy number I've never heard of.\n");
```

Wenn das vorkommt, sollte man jedoch zuerst bei der
[`switch`](#switch-statement) Anweisung nach einer potentiell bessere
Lösung suchen. Der Haken ist, dass `switch` nur auf Gleichheit prüfen
kann und das auch nur mit konstanten Werten. Die obige
`if`-`else`-Kaskade kann auch Ungleichheit oder Werte-Bereiche prüfen,
mit Variablen vergleichen oder jeglich Kondition ermitteln die gewünscht
ist. [i[`if`-`else`-Anweisung]>]

[i[`if`-`else` statement]>]

### Die `while` Anweisung {#whilestat}
[i[`while` statement]<]

`while` ist das nullachtfünfzehn Schleifenkonstrukt in C. Wiederhole
irgendwas, solange eine Kondition wahr ist. 

Probieren wir's!

``` {.c}
// Print the following output:
//
//   i is now 0!
//   i is now 1!
//   [ more of the same between 2 and 7 ]
//   i is now 8!
//   i is now 9!

i = 0;

while (i < 10) {
    printf("i is now %d!\n", i);
    i++;
}

printf("All done!\n");
```

Damit hätte man eine einfache Schleife. C verfügt auch über eine
`for` Schleife, die in diesem Beispiel etwas sauberer gewesen wäre.

Eine nicht ganz unübliche Verwendnug von `while` Schleife ist die endlos
Schleife, die die Bedingung "wahr" hat:

``` {.c}
while (1) {
    printf("1 is always true, so this repeats forever.\n");
}
```
[i[`while` statement]>]


### Die `do-while` Anweisung {#dowhilestat}

[i[`do`-`while` statement]<]

Da wir nun die `while`-Anweisung im Griff  haben, wagen wir einen Blick
auf ihre enge Verwandte, die `do-while`.

Sie sind im Grunde gleich, es sei denn, die Schleifenbedingung ist beim ersten
Durchlauf falsch. Dann wird `do-while` einmal ausgeführt, aber
`while`  überhaupt nicht. Anders ausgedrückt, der Test, ob der Block
ausgeführt werden soll oder nicht, findet bei `do-while` Schleifen nach
der Ausführung des Blocks statt.  Bei  `while` Schleifen davor.

Schauen wir uns ein Beispiel an:


``` {.c}
// Using a while statement:

i = 10;

// this is not executed because i is not less than 10:
while(i < 10) {
    printf("while: i is %d\n", i);
    i++;
}

// Using a do-while statement:

i = 10;

// this is executed once, because the loop condition is not checked until
// after the body of the loop runs:

do {
    printf("do-while: i is %d\n", i);
    i++;
} while (i < 10);

printf("All done!\n");
```

In beiden Fällen ist die Schleifenbedingung von Anfang an falsch ist.
Daher wird der `while` Codeblock nie ausgeführt. Bei der
`do-while`-Schleife hingegen wird die Bedingung _nach_ der Ausführung
des Codeblocks geprüft, so dass dieser immer mindestens einmal
ausgeführt wird: Die Meldung wird ausgedruckt, `i` inkrementiert, und
erst dann ist die Bedingung nicht erfüllt, und das Programm fährt mit
der Ausgabe "Alles erledigt!" fort.
 
Kurz und Gut: Wenn gewünscht ist, dass die Schleife mindestens einmal
ausgeführt wird und zwar unabhängig von der Schleifenbedingung, verwende
`do-while`.
 
Alle diese Beispiele wären wohl eher für eine "for"-Schleife geeignet.
Lassen Sie uns etwas weniger Deterministisches ausprobieren -
wiederholen, bis eine bestimmte Zufallszahl auftaucht!


``` {.c .numberLines}
#include <stdio.h>   // For printf
#include <stdlib.h>  // For rand

int main(void)
{
    int r;

    do {
        r = rand() % 100; // Get a random number between 0 and 99
        printf("%d\n", r);
    } while (r != 37);    // Repeat until 37 comes up
}
```

Nebenbemerkung: hast Du versucht, das Programm mehrmals laufen zu
lassen? Ist Dir aufgefallen, dass sich die Zufallszahlen wiederholt
haben? Immer wieder? Das liegt daran, dass es sich bei `rand()` um eine
Pseudozufallszahlen Generator handelt der einen _Seed_ verwendet um
entweder dieselbe oder unterschiedliche Sequenzen zu generieren.
Schau Dir mal: 
[fl[`srand()`|https://beej.us/guide/bgclr/html/split/stdlib.html#man-srand]]
für mehr Infos an.
[i[`do`-`while` statement]>]

### The `for` statement {#forstat}
### Die `for` Anweisung {#forstat}

[i[`for` statement]<]

Willkommen bei der beliebtesten Schleife der Welt! Der `for` Schleife!

Das ist die beste Schleifen, wenn Du schon im vorhinein weisst, wie
häufig Du die Schleife durchlaufen willst.

Dasselbe Verhalten kann man mit eine `while` Schleife erreichen, aber
die `for` Schleife hilft, den Code sauber zu halten.

Hier sind zwei vergleichbare Code Schnipsel -- man sieht, dass die `for`
Schleife das Verhalten kompakter ausdrückt.

``` {.c}
// Print numbers between 0 and 9, inclusive...

// Using a while statement:

i = 0;
while (i < 10) {
    printf("i is %d\n", i);
    i++;
}

// Do the exact same thing with a for-loop:

for (i = 0; i < 10; i++) {
    printf("i is %d\n", i);
}
```

Nochmal -- beide Abschnitt bewirken genaue dasselbe Verhalten. Aber man
sieht, dass die `for` Schleife etwas kompakter und augenschonender ist.
(JavaScript-Benutzer werden an dieser Stelle seine C-Ursprünge zu
schätzen wissen.)


`for` ist in drei, durch Semikolons getrennte Teile aufgeteilt. Der
erste Teil ist die Initialisierung, der zweite ist die
Schleifenbedingung und der dritte ist das, was am Ende des Blocks
geschehen soll, sofern die Schleifenbedingung wahr ist. Alle drei dieser
Teile sind optional.


``` {.c}
for (initialize things; loop if this is true; do this after each loop)
```

Beachte, dass die Schleife überhaupt nicht ausgeführt wird, wenn die
Schleifenbedingung Bedingung zu Anfang falsch ist.

> **`for`-loop fun fact!**
>
> Man kann den Komma Operator verwenden, um in jeden Abschnitt der `for`
> Schleife mehrere Dinge zu tun!
>
> ``` {.c}
> for (i = 0, j = 999; i < 10; i++, j--) {
>     printf("%d, %d\n", i, j);
> }
> ```

Ein leeres `for` läuft ewig: 

``` {.c}
for(;;) {  // "forever"
    printf("I will print this again and again and again\n" );
    printf("for all eternity until the heat-death of the universe.\n");

    printf("Or until you hit CTRL-C.\n");
}
```
[i[`for` statement]>]

### Die `switch` Anweisung {#switch-statement}


[i[`switch` statement]<] 

Je nachdem, mit welchen Programmiersprachen Du vertraut bist, kann es
sein, dass`switch` schon bekannt ist.  Es kann auch sei, dass die C
Variante restriktiver als gewohnt ist. Es handelt sich bei `switch` um
eine Anweisung, die es erlaubt, unterschiedliche Aktionen in
Abhängigkeit vom Wert eines ganzzahligen Ausdrucks auszuführen.

Im Grunde wertet es einen Ausdruck zu einem ganzzahligen Wert aus,
springt zu dem entsprechenden [i[`case`-Anweisung]<]`case`. Die
Ausführung wird an diesem Punkt fortgesetzt. Sobald eine
`break`[i[`break`-Anweisung]<] Anweisung gefunden wird, springt die
Ausführung aus dem `Switch` heraus.

Lass uns ein Beispiel schreiben. Die Benutzerin gibt eine Anzahl Ziegen
ein und wir nennen unser Bauchgefühl, um wieviele Ziegen es sich
handelt.


``` {.c .numberLines}
#include <stdio.h>

int main(void)
{
    int goat_count;

    printf("Enter a goat count: ");
    scanf("%d", &goat_count);       // Read an integer from the keyboard

    switch (goat_count) {
        case 0:
            printf("You have no goats.\n");
            break;

        case 1:
            printf("You have a singular goat.\n");
            break;

        case 2:
            printf("You have a brace of goats.\n");
            break;

        default:
            printf("You have a bona fide plethora of goats!\n");
            break;
    }
}
```


`Gib der Benutzer z.B. `2` ein, springt der `switch` zu `casel 2` und
der Programmablauf wird an dieser stelle fortgesetzt.  Trifft der Ablauf
auf ein `Break`, springt er aus dem `Switch` heraus. [i[Anweisung
`break`]>]

Vielleicht ist Dir auch das das `default`[i[`default` label]]-Label  am
am Ende aufgefallen. Dort springt der Programmablauf hin, wenn sich
keine übereinstimmendes `case`findet.

Jeder `case`, einschließlich `default`, ist optional. Und die Fälle
können in beliebiger Reihenfolge aufgeführt sein, wobei es sehr unüblich
wäre, wenn `default`nicht am Ende stünde.[i[`case`-Anweisung]>]

Das Ganze verhält sich also wie eine `if`-`else`-Kaskade:

``` {.c}
if (goat_count == 0)
    printf("You have no goats.\n");
else if (goat_count == 1)
    printf("You have a singular goat.\n");
else if (goat_count == 2)
    printf("You have a brace of goats.\n");
else
    printf("You have a bona fide plethora of goats!\n");
```

Mit ein paar kleinen Unterschieden:

* `switch` springt häufig schneller zu dem korrekten Fall (wobei das nicht von der Spezifikation vorgesehen ist)

<!-- Translator Note: this might be dangerous to tell people who are
still learning C. In general, code produced by a modern compiler should
be equivalent and this would tempt people towards premature optimization
-->

* `if`-`else` kann Bedingungen `<`, `>=` verwenden, und Fliesskomma Zahlen und andere nicht ganzzahlige Typen vergleich, was `switch` nicht kann.

Eine spannende Sache noch, die man gelegentlich sieht: das _fall
through_.

[i[`break` statement]<]
Erinnerst Du Dich, dass `break` verwendet wird, um aus dem
`switch` zu springen?

[i[Fall through]<]
Nun gut, aber was passiert wenn wir _kein_ `break` verwenden?
Es stellt sich herraus, dass wir einfach mit dem nächsten `case` Fall
weitermachen! So nämlich:

``` {.c}
switch (x) {
    case 1:
        printf("1\n");
        // Fall through!
    case 2:
        printf("2\n");
        break;
    case 3:
        printf("3\n");
        break;
}
```

Wenn `x == 1`, springt `switch` zuerst zu `case 1`, druckt die `1`,
aber macht dann einfach weiter... und druckt `2`!

Immerhin treffen wir dann auf ein `break'und springen aus dem `switch`.

Wann `x == 2`, springen wir einfach nach `case 2`, drucken `2`, und
`break`en wie üblich.

Kein `break` zu verwenden wird _fall through_ Bezeichnet.

ProTip: Wenn _fall through_ verwendet wird, sollte, wie im Beispiel
immer ein Kommentar gesetzt werden, dann anderen Programmierern klar
ist, dass es sich im intentiertes Verhalten handelt. 

[i[Fall through]>]

Vergessene `break` Anweisungen sind in der Tat eine der häufigsten
vermeidbaren Fehlerquellen in C. In der Regel ist das `break`
für die Programmlogik erforderlich, wird aber von der Sprache nicht
verlangt.^[Das Verhalten wurde von den Designern der Go Sprach als so
problematisch erachtet, dass in Go das Verhalten umgekehrt ist: `break`
ist das, implizite, default Verhalten, wenn es nicht gewünscht ist muss
eine `fallthrough` Anweisung verwendet werden.]

<!-- Translator Note: added that break is usually necessary for the
program's logic, but not necessary for the program's logic -->

[i[`break` statement]>]

Vorhin wurde erwähnt, dass `switch` mit Integer-Typen funktioniert -
halte Dich dran. Eine Art Ausnahme sind `char` Typen, die uns an dieser
Stelle vielleicht noch ein bisschen wir Zeichenkette vorkommen, aber
insgeheim Ganzzahlen sind. Folgendes ist daher vollkommen akyeptabel:


``` {.c}
char c = 'b';

switch (c) {
    case 'a':
        printf("It's 'a'!\n");
        break;

    case 'b':
        printf("It's 'b'!\n");
        break;

    case 'c':
        printf("It's 'c'!\n");
        break;
}
```

Zu guter Letzt gibt es noch  [i[Schlüsselwort `enum`]]`enum`s die auch
in `switch` verwendet werden dürfen, da sie auch Integer-Typen sind.
Aber mehr dazu im Kapitel "enum".

[i[`switch` statement]>] [i[`break` statement]>] ni[Flow Control]>]P
