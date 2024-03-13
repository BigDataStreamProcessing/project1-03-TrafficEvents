# Charakterystyka danych
Dane prezentują pomiary uzyskiwane przy użyciu fotoradarów ustawionych w różnych miejscach w Polsce. 

W strumieniu pojawiają się zdarzenia zgodne ze schematem `TrafficEvent`.

```
create json schema TrafficEvent(car string, manufacturer string, city string, 
car_owner string, velocity int, fine int, penalty_points int, ets string, its string);
```

Zdarzenie w strumieniu reprezentuje pojedynczy pomiar prędkości. 

Dane poza samą prędkością zawierają informacje na temat pojazdu, miejsca wykonania pomiaru, właściciela, naliczonego mandatu i punktów karnych.

Dane są uzupełnione o dwie etykiety czasowe. 
* Pierwsza (`ets`) związana jest z momentem wykonania pomiaru prędkości. 
  Etykieta ta może się losowo spóźniać w stosunku do czasu systemowego maksymalnie do 60 sekund.
* Druga (`its`) związana jest z momentem rejestracji zdarzenia zdobycia punktów w systemie.

# Opis atrybutów

Atrybuty w każdym zdarzeniu zgodnym ze schematem `TrafficEvent` mają następujące znaczenie:

* `car` - nazwa marki samochodu
* `manufacturer` - nazwa producenta danego samochodu
* `car_owner` - imię i nazwisko właściciela pojazdu
* `city` - miasto, w którym wykonano pomiar (zdjęcie) prędkości pojazdu
* `velocity` - prędkość pojazdu w punkcie pomiarowym
* `fine` - kara pieniężna za przekroczenie prędkości (w PLN)
* `penalty_points` - punkty karne za złamanie przepisu ruchu drogowego 
* `ets` - czas rejestracji zdarzenia (pomiaru prędkości wraz z identyfikacją pojazdu)
* `its` - czas rejestracji zdarzenia związanego z pomiarem prędkości w systemie

# Zadania
Opracuj rozwiązania poniższych zadań. 
* Opieraj się strumieniu zdarzeń zgodnych ze schematem `TrafficEvent`
* W każdym rozwiązaniu możesz skorzystać z jednego lub kilku poleceń EPL.
* Ostatnie polecenie będące ostatecznym rozwiązaniem zadania musi 
  * być poleceniem `select` 
  * posiadającym etykietę `answer`, przykładowo:
  ```
    @name('answer') SELECT car, city, sum(penalty_points) as sumPoints, count(*) howMany, ets, its
    FROM TrafficEvent#ext_timed(java.sql.Timestamp.valueOf(its).getTime(), 3 sec)
  ```

## Zadanie 1
Utrzymuj informacje o maksymalnych zarejestrowanych prędkościach
w poszczególnych miastach w ciągu ostatnich 5 minut.

Wyniki powinny zawierać, następujące kolumny:
- `city` - nazwę miasta
- `max_velocity` - maksymalną zarejestrowaną prędkość w ciągu ostatnich 5 minut.

## Zadanie 2
Wykrywaj przypadki przekroczenia prędkości 100 km/h przez danego kierowcę w danym aucie.

Wyniki powinny zawierać, następujące kolumny:
- `car_owner` - imię i nazwisko właściciela pojazdu
- `manufacturer` - nazwę producenta danego samochodu
- `car` - nazwę marki danego samochodu
- `velocity` - wartość zmierzonej prędkości danego kierowcy.

## Zadanie 3
Wykrywaj przypadki zdobycia przez właścicieli pojazdów sumarycznej liczby punktów karnych większej
lub równej 24 liczonych od początku działania systemu.

Wyniki powinny zawierać, następujące kolumny:
- `car_owner` - imię i nazwisko właściciela pojazdu
- `penalty_points_sum` - dokładną sumaryczną liczbę zdobytych punktów karnych.

## Zadanie 4
Dla każdego miasta, dla każdej kolejnej minuty, utrzymywane są informacje o 5 najwyższych pomiarach prędkości. Znajduj osoby, które pojawiły się na powyższych listach co najmniej w dwóch różnych miastach.

Wyniki powinny zawierać, następujące kolumny:
- `owner` - nazwę osoby
- `city1` - nazwa miasta, w którym zdarzenie zostało zarejestrowane wcześniej
- `city2` - nazwa miasta, w którym zdarzenie zostało zarejestrowane później
- `its1` - czas zdarzenia, które zostało zarejestrowane wcześniej.


## Zadanie 5
Znajduj serie co najmniej trzech pomiarów w mieście Poznań, w których zmierzona wartość prędkości nie spadała poniżej 100 km/h. Dla każdej znalezionej serii pobieraj jej początek oraz właścicieli pojazdów z dwóch pierwszych pomiarów. 

Zadbaj o to, aby wykrywane serie się nie nakładały.

Wyniki powinny zawierać, następujące kolumny:
- `owner1` - imię i nazwisko pierwszego właściciela pojazdu
- `owner2` - imię i nazwisko drugiego właściciela pojazdu
- `ets1` - czas rozpoczęcia wykrytej serii.

## Zadanie 6
Znajduj serię następujących po sobie trzech pomiarów prędkości dotyczące takiej samej marki samochodu, gdzie prędkość dla każdego pomiaru jest większa od pomiaru poprzedniego. Wykluczaj przypadki, w których pomiędzy pierwszym a ostatnim zdarzeniem w serii zanotowano pomiar z prędkością poniżej 10 km/h. 

Wyniki powinny zawierać, następujące kolumny:
- `car` - nazwę danej marki pojazdu
- `st_velocity` - pierwsza wartość prędkości
- `mid_velocity` - druga wartość prędkości
- `high_velocity` - trzecia wartość prędkości
- `st_its` - data pierwszego pomiaru.

## Zadanie 7
Dla każdego miasta wykrywaj serie następujących po sobie co najmniej trzech pomiarów prędkości, gdzie prędkość dla każdego kolejnego pomiaru ma być nie mniejsza od pomiaru poprzedniego. Serie te powinny kończyć się pomiarem, w którym prędkość wreszcie zmalała w stosunku do pomiaru poprzedniego. 

Wyniki powinny zawierać, następujące kolumny:
- `city` - nazwę miasta
- `start_velocity` - startową wartość prędkości serii
- `high_velocity` - końcowa wartość prędkości w serii
- `avg_velocity` - średnia wartość prędkości w serii
- `how_many` - liczbę pomiarów w serii
- `start_ets` - czas rejestracji pierwszego pomiaru w serii
- `end_ets` - czas rejestracji ostatniego pomiaru w serii
