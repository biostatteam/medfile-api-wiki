# 11. Zdarzenia Medyczne

## Wstęp

Raportowanie Zdarzeń Medycznych to jedna z funkcji udostępnianych przez P1. Raporty są podzielone na tzw. zasoby (Resources). Zasoby są przesyłane w formacie HL7 Fhir - https://hl7.org/fhir/

Pełna dokumentacja opisująca struktury zasobów znajduje się w dokumentacji integracyjnej od P1 na isus.ezdrowie.gov.pl w pliku o nazwie P1-DS-Dokumentacja-integracyjna-Systemu-P1-w-zakresie-obsługi-ZM v15.1 - gdzie v15.1 to numer obowiązującej wersji, która zmienia się raz na kilka tygodni (dość często).

Wersjonowanie w P1 polega na tym, że każda zmiana w zasobie wymaga wysłania nowego dokumentu XML zawierającego wszystkie informacje. Dokument po wysłaniu do P1 staje się wersją aktualną z numerem równym `poprzedni numer + 1`.

### Zasoby dostępne w P1

- Encounter - podstawowe dane Zdarzenia Medycznego
- Patient - dane osobowe Pacjenta
- Procedure - dane dotyczące wykonanych procedur medycznych (ICD-9)
- Condition - dane dotyczące rozpoznań (ICD-10)
- Provenance - zasób do potwierdzania autentyczności danych (używany w wybranych przypadkach)
- DocumentReference - powstaje po wysłaniu plików do Indeksu EDM (inna usługa)
- Organization - dane organizacji/podmiotu bazuje na rejestrze rpwdl.csioz.gov.pl
- Observation - dane dotyczące cech Pacjenta
- Device - dane urządzeń i implantów Pacjenta
- Coverage - opisuje uprawnienia Pacjenta (NFZ)
- Claim - dane dotyczące pozycji rozliczeniowych (NFZ)
- Immunization - dane nt. szczepień Pacjenta
- AllergyIntolerance - alergie Pacjenta

### Pobieranie zasobu istniejącego w P1

```
GET /zm/{zasób}/{id<\d+>}/{_history([/]\d+)?}
```

- {zasób} - to np. Patient
- {id} - to identyfikator nadany przez P1 - możemy o otrzymać tworząc nowy zasób lub przez przeszukiwanie P1 (listowanie)
- {_history} - to parametr opcjonalny do pobieranie starszej wersji zasobu

### Konwertowanie zasobu do HTML (np. do wydruku)

```
GET /zm/{zasób}/{id<\d+>}/{_history([/]\d+)?}
Accept: text/html
```

- aby skonwertować zasób do HTML (z XML źródłowego) należy w nagłówu Accept wskazać format `text/html`

### Szukanie zasobów w P1 (listowanie)

```
GET /zm/{zasób}/?{parametry}
```

- {zasób} - to np. Patient
- {parametry} - to lista parametrów typu GET (query params) do przeszukiwania P1, każdy zasób ma inne parametry i są one opisane dokładnie w dokumentacji P1, nie umieszczamy ich tutaj ponieważ zmieniają się bardzo często i lepszym źródłem informacji jest dokument źródłowy.

#### Przykład wyszukiwania pacjenta po numerze PESEL, imieniu i nazwisku:

```
GET /zm/Patient/?plpatient=urn:oid:2.16.840.1.113883.3.4424.1.1.616|40010151673&plgiven=Sylwester&plfamily=Senior
```

- 2.16.840.1.113883.3.4424.1.1.616 - to OID reprezentujacy numer PESEL (krajowy identyfikator dla Polski). Każdy identyfikator taki jak paszport, dowód osobisty, karta żeglarska ma inny OID w zależności od kraju.

### Tworzenie nowych zasobów w P1

```
POST /zm/{zasób}

{XML}
```

- {zasób} - to np. Patient
- {XML} W body żądania należy przesłać plik XML z zasobem w formacie FHIR. Alternatywnie planujemy udostępnić opcję wysyłania danych w formacie Json zgodnym z FHIR - przykłady są w trakcie budowy.

### Aktualizacja istniejącego zasobu w P1

```
PUT /zm/{zasób}/{id}

{XML}
```

- {zasób} - to np. Patient
- {id} - to identyfikator wewnętrzny w P1 nadany temu zasobowi przez P1
- {XML} W body żądania należy przesłać plik XML z zasobem w formacie FHIR.

### Pobieranie rejestru OID dla identyfikatów pacjentów

```
GET /zm/dictionary/personalid/{kraj}/{typ}
```

- {kraj} - nazwa kraju - opcjonalna (tekst np. Polska)
- {typ} - typ identyfikator - opcjonalny (wartość 0-5)

### Usuwanie zasobów w P1

```
DELETE /zm/{zasób}/{id<\d+>}
```

- {zasób} - to np. Patient
- {id} - to identyfikator wewnętrzny w P1 nadany temu zasobowi przez P1

### Pobieranie innych słówników potrzebnych w ZM

```
GET /zm/dictionary/p1/{typ}
```

- {typ} - typ słownika - opcjonalny (tekst np. PLMEDICALEVENTADMITSOURCE)


