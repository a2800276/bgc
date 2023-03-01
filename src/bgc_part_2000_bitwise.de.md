<!-- Beej's guide to C

# vim: ts=4:sw=4:nosi:et:tw=72
-->

# Bitweise Manipulationen

[i[Bitwise operations]<]

Die hier beschriebenen Operationen erlauben es, individuelle Bits von
Werten zu manipulieren. Passend, da C auf einer niedrigen Ebene
ansetzt^[Es ist natürlich nicht der Fall, dass andere Sprachen dies nich
zulassen--tun sie. Insbesonders ist interessant wie viele moderne
Sprachen dieselben Operatoren für Bit Manipulation verwenden wie C].

Sofern Dir Bit Manipulation nicht geläufig sind, bietet
[flw[Wikipedia eine gute Einführung|Bitwise_operation]].

## Bitwise AND, OR, XOR, and NOT

Für alle diese Operationen werden die
[üblichen arithmetischen Konversionen](#usual-arithmetic-conversions) auf den Operanden ausgeführt (bei denen es sich zwingend um ganzzahlige Typen handeln muss). Anschliessend wird die bitweise Operation durchgeführt.

|Operation|Operator|Beispiel|
|-|:-:|-|
|[i[`&` bitwise AND]]AND|`&`|`a = b & c`|
|[i[`|` bitwise OR]]OR|`|`|`a = b | c`|
|[i[`^` bitwise XOR]]XOR|`^`|`a = b ^ c`|
|[i[`~` bitwise NOT]]NOT|`~`|`a = ~c`|

Beachte die Ähnlichkeit der Booleschen Operatoren  `&&` und `||`.

Es gibt auch verkürzte Zuweisungsvarianten, ähnlich `+=` and `-=`:

|Operator|Beispiel|Äquivalent|
|-|-|-|
|[i[`&=` assignment]]`&=`|`a &= c`|`a = a & c`|
|[i[`|=` assignment]]`|=`|`a |= c`|`a = a | c`|
|[i[`^=` assignment]]`^=`|`a ^= c`|`a = a ^ c`|

## Bitweiser Shift
<!-- Translator Note: TODO integer Promotion -->
Dafür wird [i[Integer promotions]] [integer
promotions](#integer-promotions) bei allen Operanden durchgeführt. Auch
hier muss es sich um ganzahlige Typen handeln. Im Anschluss wird der
Shift (bitweise Verrückung) durchgeführt. Der Typ
des Ergebnisses ist der Typ des _promoteten_ linken Operanden: 

"Neue" Bits werden mit Nullen gefüllt, Ausnahme zu diesem Verhalten sind
unten aufgeführt.

|Operation|Operator|Beispiel|
|-|:-:|-|
|[i[`<<` shift left]]Shift left|`<<`|`a = b << c`|
|[i[`>>` shift right]]Shift right|`>>`|`a = b >> c`|

Verkürzte Schreibweise gibt es auch für Shifts:

|Operator|Beispiel|Äquivalent|
|-|-|-|
|[i[`>>=` assignment]]`>>=`|`a >>= c`|`a = a >> c`|
|[i[`<<=` assignment]]`<<=`|`a <<= c`|`a = a << c`|

Halte die Augen auf für undefiniertes Verhalten: keine negativen Shifts
und keine Shifts die größer sind als der promotete linke Operand.

Sei ebenso auf der Hut bezüglich Implementierungs-spezifischem
Verhalten: rechte Shifts einer negativen Zahl sind
Implementierungs-spezifisch. (`int`'s mit Vorzeichen (signed) darf man
selbstverständlich rechts shiften, man muss nur sicher sein, das die
Werte positiv sind).

[i[Bitwise operations]>]
