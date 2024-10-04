## W tym zadaniu należy zaimplementować nową wersję języka Macchiato, na którą będą się składały następujące nowe funkcjonalności języka, a także usprawnienia w jego ekosystemie:

## 1. Procedury
Macchiato w wersji 1.1 ma procedury. O procedurach myśleć można jak o funkcjach, które mają zero lub więcej parametrów oraz nie dają żadnej wartości (innymi słowy są typu void). Można je zadeklarować, a następnie w ciągu instrukcji wywołać z odpowiednimi argumentami.

#### Deklaracja procedury zawiera:

* nagłówek, czyli informacje o nazwie procedury i jej parametrach (wszystkie są typu całkowitego). Nazwa procedury jest niepustym ciągiem liter alfabetu angielskiego od 'a' do 'z', natomiast nazwy parametrów podlegają takim ograniczeniom jak nazwy zmiennych, to znaczy są jednoliterowe.
* Wszystkie nazwy parametrów muszą być różne.
* Parametry są przekazywane przez wartość.
* Deklaracje procedur działają w sposób podobny do deklaracji zmiennych, to jest:
* znajdują się w bloku w tym samym miejscu, co deklaracje zmiennych, to znaczy na początku bloku w ciągu deklaracji zmiennych i procedur,
* są widoczne do końca swojego bloku,
* mogą być przesłaniane,
* w tym samym bloku nie można zadeklarować dwa razy procedury o tej samej nazwie,
* treść procedury, która składa się z ciągu deklaracji, po których następuje ciąg instrukcji wykonywanych w momencie wywołania procedury.

#### Wywołanie procedury jest instrukcją, która zawiera:

* nazwę procedury,
* argumenty, będące wyrażeniami języka Macchiato.
* Argumenty wyliczane są w momencie wywołania procedury (w kolejności w jakiej zostały zapisane, tj. od pierwszego do ostatniego) i w jej treści dostępne są jako wartości zmiennych odpowiadających parametrom procedury. Wywołanie procedury skutkuje wykonaniem po kolei instrukcji z jej treści, przy czym dzieje się to w sposób zgodny z dynamicznym wiązaniem zmiennych. To znaczy, że jeśli w treści procedury występuje odwołanie do zmiennej, to ustalanie, o którą zmienną chodzi (jeśli jest w programie więcej niż jedna o tej samej nazwie i czy w ogóle taka zmienna jest dostępna) dzieje się to w trakcie wykonania procedury. Wykorzystywana jest zmienna widoczna obecnie w środowisku wykonania procedury. Uwaga: dynamiczne wiązanie zmiennych jest łatwiejsze do zaimplementowania (i dlatego wybrano je w Macchiato), ale praktycznie nie jest stosowane. W językach takich jak C, Java czy Python występuje statyczne wiązanie zmiennych.

## 2. Nowe polecenie debuggera
Debugger dla Macchiato w wersji 1.1 zawiera wsparcie dla nowego polecenia dump, umożliwiającego wykonanie zrzutu pamięci programu do pliku. Polecenie to ma symbol m i wymaga jednego parametru będącego ścieżką do pliku. Efektem działania polecenia powinno być zapisanie w podanym pliku w formie tekstowej zrzutu pamięci programu. Na zrzut pamięci programu składają się:
widoczne deklaracje procedur, to jest ich nazwy wraz z nazwami parametrów (bez treści),
bieżące wartościowanie zmiennych (takie jak w poleceniu d 0).
## 3. Wygodne tworzenie programów Macchiato w Javie
Programy Macchiato w wersji 1.1 mogą być tworzone w sposób dużo wygodniejszy niż w poprzedniej wersji. Zestaw klas będących niewielkim SDK dla Macchiato zapewnia możliwość tworzenia programów oraz ich poszczególnych części w sposób podobny do DSL, pozwalający na dodawanie po kolei poszczególnych deklaracji i instrukcji dzięki wywołaniom odpowiednich metod (zob. wzorzec projektowy "budowniczy"), a także tworzenie wyrażeń za pomocą czytelnych, statycznych funkcji (zob. wzorzec projektowy "fabryka"). Stworzenie programu o następującej meta składni:

begin block
    var x 101
    var y 1
    proc out(a)
        print a+x
    end proc
    x := x - y
    out(x)
    out(100)  // tu powinno wypisać 200
    begin block
        var x 10
        out(100) // tu statycznie wciąż 200, dynamicznie 110
    end block
end block
mogłoby przebiegać na przykład tak:

var program = new ProgramBuilder()
    .declareVariable('x', Constant.of(101))
    .declareVariable('y', Constant.of(1))
    .declareProcedure('out', List.of('a'), new BlockBuilder()
         .print(Addition.of(Variable.named('a'), Variable.named('x')))
         .build()
    )  
    .assign('x', Subtraction.of(Variable.named('x'), Variable.named('y')))
    .invoke('out', List.of(Variable.named('x')))
    .invoke('out', List.of(Constant.of(100)))
    .block(new BlockBuilder()
        .declareVariable('x', Constant.of(10))
        .invoke('out', List.of(Constant.of(100)))
        .build() 
    )
    .build();
## Testy
Projekt z rozwiązaniem należy uzupełnić o testy JUnit. Każda konstrukcja składniowa języka Macchiato 1.1 powinna być uzupełniona jednym testem.

## Forma rozwiązania zadania
Zadanie polega na zaimplementowaniu Macchiato w wersji 1.1 zgodnie z podaną specyfikacją. Rozwiązanie powinno być w formie projektu o nazwie po_macchiato stworzonego na wydziałowym Gitlabie. Należy zacząć od utworzenia pustego projektu i umieszczenia w nim swojego rozwiązania pierwszej części zadania, w formie pierwszego commita na gałęzi master. Przy pracy nad rozwiązaniem drugiej części należy przestrzegać dobrych praktyk związanych z systemem kontroli wersji dotyczących na przykład nazw commitów, a także ich struktury (na potrzeby tego zadania uznaje się, że powinno być widać pracę w formie mniejszych commitów, a nie jeden ogromny commit z całym rozwiązaniem drugiej części zadania). Co więcej, należy korzystać z gałęzi (tzw. feature branch) podczas dodawania poszczególnych funkcjonalności.


