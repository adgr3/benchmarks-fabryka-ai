# WSC273: Slayer 1.06 na T14

> Praca idzie według [`METODOLOGIA.md`](../../METODOLOGIA.md). Etap B to odtworzenie opublikowanego wyniku
> (`REPRODUCTION.md`). Kartę oryginału (`ORIGINAL.md`) dopisuje osobne zadanie po terminie testów Space.
> Pojęcia (Space, T01–T50, panel 200, SE, werdykt): [słowniczek](../../METODOLOGIA.md#słowniczek).

WSC273 to 273 pytania Winograd Schema Challenge. Model wybiera, do kogo odnosi się zaimek, między dwiema opcjami. <!-- liczba-spoza-wynikow: rozmiar splitu z karty zadania -->
lm-eval podstawia każdą opcję w miejsce zaimka i porównuje log-prawdopodobieństwo wspólnej kontynuacji. Metryka
decyzyjna to trafność (`acc`), a pytania są 0-shot.

- Protokół zapisany przed pomiarem: [`PROTOCOL-space.md`](PROTOCOL-space.md)
- Odtworzenie (etap B): [`REPRODUCTION.md`](REPRODUCTION.md)
- Przepis (commity, modele, komendy, sprzęt): [`RECIPE.md`](RECIPE.md)
- Liczby: [`results/wsc273.json`](results/wsc273.json)

## Wyniki

| model | próbka | trafność | n | SE | CI95 (bootstrap) |
|---|---|---:|---:|---:|---|
| Slayer 1.06 | panel 200 | 0,605 | 200 | 0,0347 | 0,535-0,675 |
| Slayer 1.06 | pełny split | 0,590 | 273 | 0,0298 | 0,527-0,648 |
| `bloom-560m` (kontrola) | pełny split | 0,623 | 273 | 0,0294 | 0,564-0,681 |

Bazy:

| próbka | losowa | większościowa | udział najczęstszej predykcji Slayera | stała predykcja |
|---|---:|---:|---:|---|
| panel 200 | 0,500 | 0,520 | 0,555 | nie |
| pełny split | 0,500 | 0,502 | 0,535 | nie |

Slayer 1.06 wobec baz:

| próbka | baza | różnica | SE | test | werdykt |
|---|---|---:|---:|---|---|
| panel 200 | losowa | +10,5 pkt | 0,0347 | z = 3,03 | pewne |
| panel 200 | większościowa | +8,5 pkt | 0,0347 | z = 2,45 | prawdopodobne |
| pełny split | losowa | +9,0 pkt | 0,0298 | z = 3,01 | pewne |
| pełny split | większościowa | +8,8 pkt | 0,0298 | z = 2,95 | prawdopodobne |

Slayer 1.06 jest nad bazami, ale tylko o ok. 3 SE. Dwa z czterech werdyktów to „pewne” tuż nad progiem |z| ≥ 3, <!-- ocena-bez-se: opis progu werdyktu, SE w tabeli powyżej -->
więc ta odległość nie jest bezpieczna.

Przebieg próbki zgadza się z podzbiorem przebiegu pełnego: największa różnica logliku to 0,0, liczba różnych `acc` to 0. <!-- liczba-spoza-wynikow: zgodność przebiegów z results/wsc273.json -->
Najdłuższe żądanie ma 48 tokenów, więc nic nie zostało obcięte. <!-- liczba-spoza-wynikow: wyjście evaluate -->

## Wrażliwość na tożsamość dokumentu

WSC273 nie ma identyfikatora, a `METHOD.md` nie publikuje postaci kanonicznej dokumentu. Próbka Space może więc być
inna niż nasza. Wartości tej samej reguły próbki przy trzech rozsądnych tożsamościach, jako podzbiory przebiegu
pełnego:

| tożsamość dokumentu | trafność | n | SE |
|---|---:|---:|---:|
| dokument po `process_docs` (wariant główny) | 0,605 | 200 | 0,0347 |
| dokument przed `process_docs` | 0,570 | 200 | 0,0351 |
| indeks wiersza | 0,565 | 200 | 0,0351 |

Rozstęp to 4,0 pkt, czyli sam wybór tożsamości daje tyle co różnica rzędu jednego SE próbki. Różnice nie mają <!-- ocena-bez-se: opis rozstępu, SE w tabeli powyżej -->
osobnego testu, bo próbki w większości się pokrywają. Wynik pełnego splitu od tego wyboru nie zależy.
