<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Zeichen und Zeichen Ketten II (Characters and Strings)

Dass `char` Typen eigentlich kleine Ganzzahlen sind wurde bereits
besprochen ... aber das ist Zeichen in einfachen Anführungszeichen egal.

Aber eine Zeichenkett in doppelten Anführungszeichen hat den Typ `const
char *`.

Wie sich herausstellt gibt es noch ein paar Zeichen- und
Zeichenkettentypen und das ganze führt uns zu eine der dunkelsten
Abgründe der Sprache: die Angelegenheit mit
multibyte/`wide`/Unicode/localization ...

Wir werden einen kurzen Blick in den Abgrund werfen ... aber _noch nicht
hineinspringen.

## Escape Sequenzen

[i[Escape sequences]<]

Zeichenketten aus Buchstaben, Zahlen und Satzzeichen kennen wir:

``` {.c}
char *s = "Hello!";
char t = 'c';
```

Aber was machen wir, wenn wir ein Sonderzeichen (z.B. "€") benötigen,
dass wir nicht auf der Tastatur finden oder, noch banaler, ein
Anführungszeichen? Folgendes geht schon mal nicht:

``` {.c}
char t = ''';
```

[i[`\` backslash escape]<]

Zu diesem Zwecken wurden _Escape Sequenzen_ erfunden. Dabei handelt es
sich um einen Backslash^[Ein rückwärts orientierter Schrägstrich, der
wiederrum selber auf deutschen Tastaturen schwer zu finden ist] (`\`)
gefolgt von einem weiteren Zeichen. Diese Zeichenfolge aus zwei (oder
mehr) Zeichen hat eine besondere Bedeutung. 

Unser einfaches Anführungszeichen können wir darstellen, in dem wir es
_escapen_, d.h. ein Backslash (`\`) voranstellen:

[i[`\'` single quote]<]

``` {.c}
char t = '\'';
```

Und schon weiss C, dass `\'` heisst wir wollen ein Anführungszeichen
darstellen, nicht das Zeichenliteral beenden. 

<!-- 
Translator Note: shouldn't this read "end the character literal"?
means just a regular quote we want to print, not
the end of the character sequence.
-->


[i[`\'` single quote]>]

Sprachlich kann man sich auf den "Backslash" oder "Escapen" beziehen, C
Entwickler sollten wissen, was gemeint ist. Beachte, dass "Escapen" in
diesem Zusammenhang nichts mit der Esc-Taste oder dem ASCII `ESC` zu tun
hat.

<!-- Translator Note: might be worth an excursion here to explain some
of the line noise in ASCII, after all C-String-escaping and ESC are
conceptually related -->

### Häufig verwendete Escape Sequenzen

Aus meiner Sicht machen die folgenden Escape-Sequenzen 99.2%^[Hab' ich
mir ausgedacht, liegt aber bestimmt nah dran.] aller
Verwendungen aus.

|Sequenz|Bedeutung|
|--|------------|
|[i[`\n` newline]]`\n`|Newline Zeichen--- beid er Ausgabe werden die
folgenden Zahlen auf der nächsten Zeile weitergeführt|
|[i[`\'` single quote]]`\'`|Einfaches Anführungszeichen|
|[i[`\"` double quote]]`\"`|Doppeltes Anführungszeichen|
|[i[`\\` backslash]]`\\`|Backslash---wenn man tatsächlich ein `\` in einen `char` oder Zeichenkette benötigt.|

<!-- Translator Note: would it make sense to add hex ASCII values? -->

Hier ein paar Beispiel von Escapes und wie sie ausgegeben werden.

``` {.c}
printf("Use \\n for newline\n");  // Use \n for newline
printf("Say \"hello\"!\n");       // Say "hello"!
printf("%c\n", '\'');             // '
```

### Seltene Escapes
Es gibt aber noch ein paar Escapes. Die sind man selten.

|Sequenz|Bedeutung|
|--|------------|
|[i[`\a` alert]]`\a`|Alert. Führt dazu, dass das Terminal piep und/oder
blinkt!|
|[i[`\b` backspace]]`\b`|Backspace. Bewegt den Cursor eine Stelle zurück, löscht aber nicht das dort befindliche Zeichen.|
|[i[`\f` formfeed]]`\f`|Formfeed. Geht zu nächsten "Seite", hat aber auf modernen Computer wenig Bedeutung, bei mir verhält es sich wie `\v`.|
|[i[`\r` carriage return]]`\r`|Return. Geht zum Anfang der aktuellen Zeile.|
|[i[`\t` tab]]`\t`|Tab. Geht zum nächsten horizontalen Tabstop. Bei mir heisst das, Spalten werden in 8er Schritten ausgerichtet, aber wer weiss, was woanders passiert. |
|[i[`\v` vertical tab]]`\v`|Vertikaler Tab. Geht zum nächsten vertikalen
Tabstop. Bei mir führt das in dieselbe Spalte auf der nächsten Zeile.|
|[i[`\?` question mark]]`\?`|Fragezeichen. Selten benötigt um _Trigraphs_ zu vermeiden, s.u.|

#### Nicht scrollende Status-Meldungen

[i[`\b` backspace]<]
[i[`\r` carriage return]<]

`\b` oder `\r` kann man verwenden, um Statusmeldungen anzuzeigen, die
an derselben Stelle auf dem Bildschirm bleiben und nicht wegscrollen.
Hier ein klassischer Countdown ab 10. ( Achtung, hier wird `sleep()` aus `<unistd.h>`
verwendet -- wenn Du keine Unix-artiges Betriebssystem verwendest, muss
Du vielleicht nach einer äquivalenten Funktion Ausschau halten.)


``` {.c .numberLines}
#include <stdio.h>
#include <threads.h>

int main(void)
{
    for (int i = 10; i >= 0; i--) {
        printf("\rT minus %d second%s... \b", i, i != 1? "s": "");

        fflush(stdout);  // Force output to update

        // Sleep for 1 second
        thrd_sleep(&(struct timespec){.tv_sec=1}, NULL);
    }

    printf("\rLiftoff!             \n");
}
```

Auf Zeile 7 ist jede Menge los! Erstmal fangen wir mit `\r` an um an den
Zeilenanfang zu gelangen. Dann überschreiben wir was aktuell an der
Stelle steht mit dem aktuellen Countdown. (Und der _ternary Operator_
wird verwendet, um sicherzustellen, das wird in der letzten Sekunde
nicht den Plural vergessen.)

Zuletzt ist noch ein Leerzeichen hinter `...`. Das dient dazu, das
letzte `.` zu überschreiben, wenn `i` von `10` auf `9` springt und wir
eine Spalte schmaler werden. Probier's ohne Leerzeichen aus.

Zum Schluss gehen wir aus ästhetischen Gründen mit `\b` eine Stelle
zurück damit sich der Cursor an der korrekten Stelle befindet.

[i[`\b` backspace]>]

Beachte auch, dass in Zeile 14 ziemlich viele Leerzeichen sind um am
Schluss die Countdown Meldungen zu überschreiben.

Zu guter Letzt müssen wir noch über das komische `fflush(stdout)`
sprechen. Kurz gesagt werden Terminalausgaben i.d.R. _zeilenweise
gepuffert_. D.h. die Ausgabe geschieht erst, wenn ein _Newline_
auftaucht. Da wir aber aber nur `\r` und Newline absichtlich vermeiden,
würden unser Program bis "Liftoff!" gar nichts tun, und dann alles
gleichzeitig ausgeben. `fflush()` modifiziert das verhalten und erwingt
die sofortige Ausgabe.

[i[`\r` carriage return]>]

#### Escape mit Fragezeichen

[i[`\?` question mark]<]

Warum überhaupt? Das hier funktioniert doch einwandfrei:

``` {.c}
printf("Doesn't it?\n");
```

Auch mit _Escape_:

``` {.c}
printf("Doesn't it\?\n");   // Note \?
```

Wozu dann das Ganze??!


[i[Trigraphs]<]

Verleihen wir dem Ganzen mit einem weiteren Fragezeichen und einem
Ausrufezeichen ein bisschen Nachdruck:

``` {.c}
printf("Doesn't it??!\n");
```

Beim kompilieren stoße ich auf folgende Warnung:

``` {.zsh}
foo.c: In function ‘main’:
foo.c:5:23: warning: trigraph ??! converted to | [-Wtrigraphs]
    5 |     printf("Doesn't it??!\n");
      |    
```

Und das Programm erzeugt diese unerwartete Ausgabe:

``` {.default}
Doesn't it|
```

_Tregraphs_ also? Was zum Teufel??!

Eines Tages kehren wir zu dieser verstaubten Mottenkiste zurück, aber
kurz zusammengefasst: der Compiler hält Ausschau nach bestimmten
Dreiersequenzen, die mit `??` anfangen und tauscht sie durch andere
Zeichen aus. Sofern Du Dich also an einem Terminal aus der Steinzeit
wiederfindest das kein Pipe Zeichen hat (`|`), kannst Du stattdessen
`??!` eingeben.

Um diesen Austausch zu vermeiden, kannst Du das zweite Fragezeichen
escapen:

``` {.c}
printf("Doesn't it?\?!\n");
```

Und alles kompiliert wie gewohnt.

Heutzutag verwendet natürlich niemand mehr _Trigraphs_. Aber `??!`
taucht doch Gelegentlich in Zeichenketten auf.

[i[Trigraphs]>]
[i[`\?` question mark]>]

#### Numerische Escapes

Darüberhinaus besteht die Möglichkeit, numerische Konstanten oder andere
Zeichenwerte in Zeichenkette oder `char` Werten anzugeben.

Sofern Du die oktale oder hexdezimale Darstellung eines Bytes kennst,
kann man ihn in eine Zeichenketten oder `char` Wert einfügen.

Die folgende Tabelle führt ein paar Beispiele auf. Aber jede hex oder
oktal-Zahl kann verwendet werden. Falls notwendig füllt man mit
führenden Nullen auf die korrekte Anzahl Stellen auf.

|Sequenz|Beschreibung|
|--|------------|
|[i[`\123` octal value]]`\123`|Byte mit dem oktalen Wert `123`, Genau 3 Ziffern.|
|[i[`\x12` hexadecimal value]]`\x4D`|Byte mit hex Wert `4D`, 2 Ziffern.|
|[i[`\u` Unicode escape]]`\u2620`|Unicode Zeichen mit _Code Point_ vom hex Wert `2620`, 4 Ziffern.|
|[i[`\U` Unicode escape]]`\U0001243F`|Unicode Zeichen mit _Code Point_ hex Wert `1243F`, 8 Ziffern.|

Hier noch ein Beispiel, das die selten verwendete Oktal Schreibweise
verwendet um den Buchstaben `B`, zwischen `A` und `C` darzustellen.
Normalerweise würde man das benutzen um besondere, ggfs. nicht druchbare
Zeichen darzustellen, aber hier als Beispiel:

[i[`\123` octal value]<]

``` {.c}
printf("A\102C\n");  // 102 is `B` in ASCII/UTF-8
```

Beachte, dass hier keine anführende Null benötigt wird. Das _Escape_
erfordert in dieser Form allerding drei Ziffer, also nicht vergessen mit
Nullen aufzufüllen falls nötig.

[i[`\123` octal value]>]

[i[`\x12` hexadecimal value]<]

Weit verbreiteter ist die Verwendung von hexadezimal Werten. Hier ein
Beispiel, dass Du in echt nicht verwenden solltest, das demonstriert,
wie man die UTF-8 Bytes 0xE2, 0x80 und 0xA2 in eine Zeichenkette
einbettet. Die Bytes entsprechen dem Unicode "bullet" (•).

``` {.c}
printf("\xE2\x80\xA2 Bullet 1\n");
printf("\xE2\x80\xA2 Bullet 2\n");
printf("\xE2\x80\xA2 Bullet 3\n");
```

Das erzeugt auf UTF-8 fähigen Konsolen folgende Ausgabe (und überall
anders vermutlich Müll):

``` {.default}
• Bullet 1
• Bullet 2
• Bullet 3
```

[i[`\x12` hexadecimal value]>]


[i[`\u` Unicode escape]<]
[i[`\U` Unicode escape]<]

Das ist aber eine beschissene Art mit Unicode zu hantieren. Du kannst
stattdessen Unicode _Escapes_ `\u` (16-Bit) und `\U` (32-Bit) nutzen, um
auf _Code Points_ zu verweisen. Das Bullet Zeichen ist `2022` (hex) in
Unicode. Mit folgendem bekommst Du also portabelere Ergebnisse:

``` {.c}
printf("\u2022 Bullet 1\n");
printf("\u2022 Bullet 2\n");
printf("\u2022 Bullet 3\n");
```

Achte darauf `\u` auf vier und `\U` auf acht Zeichen mit führenden
Nullen aufzufüllen.

[i[`\u` Unicode escape]>]

For example, that bullet could be done with `\U` and four leading zeros:
Beispielsweise kann das Billet mit `\U` und view anführenden Nullen
dargestellt werden:

``` {.c}
printf("\U00002022 Bullet 1\n");
```

[i[`\U` Unicode escape]>]

Aber wer hat heutzutage schon Zeit, so viel zu tippen?

[i[`\` backslash escape]>]
[i[Escape sequences]>]
