# Funkcyjnie z Haskellem
## Część 1: Nowe poziomy abstrakcji
### Dlaczego funkcyjnie?
Programowania obiektowego uczyli mnie na studiach, obiektowe projektowanie ćwiczyłem w pracy, wszyscy byli szczęśliwi, bo mieliśmy jeden poprawny sposób tworzenia naszych aplikacji. Najlepiej w Javie, czasem C#. Pewnego dnia kolega przyszedł do pracy i powiedział, że on chce teraz pisać w Scali i funkcyjnie. Nie zrozumiałem z tego nic. Sprawdziłem, co to Scala, okazało się, że język programowania kompilujący się do JVM. Pomyślałem wtedy, takie wynalazki już widziałem - Jython, JRuby. Nie chwyciło mnie za serce. A to programowanie funkcyjne? Co to?

Jeśli ktoś studiował informatykę, to mógł tak jak ja odnieść wrażenie, że istnieją dwie równoległe ścieżki: matematyka (z fizyką) i programowanie (z innymi elementami informatyki). Nie było połączenia. Otóż było. Od kilkudziesięciu lat było i było znane, ale nikt nie raczył tym znudzonych studentów zagaić. Programowanie funkcyjne to połączenie między tymi światami. To stan umysłu, w którym nie trzeba pisać testów na każdą jedną linijkę kodu. Można udowodnić poprawność jego działania za pomocą kartki papieru i ołówka, np. przez indukcję.

W programowaniu funkcyjnym można też naprawdę wdrożyć konpcepcje kładzone nam do głowy przez Wujka Boba. Weźmy "nie powtarzaj się". Ile razy pisaliście ten sam fragment kodu? `for (int i = 0; i < koszyk.zakupy.length; ++i)`? Tak, można to inaczej zapisać, bo mamy iteratory, cukier składniowy itp. Ale wciąż są te fragmenty, który wciąż się powtarzają. Ten punkt łączy się z kolejnym - "separacją trosk". Wujek mówił, żeby ciało metody, która możę wygenerować wyjątek wydzielić od obsługi wyjątku. Lub wydzielić ciało z warunku. Lub właśnie z pętli. Programowanie funkcyjne daje nam lepsze narzędzia do tego, by konstruować czysty kod.

### Dlaczego Haskell?
Zainteresowałem się Haskellem, bo jest inny. Dlaczego Ty możesz zainteresować się Haskellem? Bo jest czysto funkcyjny - idealny do nauki, nie można pójść na skróty. Bo ma silne statyczne typowanie i wnioskowanie typów - każde wyrażenie ma konkretną sygnaturę, którą kompilator może wywnioskować samodzielnie. Wreszcie jest skalowalny - jeśli spodoba Ci się ten język, możesz kontynuować jego używanie.

Jednym z założeń Haskella było, by nie tylko stanowił poligon doświadczalny dla naukowców badających języki programowania, ale żeby dało się w nim tworzyć rozwiązania produkcyjne. Używa go np. Facebook. Ponoć można go też spotkać w bankach, gdzie poprawność rozwiązania warto udowodnić formalnie, ale o tym słyszałem tylko pogłoski, bo żaden bank publicznie swojego kodu nie udostępnia.

Jeśli natomiast potrzebujesz autorytetów, spójrz na listę osób podpisanych pod specyfikacją języka. Anegdotą w społecznośći jest, że Haskell jest najlepszą rzeczą, jaka kiedykolwiek wyszła spod rąk jakiegoś komitetu.

### Skąd wziąć Haskella
  Standardem de facto jest GHC - Glasgow Haskell Compiler. To wokół niego toczy się rozwój języka. Od kilku lat panuje niepodzielnie. Źródła Haskella są dwa: oficjalne (Haskell Platform) i lepsze (Stack, zapewniany przez firmę FPComplete, która zaczęła upubliczniać swoje wewnętrzne narzędzia). Użyjemy tego lepszego, bo będzie szybciej.

Instrukcję można znaleźć na stronie https://docs.haskellstack.org/en/stable/README/, gdzie są opisane kroki dla wszystkich popularnych platform.

Pierwszą strefą dyskomfortu będzie niestety IDE. Ponieważ go nie ma. Istnieją pluginy wspierające Haskella dla Eclipse'a czy IDEI, ale proponowałbym prostsze rozwiązanie. Jeśli ktoś korzysta z Emacsa, to może wypróbować upublicznione w maju 2016 https://github.com/commercialhaskell/intero. Dla pozostałych jest Leksah http://leksah.org/, Visual Studio Code https://code.visualstudio.com/ albo Atom https://atom.io/, któ©ego osobiście używam.

Architektura Atoma oparat jest o pluginy, zatem musimy kilka doinstalować, żeby w pełni obsługiwał Haskella.
```
$ apm install autocomplete-haskell haskell-ghc-mod ide-haskell ide-haskell-cabal language-haskell
```
Potrzebne sa jeszcze pakiety haskellowe, których wtyczki Atom używają.
```
$ stack install ghc-mod stylish-haskell hlint
```

Teraz możemy założyć now projekt
```
$ stack new MojProjekt simple
```

I uruchomić edytor.
```
$ stack exec atom
```

Jest to jednak tylko edytor, nie IDE, by skompilować i uruchomić nasz program potrzebne są następujące komendy.
```
$ stack build && stack exec MojProjekt
```

Albo można załadować swój moduł do REPLa (GHCi).
```
$ stack ghci
```

Jeśli nasz moduł nie może akurat zostać skompilowany, przydatne są komendy GHCi.
```
-- by załadować
Prelude> :l src/Main.hs
-- by przeładować
Prelude Main> :r
```

### Minimum składni
W Haskellu bardzo ważne są białe znaki. Jak w Pythonie, ale gdyby ktoś zrobił to dobrze. Przez większość czasu, jeśli piszemy ładnie wyglądający kod, nic nas nie zaskoczy. Najważniejsze jest to, że bloki kodu wydziela się poziomem wcięcia. Ważna jest też wielkość liter. To, co w Javie jest konwencją, tu jest częścią składni, czyli funkcje (już nie metody) zaczynają się od małej litery, natomiast nazwy typów wielką literą. Do tego jest kilka dodatkowych zasad, które razem składają się na spójny system.

Hello world
```haskell
main :: IO ()
main = putStrLn "Hello, world!"
```
Tylko druga z tych linii jest konieczna, nagłowek typu mógł zostać wywnioskowany przez kompilator. W dobrym tonie jest jednak dać jawne typy wszystkim funkcjom o globalnej widoczności. Globalnej w zakresie modułu, o modułach opowiemy kiedy indziej.

Funkcja nazywa się `main`, nie przyjmuje żadnych parametrów. Ciało funkcji znajduje się po prawej stronie znaku równości. W typ wypadku korzystamy z bibliotecznej funkcji `putStrLn :: String -> IO ()`. Funkcja ta przyjmie ciąg znaków i wzróci `()`, czyli "unit" albo "nic ciekawego", ale typ mówi nam, że będzie miał miejsce efekt uboczny.

### Rekurencja
### Listy
### Mapowanie
### Filtrowanie
### Złożenia
