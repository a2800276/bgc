<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

<!-- No hyphenation -->
[nh[scalbn]]
[nh[scalbnf]]
[nh[scalbnl]]
[nh[scalbln]]
[nh[scalblnf]]
[nh[scalblnl]]
<!-- Can't do things that aren't letters
[nh[atan2]]
[nh[atan2f]]
[nh[atan2l]]
-->
[nh[lrint]]
[nh[lrintf]]
[nh[lrintl]]
[nh[llrint]]
[nh[llrintf]]
[nh[llrintl]]
[nh[lround]]
[nh[lroundf]]
[nh[lroundl]]
[nh[llround]]
[nh[llroundf]]
[nh[llroundl]]

<!-- Index see alsos -->
[is[String==>see `char *`]]
[is[New line==>see `\n` newline]]
[is[Ternary operator==>see `?:` ternary operator]]
[is[Addition operator==>see `+` addition operator]]
[is[Subtraction operator==>see `-` subtraction operator]]
[is[Multiplication operator==>see `*` multiplication operator]]
[is[Division operator==>see `/` division operator]]
[is[Modulus operator==>see `%` modulus operator]]
[is[Boolean NOT==>see `!` operator]]
[is[Boolean AND==>see `&&` operator]]
[is[Boolean OR==>see `||` operator]]
[is[Bell==>see `\a` operator]]
[is[Tab (is better)==>see `\t` operator]]
[is[Carriage return==>see `\r` operator]]
[is[Hexadecimal==>see `0x` hexadecimal]]

# Vorwort

> _C is not a big language, and it is not well served by a big book._
>
> --Brian W. Kernighan, Dennis M. Ritchie

> _C ist keine große Sprache, und ein großes Buch ist nicht gut für sie.
>
> --Brian W. Kernighan, Dennis M. Ritchie
<!-- TODO check original Translation? -->


Keine Zeit verschwenden! Lass uns direkt _in medias res_ gehen:


``` {.c}
E((ck?main((z?(stat(M,&t)?P+=a+'{'?0:3:
execv(M,k),a=G,i=P,y=G&255,
sprintf(Q,y/'@'-3?A(*L(V(%d+%d)+%d,0)
```

And they lived happily ever after. The End.
Und wenn sie nicht gestorben sind, dann leben sie noch heute.

Was ist los? Du meinst es bestehen noch Ungereimheiten bezüglich dieser
C Programmiersprache?

Naja, um ganz ehrlich zu sein, bin ich mir nicht einmal sicher, was der
obige Code macht. Es handelt sich um einen Schnipsel aus einem der
2001er [fl[International Obfuscated C Code
Contest|https://www.ioccc.org/]][i[Internationaler Obfuscated C Code
Contest]] Beiträgen, einem wunderbaren Wettbewerb, bei dem die
Teilnehmer versuchen, einen möglichst unleserlichen C Code zu schreiben,
mit oft überraschenden Ergebnissen.

Wenn Du ein Anfänger bist, sieht aber leider jeglicher C Code ähnlich
obfuskiert aus. Die gute Nachricht ist, dass das nicht lange so bleiben
wird!

Was wir mit diesem Buch beabsichtigen, ist Dich aus der vollständigen,
dunkeln Verwirrung zu der Erleuchtung zu führen, die nur durch reine C
Programmierung erreicht werden kann. Oder so. 

Vor langer Zeit war C eine einfachere Sprache. Einige der Funktionen in
diesem Buch und _jede Menge_ der Funktionalität der des zweiten Bandes,
der Library Referenz, gab es nicht als K&R die berühmte zweite Ausgabe
ihres Buches 1988 schrieben. Nichtsdestotrotz bleibt die Sprache in
ihrem Kern klein, und ich hoffe, dass ich es geschafft habe, sie mit
dem einfachen Kern anfange und darauf aufbaue.

Und das ist meine Ausrede dafür, dass ich ein so lachhaft umfangreiches
Buch für eine so eine kleine, knappe Sprache schreibe.

## Zielgruppe

Dieses Buch geht davon aus, dass Sie bereits über Programmierkenntnisse aus einer anderen Sprache verfügen,  zum Beispiel
[flw[Python|Python_(Programmier_sprache)]],
[flw[JavaScript|JavaScript]], [flw[Java|Java_(Programmiersprache)]],
[flw[Rust|Rust_(Programmiersprache)]],
[flw[Go|Go_(Programmier_sprache)]],
[flw[Swift|Swift_(Programmiersprache)]], usw.
([flw[Objective-C|Objective-C]]-Entwickler werden es besonders leicht haben
haben!)

Wir werden davon ausgehen, dass Du weisst, was Variablen sind, was
Schleifen bewirken, was Funktionen tun, und so weiter.

Wenn das aus irgendeinem Grund nicht auf dich zutrifft, kann ich nur
hoffen, dass ich Dir etwas aufrichtige Unterhaltung zu Deinen Vergnügen
bieten kann. Das einzige, was ich versprechen kann, ist, dass dieser
Leitfaden nicht mit einem Cliffhanger enden wird... oder vielleicht
_doch_? 

## Wie man dieses Buch liest
 
Der Leitfaden besteht aus zwei Bänden, und dies ist der erste: der Tutorialband!
 
Der zweite Band ist die [fl[Bibliothek
Referenz|https://beej.us/guide/bgclr/]], und er ist weit mehr Referenz
als Anleitung.
 
Wenn Du neu bist, solltest Du das Tutorial in der Regel der Reihe nach
durcharbeiten. Je fortgeschrittener im Text du Dich befindest, desto
weniger wichtig ist es, die Reihenfolge einzuhalten.
 
Und unabhängig von deinem Kenntnisstand gibt es den Referenzteil mit mit
Beispielen für Aufrufe aller Funktionen der Standardbibliothek, damit
Du Dein Gedächtnis jederzeit auffrischen kannst. Ideal zum Lesen bei
einer Schüssel Müsli.

Zu guter Letzt, wen Du in Druckfassung blickst, sind die Einträge im Register, die auf den
 Referenzteil verweisen, kursiv gedruckt.

## Plattform und Compiler

Ich werde versuchen, mich auf langweiliges, altmodisches
[flw[ISO-standard C|ANSI_C]] zu beschränken. Zumindest größtenteils.
Hier und da könnte ich ausflippen und anfangen über [flw[POSIX|POSIX]]
oder so zu reden, schauen wir mal.

**Unix**-Benutzer (z.B. Linux, BSD, etc.) sollten versuchen, `cc` oder
`gcc` von der Kommandozeile aus auszuführen, eventuell haben Sie bereits
einen Compiler installiert. Falls nicht, suche danach, wie in Deiner
Distribution `gcc` oder `clang` installiert wird.

**Windows**-Benutzer sollten sich die [fl[Visual Studio
Community|https://visualstudio.microsoft.com/vs/community/]] anschauen.
Oder installiere
[fl[WSL|https://docs.microsoft.com/en-us/windows/wsl/install-win10]] und
`gcc`, wenn Du eine eher Unix-ähnliche Erfahrung suchst (wärmstens
empfohlen!)

**Mac**-Benutzer werden [fl[XCode|https://developer.apple.com/xcode/]]
installieren wollen, und zwar insbesondere die Kommandozeilen-Tools.

Es gibt eine Vielzahl von Compilern, und praktisch alle werden mit
diesen Buch funktionieren. Und selbst ein C++-Compiler kompiliert den
meisten (aber nicht allen!) C-Code. Verwende am besten einen richtigen
C-Compiler, wenn es geht.

## Offizielle Homepage

Der offizielle Speicherort dieses Dokuments ist
[fl[https://beej.us/guide/bgc/|https://beej.us/guide/bgc/]]. Vielleicht
wird sich das in Zukunft ändern, aber es ist wahrscheinlicher, dass alle anderen
Leitfäden von den Chico State Computern migriert werden.


## Email Regeln

Im Allgemeinen stehe ich für Email Anfragen zur Verfügung also schreibe
mir ruhig. Ich kann aber keine Antwort versprechen. Ich habe ein
ziemlich geschäftiges Leben und es kommt vor, dass ich eine Frage nicht
beantworten kann. Wenn das der Fall ist, lösche ich die Nachricht
normalerweise einfach. Nimm's nicht persönlich, ich werde nie die Zeit
finden alle Fragen gebührend zu beantworten.

Je komplizierter die Frage ist, desto unwahrscheinlicher ist es, dass
ich antworten werde. Fragen thematisch einzugrenzen und alle relevanten
Informationen (wie Plattform, Compiler, erhaltene Fehlermeldung und
alles weitere, was bei der Fehlersuche behilflich sein könnte) anzugeben
erhöht die Wahrscheinlichkeit einer Antwort erheblich. 

Solltest Du keine Antwort erhalten, bastel ein bisschen weiter rum,
versuche die Frage selber zu klären und falls Du trotzdem nicht fündig
wirst, schreib nochmal mit den neue Erkenntnisse. Hoffentlich reicht es
dann aus, dass ich helfen kann.

Nach der ausführlichen Anleitungen, wie mir zu schreiben und nicht zu
ist, möchte ich feststellen, dass ich den Lob, den ich über die Jahre zu
diese Guides erhalten habe sehr zu schätzen weiß. Er ist ein großer
Ansporn und ich freue mich, dass sie für gute Zwecke eingesetzt werden!
`:-)` Danke!

## Vervielfältigung

Sie sind herzlich eingeladen, diese Website zu spiegeln, sowohl
öffentlich als auch privat. Wenn Sie die Seite öffentlich spiegeln und
möchten, dass ich den Mirror von der Hauptseite aus verlinke, schreiben
Sie mir eine Nachricht an [`beej@beej.us`](mailto:beej@beej.us).
 
## Hinweis für Übersetzer
 
Wenn Sie den Leitfaden in eine andere Sprache übersetzen wollen,
schreiben Sie mir an [`beej@beej.us`](beej@beej.us) und ich werde Ihre
Übersetzung  von der der Hauptseite verlinken. Fügen Sie der Übersetzung
ruhig Ihren Namen und Ihre Kontaktinformationen hinzu

Bitte beachten Sie die Lizenzbeschränkungen im folgenden Abschnitt:
Copyright und Verbreitung. 

## Copyright und Verbreitung
 
Beej's Guide to C ist Copyright © 2021 Brian "Beej Jorgensen" Hall.
 
Mit spezifischen Ausnahmen für Quellcode und Übersetzungen, weiter
unten, steht dieses Werk unter der Creative Commons
Attribution-Noncommercial-No Derivative Works Abgeleitete Werke 3.0
Lizenz. Um eine Kopie dieser Lizenz einzusehen, besuche
[`https://creativecommons.org/licenses/by-nc-nd/3.0/`](https://creativecommons.org/licenses/by-nc-nd/3.0/)
oder senden Sie einen Brief an Creative Commons, 171 Second Street,
Suite 300, San Francisco, Kalifornien, 94105, USA.

Eine spezifische Ausnahme zum Teil "Keine abgeleiteten Werke" der Lizenz
ist folgende: Dieser Leitfaden darf frei in jede Sprache übersetzt
werden, vorausgesetzt, die Übersetzung ist korrekt und der Leitfaden
wird in seiner Gesamtheit abgedruckt. Für die Übersetzung gelten die
gleichen Lizenzbeschränkungen wie für den ursprünglichen Text. Die
Übersetzung darf außerdem den Namen und die Kontaktinformationen des
Übersetzers enthalten.

Der C-Quellcode, der in diesem Dokument präsentiert wird, wird hiermit
der der Öffentlichkeit zugänglich gemacht und ist völlig frei von
Lizenzbeschränkungen (public domain).

Lehrende sind aufgefordert, Kopien dieses Leitfadens an ihre
Studierenden weiterzugeben oder zu empfehlen.

Kontaktiere [`beej@beej.us`](beej@beej.us) für weitere Informationen.

## Widmung

Das Schwierigste beim Schreiben dieser Leitfäden ist:

* Das Material so detailliert zu lernen, dass ich es erklären kann.
* Herauszufinden, wie man den Stoff am besten erklärt, ein scheinbar endloser
  iterativer Prozess
* Mich als sogenannte _Autorität_ aufzuspielen, obwohl ich in Wirklichkeit ein normaler Mensch bin, der versucht, alles zu verstehen, genau wie jeder andere auch
* Dranbleiben, wenn so viele andere Dinge mich verlocken

Viele Menschen haben mir während dieses Prozesses geholfen, und ich möchte
Ich möchte mich bei denen bedanken, die dieses Buch ermöglicht haben.

* Alle im Internet, die sich entschlossen haben, ihr Wissen in der ein- oder anderen Form zu teilen. Der freie Austausch von lehrreichen Informationen ist es
  was das Internet zu einem so großartigen Ort macht.
* Den Freiwilligen bei [fl[cppreference.com|https://en.cppreference.com/]]
  die die Brücke von der Spezifikation zur realen Welt schlagen.
* Die hilfreichen und sachkundigen Leute auf
  [fl[comp.lang.c|https://groups.google.com/g/comp.lang.c]] und 
  [fl[r/C_Programming|https://www.reddit.com/r/C_Programming/]], die mir
  die mich durch die schwierigeren Teile der Sprache gebracht haben.
* Alle, die Korrekturen und Pull-Requests eingereicht haben
  von missverständlichen Anweisungen bis hin zu Tippfehlern.


Vielen Dank! ♥
