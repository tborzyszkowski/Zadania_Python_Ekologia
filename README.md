# Zadanie: Ekologia

| Termin oddania | Punkty     |
|----------------|:-----------|
|  29.05.2026 23:00 |   25        |

---

Przekroczenie terminu o **n** zajęć wiąże się z karą:
- punkty uzyskania za realizację zadania są dzielone przez **2<sup>n</sup>**.

---

W katalogu `begin` znajduje się definicja świata, w którym rządzą następujące zasady:
* świat jest płaski i posiada wysokość i szerokość
* każdy organizm na świecie posiada:
    * `power`: zwiększa się co jedną turę o 1; decyduje o sile organizmu
    * `initiative`: priorytet decyduje o kolejności wykonania ruchu w ramach jednej tury
    * `position`: położenie w świecie
    * `liveLength`: liczba tur do końca życia
    * `powerToReproduce`: granica dolna siły, powyżej której może się rozmnażać; po rozmnożeniu traci połowę siły
    * `sign`: znak reprezentujący organizm w świecie
    * `world`: świat, w którym żyje organizm
* jedynymi organizmami żyjącymi na świecie jest trawa i owca.

---

## Wymagania jakościowe [obowiązkowe]

Poniższe wymagania dotyczą całości oddawanego kodu - zarówno kodu istniejącego, jak i każdej nowej funkcjonalności. Ich nieprzestrzeganie skutkuje odliczeniem punktów.

### Clean Code

Kod powinien być czytelny i samodokumentujący się. Wymagania:

* **Nazwy mówią wprost, co oznaczają** — zmienne, metody i klasy powinny mieć pełne, opisowe nazwy w jednym języku (angielskim). Przykłady błędów w kodzie startowym do poprawy:
    * `pomOrganism`, `pomPosition`, `pomPositions` → np. `candidateOrganism`, `candidatePosition`, `candidatePositions`
    * `atackingOrganism` → `attackingOrganism` (literówka)
* **Brak magicznych liczb** — stałe liczbowe (np. wartości parametrów organizmów) powinny być wyrażone jako nazwane stałe klasy, np.:
    ```python
    INITIAL_POWER = 3
    INITIAL_INITIATIVE = 3
    ```
* **Każda metoda robi dokładnie jedną rzecz** — jeśli metoda robi więcej, należy ją rozbić.
* **Metody nie przekraczają ~20 linii** — długa metoda to sygnał, że warto ją podzielić.
* **Brak martwego kodu** — żadnych zakomentowanych fragmentów, nieużywanych zmiennych ani importów.

### Separation of Concerns

Każda klasa powinna mieć jedną, jasno określoną odpowiedzialność. W kodzie startowym klasa `World` łączy kilka różnych odpowiedzialności — należy je rozdzielić:

* **`WorldRenderer`** — odpowiada wyłącznie za wizualizację świata (metoda `__str__` i cała logika renderowania planszy powinna trafić do tej klasy).
* **`TurnManager`** — odpowiada za logikę jednej tury: kolejkowanie akcji, ich wykonanie oraz aktualizację stanu organizmów. Metoda `makeTurn` oraz `makeMove` powinny być przeniesione i ewentualnie rozbite na mniejsze, prywatne metody.
* **`World`** — po refaktoryzacji odpowiada wyłącznie za przechowywanie stanu świata (lista organizmów, wymiary) i udostępnianie operacji na pozycjach (`positionOnBoard`, `getNeighboringPositions`, itd.).

Każda z nowych klas powinna być objęta testami jednostkowymi weryfikującymi jej izolowaną odpowiedzialność.

---

## Ryś [4 pkt]
Bazując na definicji zwierzęcia dodać rysia, który posiada następujące atrybuty:
* `INITIAL_POWER = 6`
* `INITIAL_INITIATIVE = 5`
* `INITIAL_LIVE_LENGTH = 18`
* `INITIAL_POWER_TO_REPRODUCE = 14`
* `SIGN = 'R'`

Ryś podczas ruchu wybiera sąsiednie pole z pominięciem pól zajętych przez inne rysie.

## Antylopa [4 pkt]
Dodać antylopę, która zachowuje się jak owca, z tym, że jeżeli w jej otoczeniu pojawi się ryś, to ucieka od niego o dwa pola (kierunek odwrotny do występowania rysia). Jeżeli ucieczka nie jest możliwa, atakuje rysia.
* `INITIAL_POWER = 4`
* `INITIAL_INITIATIVE = 3`
* `INITIAL_LIVE_LENGTH = 11`
* `INITIAL_POWER_TO_REPRODUCE = 5`
* `SIGN = 'A'`

## Plaga [4 pkt]
Dodaj możliwość włączenia trybu plagi, który powoduje skrócenie życia wszystkich organizmów o połowę. Plaga działa tylko dwie tury i nie powoduje zmian w dalszych pokoleniach organizmów.

## Dodanie organizmu [4 pkt]
Zaimplementować możliwość dodania po dowolnej turze dowolnego nowego organizmu na dowolne wolne pole.

## Ewolucja ekologiczna [4 pkt]

Żaden gatunek nie powinien wyginąć — świat dąży do zachowania bioróżnorodności.

Zasady:
* Świat śledzi populację każdego gatunku osobno (na podstawie klasy organizmu).
* Jeżeli liczba osobników danego gatunku **spadnie do jednego**, jedyny ocalały osobnik otrzymuje **bonus rozrodczy**: jego `powerToReproduce` zostaje tymczasowo zmniejszone o połowę na **trzy tury**, po czym wraca do wartości wyjściowej.
* Jeżeli gatunek **całkowicie wymrze** (liczba osobników = 0), w następnej turze na losowym wolnym polu **samoistnie pojawia się jeden nowy osobnik** tego gatunku z domyślnymi parametrami.
* Mechanizm działa dla wszystkich gatunków z wyjątkiem kosmity (patrz niżej).

Wymagania implementacyjne:
* Logika śledzenia populacji i wyzwalania ochrony gatunkowej powinna być wydzielona do osobnej klasy (np. `SpeciesGuard`) zgodnie z zasadą _Separation of Concerns_.
* Do każdego scenariusza ochrony napisać testy jednostkowe.

## Kosmita [5 pkt]

Dodać nowy typ organizmu — **kosmitę** — który nie jest ani rośliną, ani zwierzęciem. Kosmita dziedziczy bezpośrednio po `Organism`.

Cechy kosmity:
* `INITIAL_POWER = 0`
* `INITIAL_INITIATIVE = 10`
* `INITIAL_LIVE_LENGTH = 999`
* `SIGN = 'K'`
* Kosmita **nie atakuje, nie ucieka i nie rozmnaża się** — metody `move()` i `action()` zwracają puste listy.
* Kosmita **pojawia się z nikąd**: co określoną liczbę tur (np. co 7 tur) lub z zadanym prawdopodobieństwem na losowym wolnym polu planszy, bez ingerencji gracza.

Efekt zamrożenia czasu:
* Wszystkie pola **bezpośrednio sąsiadujące** z kosmitą (8-sąsiedztwo, Moore'a) są **zamrożone**.
* Organizmy przebywające na zamrożonych polach w danej turze:
    * **nie wykonują ruchu** (`move()` jest pomijane),
    * **nie wykonują akcji** (`action()` jest pomijane),
    * **nie tracą czasu życia** (`liveLength` nie maleje),
    * **nie zyskują siły** (`power` nie rośnie).
* Reszta planszy działa normalnie — czas płynie dla organizmów poza strefą zamrożenia.
* Jeżeli na planszy jest kilka kosmitów, ich strefy zamrożenia się sumują (unia zamrożonych pól).

Wymagania implementacyjne:
* Klasa `Alien` powinna implementować metodę `getFrozenPositions()` zwracającą zbiór (`set`) zamrożonych pozycji.
* `TurnManager` (lub `World.makeTurn`) powinien przed każdą turą zbierać pełny zbiór zamrożonych pozycji ze wszystkich kosmitów i odpowiednio pomijać działania organizmów na tych polach.
* Kosmita **nie podlega** mechanizmowi ochrony gatunkowej z punktu _Ewolucja ekologiczna_.
* Do klasy `Alien` i mechanizmu zamrażania napisać testy jednostkowe.

---

## Uwaga
Do każdej dodawanej nowej funkcjonalności napisać odpowiednie testy jednostkowe.

