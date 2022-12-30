---
title: "Zajęcia 10: `Map` i `enum class`"
date: 2023-01-03T14:30:00+01:00
draft: false
---

{{<toc>}}

# *"Po jakie licho są nam klasy?"*

Pytanie, które zadał mi po ostatnich zajęciach Jurek, i które pewnie niejednemu z was przyszło do głowy.

Programy, które napisaliśmy do tej pory, faktycznie dało napisać się bez tworzenia własnych klas. Przy kolejnych, bardziej skomplikowanych programach, korzystanie jedynie z wbudowanych typów danych będzie albo niepraktyczne, albo wręcz niemożliwe. Dlatego też chciałem powoli zacząć wprowadzać pojęcie klas.

Na przykład, punkt na dwuwymiarowej płaszczyźnie można opisać i przekazywać do funkcji jako dwie liczby `x` i `y`. Nie da się natomiast napisać funkcji, która zwróci dwie liczby: instrukcja `return` może zwrócić tylko jeden obiekt. Tak więc funkcja, która miałaby zwracać współrzędne `x` i `y`, musi je "opakować" w jakiś obiekt.

Przed podobnym dylematem staniemy, jeśli będziemy potrzebować listy punktów: teoretycznie możliwe jest stworzenie dwóch list (jedna na współrzędne `x`, druga na współrzędne `y`), albo wrzucanie współrzędnych naprzemiennie do jednej listy (czyli zrobienie `listOf(x1, y1, x2, y2, itd.)`), albo zastosowanie jeszcze bardziej karkołomnej konstrukcji. Lepiej będzie jednak utworzyć `data class Point(val x: Int, val y: Int)`, i korzystać po prostu z `List<Point>`.

Inna korzyść z używania własnych typów danych to lepsze odzwierciedlenie w kodzie problemu, nad którym pracujemy. Fachowo programiści nazywają to "modelowaniem domeny". Chodzi z grubsza o to, że jeśli w naszym programie robimy jakieś rzeczy z punktami na płaszczyźnie, to dobrze jest mieć klocki, które odpowiadają tym punktom, zamiast żonglować "luźnymi" `Int`ami. Dobre "zamodelowanie" obiektów znacznie ułatwia pisanie pozostałych części programu i utrudnia zrobienie błędów.

# Nowy materiał

## `enum class`

W programie "Kółko i Krzyżyk" przechowywaliśmy planszę jako `List<String>`. Wspomniałem wówczas, że nie jest to najlepsze rozwiązanie. Użycie takiej struktury umożliwia popełnienie błędów w stylu używanie w jednym miejscu małego `x` a w innych dużego `X` to reprezentowania symbolu krzyżyka.

Zamiast korzystać ze `String`'ów moglibyśmy stworzyć własny typ opisujący symbol na planszy. Poznaliśmy do tej pory dwa rodzaje własnych typów:
- `class` (oraz `data class`), gdy chcemy mieć możliwość stworzenia dowolnej ilości obiektów jakiegoś typu
- `object`, gdy zależy nam na tym, żeby istniał tylko jeden obiekt tego typu (pamiętacie `MisterneRęcznieWystruganeKrzesłoWujkaWładka`?)

Żadne z tych rozwiązań nie pasuje do "Kółka i Krzyżyk" gdzie potrzebujemy dokładnie dwóch obiektów naszego nowego typu: jednego dla kółek, drugiego dla krzyżyków. Potrzebny będzie nam nowy rodzaj obiektów: `enum class`.

```kotlin
enum class Symbol { KÓŁKO, KRZYŻYK }
```

Pierwszą rzeczą w ciele obiektów typu `enum` jest oddzielona przecinkami lista wszystkich obiektów tego typu. Dla wartości `enum`'ów przyjęło się stosować nazwy w formie `SNAKE_UPPER_CASE`, czyli wyrazy pisane wielką literą oddzielone znakiem `_`.

Możemy później użyć tego typu do przechowywania danych o planszy:

```kotlin
// używamy opcjonalnego typu, null będzie oznaczał puste pole
val plansza = mutableListOf<Symbol?>()
for (i in 1..9) { plansza.add(null) }
```

Podobnie jak przypadku innych `class`, możemy do naszego `enum`a dodać konstruktor:

```kotlin
enum class Symbol(val znaczek: Char) {
    KÓŁKO,
    KRZYŻYK
}
```

Linijki z `KÓŁKO` i `KRZYŻYK` zostaną podkreślone na czerwono: w tych miejscach tak naprawdę tworzymy obiekty typu `Symbol`, a właśnie powiedzieliśmy, że każdy obiekt tego typu powinien otrzymać w konstruktorze `znaczek` typu `Char`. Musimy dodać odpowiednie wywołania konstruktorów:

```kotlin
enum class Symbol(val znaczek: Char) {
    KÓŁKO('O'),
    KRZYŻYK('X')
}
```

## `Map`

Poznajmy kolejną strukturę danych: `Map`.

W niektórych językach programowania (np: w Pythonie czy w C#) ta struktura danych nazywana jest słownikiem i myślę, że na tym przykładzie będzie wam to łatwo wytłumaczyć.

Załóżmy, że mamy "papierowy" słownik polsko-angielski i potrzebujemy przetłumaczyć kilka wyrazów. Każdy wpis w takim słowniku składa się z dwóch części: polskiego wyrazu oraz jego angielskiego tłumaczenia (zazwyczaj nie jest to pojedynczy wyraz w języku angielskim, ale kilka synonimów; w niektórych słownikach można też znaleźć przykładowe zdania). Hasła w słowniku ułożone są w porządku alfabetycznym, żeby ułatwić szukanie tłumaczeń.

A czy jest możliwe przetłumaczenie wyrazów "w drugą stronę", tj. angielskich wyrazów na język polski przy użyciu słownika polsko-angielskiego? Czysto teoretycznie tak – wystarczy "tylko" przeczytać cały słownik od deski do deski. Jest to możliwe, ale niepraktyczne.

Na takiej samej zasadzie działa struktura danych `Map`. Każdy wpis w naszej "mapie" składa się z dwóch elementów: Klucza i Wartości. W naszej słownikowej analogii Klucz to polski wyraz, a Wartość to wpis zawierający angielskie tłumaczenia. Wartości można łatwo i szybko odszukać, jeśli znamy pasujący Klucz, a przeszukiwanie samych Wartości jest możliwe, ale nieefektywne.

## Słownik

Napiszmy prosty program tłumaczący polskie wyrazy na język angielski.

Tłumaczenia będziemy oczywiście przechowywać w strukturze Map. Możemy ją utworzyć przy użyciu funkcji `mapOf`. Podobnie, jak w przypadku `listOf`, będziemy musieli podać w trójkątnych nawiasach, jakiego typu będą Klucze i
Wartości w naszej mapie. Dla uproszczenia użyjmy typu `String` zarówno dla Kluczy, jak i Wartości:

```kotlin
val słownik = mapOf<String, String>()
```

W nawiasach okrągłych możemy podać zawartość naszej `Map`y. Każdy element w `Map`ie składa się z dwóch części, Klucza i Wartości, więc musimy użyć następującej składni:

```kotlin
val słownik = mapOf<String, String>(
    "pies" to "dog"
)
```

Słówko `to` pomiędzy dwoma tekstami wygląda trochę dziwnie, ale w rzeczywistości to jest po prostu wywołanie funkcji. Funkcja biblioteczna `to` została oznaczona specjalnym modyfikatorem `infix`, żeby można było wywoływać ją w taki sposób. Typ zwracany przez funkcję `to` to `Pair`, czyli para obiektów:

```kotlin
val wpis: Pair<String, String> = "pies" to "dog"
```

`Map` podobnie jak `List` jest "niemutowalna", czyli po jej utworzeniu nie można zmieniać jej zawartości. Istnieje też coś takiego jak `MutableMap`, czyli "mutowalny" odpowiednik naszej `Mapy`, który można utworzyć za pomocą funkcji `mutableMapOf`.

Wyszukiwanie wartości w `Map`ie odbywa się za pomocą operatora `[]`:

```kotlin
val słownik = mapOf<String, String>(/* wpisy */)
val polskiWyraz: String = readln()
val angielskiWyraz: String? = słownik[polskiWyraz]
```

Zauważcie, że wynik wyszukiwania jest typem opcjonalnym (z `?` doklejonym do typu przechowywanego w `Map`ie): nie możemy przecież zagwarantować, że wpis z podanym przez nas kluczem będzie znajdować się w naszej `Map`ie. Pamiętajcie o tym przypadku, gdy będziecie korzystać z tej struktury danych!

# Co dalej?

Poznane dzisiaj narzędzia pozwolą nam w sensowny sposób napisać kolejny duży program: grę "Bitwa Morska", czyli znaną wam z wersji papierowej grę "w statki". Będziemy się tym zajmować na następnych zajęciach (i prawdopodobnie nie skończymy – spodziewam się, że na losowanie pozycji statków będziemy potrzebować kolejnych całych zajęć).

Zgodnie odrzuciliście propozycję rozwiązywania zagadek programistycznych. Bajtazar z Bajtocji będzie musiał znaleźć innych pomocników, a my skupimy się na pisaniu kolejnych gier. Po "Bitwie Morskiej" weźmiemy na warsztat klon "Wordle", "Sapera" i napiszemy kilka prostych animacji.

Myślę, że dobrze byłoby też wrócić do wcześniej napisanych programów i spróbować użyć nowych rzeczy:
* W programie "Kółko i Krzyżyk" zamiast typu `String` można użyć `enum class`. Będziemy potrzebować dwóch typów: jednego do reprezentowania symbolu (kółko lub krzyżyk), drugiego do wyniku (wygrana X, wygrana O, remis).
* Spróbujcie przechowywać planszę w grze "Kółko i Krzyżyk" jako `MutableMap<…>` zamiast `MutableList<…>`
* Nasza struktura danych w "Wisielcu" była nieco koślawa. Można ją trochę naprostować używając `data class Literka(val char: Char, var odkryta: Boolean)` (zauważcie, że `odkryta` to zmienna!).