<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Hallo, Welt!
 
## Was man von C erwarten kann
 
> _"Wohin führt diese Treppe?"_ \
> _"Nach oben."_
>
> ---Ray Stantz und Peter Venkman, Ghostbusters
 
C ist eine Low-Level-Sprache.
 
Das war nicht immer so. Damals, als die Leute noch Lochkarten aus Granit
schnitzten, bot C eine unglaubliche Möglichkeit, sich von der Plackerei
niedrigeren Sprachen wie [flw[assembly|Assembly_language]] zu befreien.
 
Aber heutzutage bieten aktuelle Sprachen Möglichkeiten die es als C 1972
erfunden wurde noch nicht gab. Daraus ergibt sich, dass C eine ziemlich
einfache Sprache mit einem begrenztem Funktionsumfang ist. C kann
_alles_, aber kann von Dir dafür harte Arbeit abverlangen.

Weshalb also heute überhaupt noch C verwenden?
 
* Als Lernwerkzeug: C ist nicht nur ein ehrwürdiges Stück Computer
  Geschichte, sondern ist auch mit der [flw[bare metal|Bare_machine]]
  auf eine Weise verbunden, wie es aktuelle Sprachen nicht mehr sind.
  Wenn Du C lernst, lernst Du wie Software mit dem Computerspeicher auf
  einer niedrigen Ebene interagiert. Es gibt keine Sicherheitsgurte. Du
  wirst Software schreiben, die abstürzt, das versichere ich versichere
  ich Dir. Und das ist Teil des Spaßes!

* Als nützliches Werkzeug: C wird immer noch für bestimmte Anwendungen
  verwendet, z.B. Erstellung von [flw[Betriebssysteme|Betriebssystem]]
  oder in [flw[eingebettete Systemen|Embedded_system]]. (Obwohl die
  Programmiersprache [flw[Rust|Rust_(programming_language)]] diese
  beiden Bereiche ins Visier nimmt!)


Wenn Du mit einer anderen Sprache vertraut bist, sind viele Dinge in C
einfach. C hat viele andere Sprachen inspiriert, und Du wirst Teile
davon in Go, Rust, Swift, Python, JavaScript, Java und allen möglichen
anderen Sprachen wiederfinden. Diese Teile werden Ihnen vertraut sein.

Die eine Sache, an der sich viele Leute die Zähne ausbeißen, sind
_Zeiger_. Praktisch alles andere wirkt vertraut, aber Zeiger sind
merkwürdig. Das Konzept hinter Zeigern ist wahrscheinlich bekannt, aber
C zwingt Dich, sie explizit zu verwenden, noch dazu mit Operatoren, die
Du wahrscheinlich noch nie gesehen hast.

Das ist besonders heimtückisch, weil Zeiger, sobald man sie Klick
gemacht haben, plötzlich ganz einfach sind. Aber bis zu diesem Moment
sind sie glitschige Aale.

Der ganze Rest ist nur Auswendiglernen, wie man Konstrukte in C umsetzt,
die man in anderen Sprachen schon verwendet hat. Nur Zeiger stehen
hervor. Aber selbst Zeiger sind nur eine andere Sichtweise auf ein
Thema, das Dir wahrscheinlich vertraut ist.

Mach Dich also bereit für ein ausschweifendes Abenteuer in der
einflussreichsten Computersprache aller Zeiten^[Ich weiß, irgend jemand
wird widersprechen, einigen wir uns auf zumindest unter den ersten
drei?] das so nah an den Kern des Computers reichen wird, wie man ohne
Assembler kommen kann. Halt dich fest! The one thing about C that hangs
people up is _pointers_. Virtually everything else is familiar, but
pointers are the weird one. The concept behind pointers is likely one
you already know, but C forces you to be explicit about it, using
operators you've likely never seen before.


## Hallo, Welt!
 
[i[Hallo, Welt]()], das kanonische C-Beispiel-Programm.
Jeder benutzt es. ( Beachte, dass die Zahlen auf der linken Seite nur der Lesbarkeit dienen
und nicht Teil des Quellcodes sind.)

``` {.c .numberLines}
/* Hello world program */

#include <stdio.h>

int main(void)
{
    printf("Hello, World!\n");  // Actually do the work here
}
```

We're going to don our long-sleeved heavy-duty rubber gloves, grab a
scalpel, and rip into this thing to see what makes it tick. So, scrub
up, because here we go. Cutting very gently...

[i[Comments]<]Let's get the easy thing out of the way: anything between
the digraphs `/*` and `*/` is a comment and will be completely ignored
by the compiler. Same goes for anything on a line after a `//`. This
allows you to leave messages to yourself and others, so that when you
come back and read your code in the distant future, you'll know what the
heck it was you were trying to do. Believe me, you will forget; it
happens.[i[Comments]>]

Wir werden nun unsere langärmeligen Gummihandschuhe überstreifen, ein
Skalpell schnappen und sehen uns an, wie das Ding tickt. Also,
aufgepasst, es geht los. Wir schneiden ganz vorsichtig...

[i[Comments]<] Fangen wir mit dem Einfachen an: alles zwischen  `/*` und
`*/` ist ein Kommentar und wird vom Compiler vollständig ignoriert.
Dasselbe gilt für alles, was auf einer Zeile hinter `//` steht. Das
erlaubt Dir Nachrichten an Dich selbst und andere zu hinterlassen. Wenn
Du in ferner Zukunft Deinen Code liest, bekommst Du vielleicht eine
Ahnung davon, was Du damals eigentlich tun wolltest. Glaube mir, du
wirst es vergessen; es geht allen so.[i[Comments]>]

[i[C Preprocessor]<][i[`#include` directive]<] Was ist dieses
`#include`? WÜRG! Nun, es teilt dem C-Präprozessor mit, dass er den
Inhalt einer anderen Datei and genau _dieser_ Stelle im Code einfügen
soll.

Moment - was ist ein C-Präprozessor? Gute Frage. Kompilieren ist ein
zweistufiger Prozess^[Genau genommen sind es mehr als zwei, aber hey,
tun wir mal so als wären es zwei---Ignoranz ist Glückseligkeit, oder?] :
bestehend aus Präprozessor und Compiler. [i[Octothorpe]<]Alles, was mit
Pfundzeichen oder "Octothorpe" (`#`) beginnt, ist etwas, das der
Präprozessor[i[Preprocessor]] bearbeitet, bevor der Compiler überhaupt
gestartet wird. Übliche _Präprozessor-Direktiven_, wie sie genannt
werden, sind `#include` und `#define`.[i[`#define` directive]] Mehr dazu
später.

Bevor wir weitermachen sollte darauf hingewiesen werden, dass das
"Lattenzaun" Symbol im englishen "Pound Sign" oder "Octothorpe" genannt
wird und der Autor die Bezeichnung "Octothorpe" wahnsinnig witzig
findet. Pfft! 
[i[Octothorpe]>]

Nachdem der C-Präprozessor also die Vorverarbeitung abgeschlossen hat,
sind die Ergebnisse bereit für den Compiler (auf Deutscher klassiche:
"Übersetzer"), der sie nimmt und in
[flw[Assemblercode|Assembly_language]], [flw[Maschinen
Code|Maschinencode]], oder was auch immer übersetzt. Maschinencode ist
die "Sprache", die die CPU versteht, und zwar  _sehr schnell_ versteht.
Das ist einer der Gründe, warum C-Programme in der Regel schnell sind.

So _anyway_. After the C preprocessor has finished preprocessing
everything, the results are ready for the compiler to take them and
produce [flw[assembly code|Assembly_language]], [flw[machine
code|Machine_code]], or whatever it's about to do. Machine code is the
"language" the CPU understands, and it can understand it _very rapidly_.
Das ist einer der Gründe, warum C-Programme tendenziell schnell
ausgeführt werden können.

Mach Dir erstmal keine Gedanken über die technischen Details der
Kompilierung; Du musst nur wissen wissen, dass der Quelltext den
Präprozessor durchläuft, dann dessen Ausgabe den Compiler durchläuft,
der schlussendlich die ausführbare Datei erzeugt, die Du laufen lässt.


Was ist mit dem Rest der Zeile? [i[`stdio.h` header file]<]Was ist
`<stdio.h>`? Es handelt sich um eine _header Datei_. Die Punkt-h Endung
verrät es.  Genaugenommen handelt es sich um die "Standard I/O"
(`stdio`) header Datei die Du noch lieben lernen wirst. Die Datei stellt
eine Menge Ein- und Ausgabe (Input Output, I/O) Funktionalität zur
Verfügung. ^[Technisch gesehen enthält es Präprozessoranweisungen und
Funktionsprototypen (mehr dazu später) für allgemeine Eingabe- und
Ausgabe    Erfordernisse]. In unserem Beispielprogramm geben wir die
Zeichenkette "Hello World" aus und benötigen dafür Zugang zur
[i[`printf()` function]<] `printf()` Funktion. Die `<stdio.h>` Datei
erlaubt uns diesen Zugriff. Wenn wir versucht hätten, `printf()` ohne
`#include <stdio.h>` zu verwenden, hätte sich der Compiler darüber
beschwert. 

Woher wusste ich, dass ich `#include <stdio.h>` für
`printf()` benötige?[i[`printf()` function]>] Antwort: es steht in der Dokumentation. Rufe `man 3 printf` auf wenn Du ein Unix System verwendest und ganz oben in der Anleitung wird stehen, welche Header benötigt werden. Oder schau Dir den Referenz Band dieses Buches an. `:-)` [i[`stdio.h` header file]>]

Zugegeben war das recht viel für die erste Zeile. Dafür verstehen wir
sie jetzt. Wir wollen nichts im Dunkeln hinterlassen!

Also, ruhe Dich kurz aus  ... und wende Dich dann wieder dem
Beispiel-Code zu. Nur noch ein paar Zeilen.

Willkommen zurück aus der Pause! Ich weiss, dass Du nicht wirklich eine
Pause gemacht hast. Das ist nur meine Art von Humor.

[i[`main()` function]<]Die nächste Zeile enthält `main()`. Dabei handelt es sich um die
Definition der Funktion `main()`; alles zwischen den geschweiften Klammern (`{`
und `}`) gehört zur Definition der Funktion.

(Wie ruft man eine andere Funktion auf? Die Antwort befindet sich in der
Zeile `printf()`. Aber dazu kommen wir noch.)

Die `main()` funktion ist in vielerlei Hinsicht besonders, aber der
wichtigste Aspekt ist, dass diese Funktion automatisch aufgerufen wird,
wenn Dein Programm anfängt zu laufen. Nicht was Du schreibst wird vor
`main()` aufgerufen. Da wir im Beispielprogramm nur eine Zeile drucken
und im Anschluss das Programm beendet wollen, ist das genau was wir
wollen.

Eine Sache noch: sobald das Programm am Ende von `main()`, hinter der
letzten geschweiften Klammer angekommen ist, wird es beendet und Du
kehrst zur Eingabeaufforderung zurück.

 Jetzt
 wissen wir als, dass das Programm eine Header Datei names
`stdio.h`[i[stdio.h]T] importiert hat und eine Funktion namens `main()`
deklariert hat, die ausgeführt wird, wenn das Programm startet. Was sind
die anderen Leckerbissen in `main()`[i[`main()` function]>]?

Schön, dass Du fragst! Genau genommen gibt es nur einen Leckerbissen:
den Aufruf der Funktion [i[`printf()` function]<]`printf()`. Ein paar Dinge
erlauben es Dir zu erkennen, dass es sich um einen Aufruf und nicht um
eine Definition der Funktion handelt. Zum einen fehlen die geschweiften
Klammern. Und das abschliessende Semikolon weist den Compiler darauf
hin, dass es sich um das Ende eines Ausdrucks (Expression) handelt. Du
wirst nach den meisten Sachen ein Semikolon platzien, wie Du bald sehen

Du übergibst ein Argument an die Funktion `printf()`[i[`printf()`
function]>]: eine Zeichenkette (string) die bei dem Funktionsaufruf
gedruckt wird. Ach so. Wir rufen also eine Funktion auf! Wuhu! 
Aber Moment noch, nicht gleich übermütig werden. [i[`\n`
newline]<]Was'n das bekloppte `\n` am Ende der Zeichenkette? 
Die meisten Zeichen in dem String werden so ausgedruck, wie sie
dargestellt werden, aber manche Zeichen lassen sich schlecht darstellen
und tippen die werden mit Backslash Codes dargestellt. Einer der
beliebtesten Codes ist `\n` (gesprochen "Backslash-N" oder "newline")
und dem _newline_ Zeichen entspricht. Dieses Zeichen führt dazu, dass
nachfolgende Zeichen auf einer neuen Zeile statt auf der aktuellen dargestellt werden.
_newline_ character. This is the character that causes further printing
So als ob man die Return Taste drücken würde.[i[`\n` newline]>]

Jetzt solltest Du den Code in eine Datei names `hello.c` einfügen und
ihn bauen.and build it. Auf eine Unix-ähnlichen Platform (z.B. Linux, BSD, Mac,
oder WSL), baust Du wie folgt von der Kommandozeile:

[i[`gcc` compiler]]
``` {.zsh}
gcc -o hello hello.c
```

(Das meint "kompiliere `hello.c`, und erzeuge als Ausgabe das Programm
`hello`".) (-o steht für "Output")

Wenn das abgeschlossen ist, solltest Du eine Datei names  `hello`
vorliegen habe, welches Du wie folgt aufrufen kannst:

``` {.default}
./hello
```

(Das führende  `./` weist die Shell an, dass das Programm aus dem aktuell Verzeichnis verwendet werden soll.)

Schauen wir, was passiert:

``` {.default}
Hello, World! 
```

Das Programm ist fertig und getestet! Wir sind also bereit es
auszuliefern![i[Hello, world]>]

## Compilation Details

[i[Compilation]<] Lass  und ein bisschen ausführlicher besprechen, wie C
Programme gebaut werden und was hinter dem Vorhang alles passiert.

So wie auch andere Sprach hat C _Quelltext_. Je nachdem an welche
anderen Sprachen Du gewöhnt bist, hast Du Deinen Quelltext zu einem
_ausführbaren_ Programm _kompilieren_ müssen.

Bei Kompilieren (oder Übersetzen) handelt es sich um den Prozess der
Deinen C Quelltext nimmt und ihn in ein Programm umwandelt, das vom
Betriebssystem ausgeführt werden kann.

JavaScript und Python Entwickler sind diesesn seperaten Schritt des
Übersetzens nicht gewohnt, auch wenn er hinter den Kulissen trotzdem
passiet. Python kompiliert Quelltext zu etwas, was _bytecode_ genannt
wird, der wiederrum von der Python _Virtual Machine_ ausgeführt wird.
Java Entwickler sind kompilieren gewohnt, da wird allerdings auch
bytecode produziert welcher von der Java Virtual Machine ausgeführt
wird.

Wenn C kompiliert wird, wird _Maschinensprache_ erzeugt. dabei handelt
es sich um die 1 und 0 die von der CPU unmittelbar ausgeführt werden


> Sprachen werden typischerweise als _interpretiert_ bezeichnet wenn sie
> nicht kompiliert werden. Aber wie bei den Beispielen Java und Python
> erwähnt wurde, werden diese auch kompiliert. Und es gibt keine Regel
> die besagt, dass C nicht interpretiert werden darf. (Es gibt
> tatsächlich C Interpreter). Kurz gesagt, die Situation ist ein
> bisschen schwammig. Ganz allgemein gesagt handelt es sich beim
> Kompilieren darum, dass Code von einer Form in eine leichter zu
> verabeitende umgewandelt wird.

Der C Compiler ist das Programm, welches das Kompilieren ausführt.


Wie bereits erwähnt, handelt es sich bei`gcc` um einen Compiler der auf vielen
[flw[Unix-like operating systems|Unix]] bereits installiert ist. Typischerweise 
wird er von der Kommandozeile aus angestossen, muss aber nicht. Er kann
auch von eine IDE gestartet werden.

Wie bauen wir als unsere Software von der Kommandozeile?

## Mit `gcc` bauen

[i[`gcc` compiler]<] Wenn eine Quelltextdatei namens `hello.c` im
aktuellen Verzeichnis vorliegt kann diese durch folgenden Befehl in das
Programm `hello` übersetzt werden:

``` {.zsh}
gcc -o hello hello.c
```

Das `-o` gibt an, wie die Ausgabedatei heissen soll^[Sofern kein Name
für eine Ausgabedatei angegeben wird, wird eine Datei namens `a.out`
verwendet---dieser Dateiname ist tief verwurzelt in der Geschichte von
Unix.]. Und man Ende steht `hello.c`, der Name der Datei, die kompiliert
werden soll.

Wenn Quelltext über mehrere Datei aufgeteilt wird, kann man alle
gleichzeitig kompilieren, fast als ob es sich um eine einzige Datei
handelt (aber die Details sind etwas komplizierter) in dem man alle 
`.c` files on the auf der Kommandozeile aufführt.

``` {.zsh}
gcc -o awesomegame ui.c characters.c npc.c items.c
```
[i[`gcc` compiler]>]

Die Dateien werden dann zu einem grossen Programm zusammengeführt.

Das sollte für den Start reichen---wir kehren später zu dem Thema zurück
und besprechen Details bezüglich mehrerer Datein, Objekt-Datei und noch
viel mehr.[i[Compilation]>]

## Mit `clang` bauen

Auf Macs is der standard Compiler nicht `gcc` sondern heisst `clang`[i[`clang`
compiler]]. Es wird aber ein Wrapper installiert, so das Aufrufe `gcc` 
trotzdem funktionieren.

Du kann den echten `gcc`[i[`gcc` compiler]] compiler aber 
mit [fl[Homebrew|https://formulae.brew.sh/formula/gcc]]  oder
anderen Mitteln installiern.

## Building from IDEs

[i[Integrated Development Environment]<] Sofern Du eine
_Entwicklungsumgebung_ (IDE) benutzt, muss Du wahrscheinlich nicht von
der Kommandozeile bauen.

In Visual Studio stösst `CTRL-F7` das Bauen an, und `CTRL-F5` führt das
gebaute Progamm aus.

In VS Code, kann man mit `F5` das Programm im Debugger starten. (Dafür
muss die C/C++ Extension installiert sein.)

In XCode baut man mit `COMMAND-B` und führt das Programm mit `COMMAND-R`
(run) aus. Um von der Kommandozeile zu arbeiten, musst Du eine Anleitung
befolgen, welchen Du findest, wenn Du nach "XCode command line tools" suchst. 

Um loszulegen solltest du versuchen, die Kommandozeile zu nuten. Es
handelt sich um ein Stück Geschichte![i[Integrated Development Environment]>]

## C Versionen

[i[Language versions]<]C hat sich im Laufe der Jahre stark
weiterentwicklet. Dementsprechend gibt es eine Vielzahl benannter
Versionen um unterschiedliche Dialekte zu beschreiben.

Meistens referenzieren die das Jahr der Standartisierung.

Die berühmtesten sind C89, C99, C11 and C2x. Wir konzentrieren uns in
diesem Buch zwar auf spätere Versionen. Aber hier ist eine etwas
deatiliertere Tabelle:


|Version|Description|
|-----|--------------|
|K&R C| Das Original von 1978. Nach Brian Kernighan and Denis Richie benannt. Richie hat die Sprach entworfen und entwickelt und Kernighan was Koauthor des Buches. Heutzutage sieht man selten K&R Code. Sollte Dir mal welcher über den Weg laufen, wird er vermutlich eigentumlich wirken, so wie Mittelhochdeutsch für Leser\*nnen von modernem Deutsch eigentümlich erscheint|
|**C89**, ANSI C, C90|1989 hat das American National Standards Institute (ANSI) ein Spezifikation der C Sprache erstellt, die bis heute den Charaktuer von C ausmacht. Ein Jahr später wurde die Hoheit über die Spezifikation an die International Organization for Standardization (ISO) übergeben, die die identische Spezifikation als C90 herrausgab| 
|C95|Eine selten erwähnte Erweiterung von C89, die `wide character support` einführte|
|**C99**|Die erste größere Überarbeitnug mit vielen Spracherweiterungen. Die meisten erinnern sich an die Einführung von mit `//` angeführten Kommentaren. Es handelt sich zum Zeitpunkt des verfassen um die gängigste Fassung|
|**C11**|Diese "major" Version beinhaltete Unterstützung von Unicode Zeichenketten und multi-threading. Unter Umständen kann die Verwendung dieser Spracherweiterungen auf Kosten von Portabilität gehen, da manche Compiler in C99-Land festhängen|
|C17, C18|Es handelt sich um Korrekturen von C11. C17 scheint der offizielle Name zu sein, der Standard wurde aber erst 2018 veröffentlich. So weit ich es durchschaue, sind beide Bezeichnungen austtauchbar, bevorzugt wird C17|
|C2x|Was die Zukunft bringt! Wird vermultlich eines Tages C23 sein.|

[i[`gcc` compiler]<] Man kann dem `--std=` Kommandozeilen Argument GCC
zwingend sich an einen spezifschen Standard zu halten. Wenn Du den
gewählten Standard pingelig einhalten willst, kann Du noch `-pedantic`
hinzufügen.

Zum Beispiel so:

``` {.zsh}
gcc -std=c11 -pedantic foo.c
```

Alle Beispielprogramme in diesem Buch kompiliere ich für C2x mit allen
Warnungen aktiviert:

``` {.zsh}
gcc -Wall -Wextra -std=c2x -pedantic foo.c
```
[i[`gcc` compiler]>]
