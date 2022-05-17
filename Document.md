# Dokumentacja Medfile API

Wersja API: `2.2.0`

Autorzy:
- Szymon Błąkała <sblakala@biostat.com.pl>
- Tomasz Kusy <tkusy@biostat.com.pl>

# Zmiany w 2.2.0 z dnia 28.01.2022

1. Dokumentacja Zdarzeń Medycznych
2. Dokumentacja bazy leków
3. Wprowadzono trasę do tworzenia organizacji
4. Poprawiono przykład z wysyłaniem i weryfikacją erecepty ("erecepta": ...)

# Zmiany w 2.0.3 z dnia 26.11.2020

1. Dla recept i skierowań dodano parametry pacjentów transgranicznych lub nieletnich bez numeru PESEL.

2. Dokumentacja eZLA została przebudowana i zawiera teraz przykładowe odpowiedzi.

3. W dokumentacji eZLA zmodyfikowano punkt 8.1. o instrukcje na temat uruchomienia środowiska testowego.

# Zmiany w 2.0.2 z dnia 12.10.2020

1. W tokenie JWT wymagamy dodatkowego pola organization (uuid) aby obsłużyć np. anulowanie recept oraz pobieranie recept.

2. Nowy kod odpowiedzi: `406` gdy wynik operacji jest błędny np. nie uda się zweryfikować recepty.

3. Anulowanie pakietu recept

4. Drukowanie pakietu recept

5. Pobieranie pakietu recept

6. Określenie kodów odpowiedzi dla wysyłania i weryfikacji recept

# Zmiany w 2.0.1 z dnia 05.10.2020

1. Numeracja sekcji w dokumentacji.

2. Poprawka w obiekcie `telecom` - wymagana składnia to
   ```json
   [{"value":"+48600600600"}]
   ```

3. W obiektach practitioner oraz organization zmiana z liczby mnogiej na pojedynczą listy `identifiers`. Po zmianie składnia wygląda następująco:
    ```json
    "identifier": [
      {
        "type": "rpwdl",
        "value": "000000792087"
      }
    ]
    ```

# 1. Kody odpowiedzi z Medfile API

- `200` - Poprawna odpowiedź
- `400` - Nieprawidłowe żądanie - np. metoda GET gdy wymagany POST
- `401` - Brak autoryzacji - token JWT jest nie tak
- `403` - Brak dostępu do wybranych zasobów
- `404` - Nie znaleziono zasobów np. recepty czy danych lekarza
- `406` - Not Acceptable/Wynik inny od sukcesu w przypadku np. weryfikacji recepty.
- `500` - Gdy wystąpi nieobsłużony problem
- `501` - Gdy funkcjonalność jeszcze nie jest uruchomiona - możemy to zwracać dla `eskierowanie`

# 2. Autoryzacja w Medfile API

## 2.1 JWT Token

Do autoryzacji używamy tokentów JWT o krótkim terminie ważności z podpisem.
Po podpisaniu umowy firma BioStat udostępnia klucze do szyfrowania i weryfikacji tokentów JWT.

Token JWT wymagana następujących pól:
- *iss* - nadany identyfiakator integratora
- *aud* - stała wartość "medfile"
- *iat* - czas (unix timestamp) wygenerowania tokena
- *exp* - czas (unix timestamp) wygaśnięcia tokena (zalecany maks. iat + 60)
- *practitioner* - opcjonalnie (kiedy wymagany) identyfikator lekarza w którego kontekście wywoływana jest akcja
- *organization* - opcjonalnie (kiedy wymagany) identyfikator podmiotu w którego kontekście wywoływana jest akcja

Ponadto:

- pole *sub* nie powinno być używane (zarezerwowane do przyszłych zastosowań)

## 2.2 Przekazywanie tokenu do API

Token przekazujemy w każdym request wysyłanym do Medfile API jako nagłówek HTTP.

Tj.

```
Authorization: Bearer JWT_TOKEN
```

## 2.3 Nieprawidłowa autoryzacja

Nieprawidłowo podpisany token będzie skutkował odpowiedzą HTTP: `401` - Brak autoryzacji - token JWT jest nie tak

# 3. Organizacja - podmiot lub praktyka

Organizacją nazywamy dowolny podmiot medyczny lub praktykę lekarską.

Do pracy z Medfile API wymagane jest utworzenie przynajmniej jednej organizacji która będzie używana we właściwych usługach w tym np. erecepta.

## 3.1. Tworzenie nowej organizacji

PUT /organization/

```json
{
  "name": "BioStat Sp. z o.o.",
  "identifier": [
    {
      "type": "rpwdl",
      "value": "000000792087"
    },
    { // Tylko dla jednostki organizacyjnej podmiotu
      "type": "rpwdlUnit",
      "value": "01"
    },
    { // Tylko dla komórki organizacyjnej podmiotu
      "type": "rpwdlCell",
      "value": "001"
    },
    { // Tylko dla komórki organizacyjnej podmiotu
      "type": "cellName",
      "value": "Ogólna izba przyjęć"
    },
    { // Tylko dla praktyki lekarskiej
      "type": "medicalChamber",
      "value": "68"
    },
    { // Tylko dla praktyki lekarskiej - miejsce wykonywania działalności
      "type": "praxisCode",
      "value": "001"
    },
    {
      "type": "regon",
      "value": "24154444300004"
    },
    {
      "type": "nip",
      "value": "6391001234"
    },
    {
      "type": "oidRoot",
      "value": "2.16.840.1.113883.3.4424.2.7.67"
    },
    { // Specjalizacja organizacji
      "type": "speciality",
      "value": "Poradnia neurologiczna"
    },
    { // Specjalizacja organizacji (VIII cz. kodu resortowego)
      "type": "specialityCode",
      "value": "1220"
    }
  ],
  "telecom": [
    {
      "value": "+48131231230"
    }
  ],
  "address": [
    {
      "street": "ul. Dubois",
      "city": "Warszawa",
      "postalCode": "00-184",
      "country": "pl",
      "houseNumber": "5A",
      "unitId": "13"
    }
  ],
  "nfzDepartment": "07", // opcjonalny oddział NFZ z którym organizacja zawarła umowę
  "nfzContract": "01-06-02336-20-03/06", // opcjonalny numer umowy z NFZ
  "services": {
    "p1": {
      "tls": [],
      "wss": []
    }
  }
}
```

# 4. Specjalista - użytkownik korzystający z usług w Medfile API

Specjalistą jest każdy użytkownik który będzie korzystał z Medfile API.

Wszystkie usługi będą wymagały do działania specjalisty/lekarza.

Każde wywołanie funkcji API wymaga do działania dokładnie jednego specjalisty. Identyfikator lekarza przekazujemy do API w tokenie JWT pod kluczem `practitioner`.

Identyfikator specjalisty w formacie UUIDv4 otrzymają Państwo przy utworzeniu użytkownika (zapisie).

## 4.1. Tworzenie nowego użytkownika

PUT /practitioner/

### Treść żądania

```json
{
  "identifier": [
    {
      "type": "pesel",
      "value": "600322236"
    },
    {
      "type": "npwz",
      "value": "1044400"
    },
    {
      "type": "regon",
      "value": "24154444300004"
    },
    {
      "type": "nip",
      "value": "6391001234"
    }
  ],
  "name": [
    {
      "family": "Kowalski",
      "given": [
        "Jan",
        "Henryk"
      ]
    }
  ],
  "telecom": [ // wymagane m.in. dla recept PA, PF
    {
      "value": "+48131231230"
    }
  ],
  "address": [
    {
      "street": "ul. Dubois",
      "houseNumber": "5A",
      "unitId": "13",
      "city": "Warszawa",
      "postalCode": "00-184"
    }
  ],
  "qualifications": [ // Wymagane dla e-skierowań
    {
      "code": "0713",
      "display": "medycyna rodzinna"
    },
    {
      "code": "0731",
      "display": "alergologia"
    }
  ],
  "role": "LEK"
}
```

### Odpowiedzi

#### Pozytywna

```json
{
  "uuid": "cd986fde-066d-4f3d-8144-67d44f955656",
  "identifier": [
    {
      "type": "pesel",
      "value": "98030801601"
    },
    {
      "type": "npwz",
      "value": "7689753"
    },
    {
      "type": "regon",
      "value": "784073291"
    },
    {
      "type": "nip",
      "value": "6391001234"
    }
  ],
  "name": [
    {
      "family": "Praktyczny",
      "given": [
        "Artur09"
      ]
    }
  ],
  "telecom": [
    {
      "value": "+48131231230"
    }
  ],
  "address": [
    {
      "street": "ul. Dubois",
      "city": "Warszawa",
      "postalCode": "00-184",
      "country": "pl",
      "houseNumber": "5A",
      "unitId": "13"
    }
  ],
  "qualifications": [
    {
      "code": "0713",
      "display": "medycyna rodzinna"
    },
    {
      "code": "0731",
      "display": "alergologia"
    }
  ],
  "role": "LEK"
}
```

#### Negatywna

```json
{
  "error": [{
    "code": "",
    "text": "Proszę podać nazwisko."
  }]
}
```

## 4.2 Aktualizacja specjalisty

POST /practitioner/{practitioner.uuid}

### Treść żądania

Treść jest identyczna z trasą `PUT /practitioner/`.
Aktualizowane są tylko przekazane informacje.

W celu usunięcia wybranych informacji należy przesłać wartość `null` dla wybranych pól.

Przykład usunięcia numeru PESEL ze specjalisty.
Oraz jednoczesna zmiana jego numeru PWZ.

```json
{
  "identifier": [
    {
      "type": "pesel",
      "value": null
    },
    {
      "type": "npwz",
      "value": "99999999"
    }
  ]
}
```

## 4.3 Usuwanie specjalisty

DELETE /practitioner/{practitioner.uuid}

### Treść żądania

Brak.

### Odpowiedź

#### Pozytywna

```json
{
  "uuid": "f0a5d245-2f7d-4fa5-ad76-04ede898987d",
  "identifier": [
    {
      "type": "pesel",
      "value": "98030801601"
    },
    {
      "type": "npwz",
      "value": "7689753"
    },
    {
      "type": "regon",
      "value": "784073291"
    },
    {
      "type": "nip",
      "value": "6391001234"
    }
  ],
  "name": [
    {
      "family": "Praktyczny",
      "given": [
        "Artur09"
      ]
    }
  ],
  "telecom": [
    {
      "value": "+48131231230"
    }
  ],
  "address": [
    {
      "street": "ul. Dubois",
      "city": "Warszawa",
      "postalCode": "00-184",
      "country": "pl",
      "houseNumber": "5A",
      "unitId": "13"
    }
  ],
  "qualifications": [
    {
      "code": "0713",
      "display": "medycyna rodzinna"
    },
    {
      "code": "0731",
      "display": "alergologia"
    }
  ],
  "role": "LEK"
}
```

#### Negatywna

```json
{
  "error": [
    {
      "code": "",
      "text": "Specjalista nie istnieje."
    }
  ]
}
```

# 5. Konfiguracja usług

## 5.1. POST /services/p1/{organizationUuid}

Certyfikaty WSS i TLS do komunikacji z P1 (dla praktyki lub podmiotu)

```json
{
  "tls": {
    "certificate": "__base64 encoded p12 file__",
    "password": "p4ssw0rd!"
  },
  "wss": {
    "certificate": "__base64 encoded p12 file__",
    "password": "p4ssw0rd!"
  }
}
```

## 5.2. POST /services/ewus/{practitionerUuid}

Ustawienia eWUŚ dla lekarza

```json
{
  "domain": "01",
  "operatorType": "doctor",
  "providerId": "abc123",
  "doctorId": "abc321",
  "username": "john",
  "password": "p4ssw0rd!"
}
```

## 5.3. POST /services/zus/{practitionerUuid}

Ustawienia ZUS dla lekarza

```json
{
  "certificate": "__base64 encoded pfx file__",
  "password": "p4ssw0rd!"
}
```


# 6. eRecepta

## 6.1. PUT|POST /erecepta/validate

### Nagłowki
- Authorization: Bearer {token}

### Treść

```json
[
  "erecepta": {
    "id": "0000000000000000025326",
    "date": "2020-08-31",
    "type": "prepared", // prepared|recipe
    "organization": "idabc",
    "practitioner": "idxyz",
    "patient": {
      "identifier": [
        {
          "type": "pesel",
          "value": "600322236"
        }
      ],
      "name": [
        {
          "family": "Senior",
          "given": [
              "Sylwester"
          ]
        }
      ],
      "telecom": [
        {
          "value": "+48131231230" // opcjonalny
        }
      ],
      "address": [{
          "street": "Wroc\u0142awska",
          "houseNumber": "11A",
          "unitId": "3", // mieszkanie
          "city": "Zielona G\u00f3ra",
          "postalCode": "00-184",
          "country": "Polska"
      }],
      "nfz": "07", // opcjonalne - jak nie ma to X ?
      "permissions": "IB",
      "permissionsDocument": "Legitymacja nr. 12312321/23"
    },
    "medication": {
      "name": "Apap Noc",
      "code": "100110151", //
      "ean": "05909990960132", //
      "kdlek": "Rp", // Rp lub Rpw
      "payment": "100%", // opcjonalne (domyślnie 100%) R, B, 30%, 50%, 100%

      "package": {
        "quantity": 60.0, // ilość leku w opakowaniu
        "unit": "tabl." // opcjonalne (domyślnie szt.)
      },

      "activeSubstance": [ // opcjonalne
        {
          "name": "Oxycodon", // opcjonalne gdy podano ilość substancji
          "quantity": 0.0004,
          "unit": "g"
        }
      ]
    },

    "kind": "PF", // PA - proauctore, PF - profamiliae, ZW - zwykła (domyślnie)
    "substanceAdminSubstitution": "N", // Nie pozwalaj na zamienniki
    "priorityCode": "UR", // CITO opcjonalne

    "dosageInstruction": "1x1 przed snem", // dawkowanie
    "dispenseRequest": {
        "quantity": 1.0, // ile opakowań/unit wydać
        "unit": "op.", // opcjonalny
        "infoForPerformer": "proszę wymieszać", // opcjonalne
        "validityPeriod": {
          "start": "2020-09-30", // opcjonalnie (domyślnie brak) od kiedy można zrealizować
          "end": "2020-10-30" // opcjonalnie (domyślnie brak) do kiedy można zrealizować (maksymalnie 365 dni od start)
        }
    }
  }
]
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Podmiot lub lekarz nie istnieją w wybranym kontekście
4. `406` - Błąd w wysyłaniu recepty (np. nieprawidłowe dane)

## 6.2. PUT|POST /erecepta/send

### Nagłowki
- Authorization: Bearer {token}

### Treść

```json
[
  "erecepta": {
    "id": "0000000000000000025326",
    "date": "2020-08-31",
    "type": "prepared", // prepared|recipe
    "organization": "idabc",
    "practitioner": "idxyz",
    "patient": {
      "identifier": [
        {
          "type": "pesel",
          "value": "600322236"
        }
      ],
      "name": [
        {
          "family": "Senior",
          "given": [
              "Sylwester"
          ]
        }
      ],
      "telecom": [
        {
          "value": "+48131231230" // opcjonalny
        }
      ],
      "address": [{
          "street": "Wroc\u0142awska",
          "houseNumber": "11A",
          "unitId": "3", // mieszkanie
          "city": "Zielona G\u00f3ra",
          "postalCode": "00-184",
          "country": "Polska"
      }],
      "nfz": "07", // opcjonalne - jak nie ma to X ?
      "permissions": "IB",
      "permissionsDocument": "Legitymacja nr. 12312321/23"
    },
    "medication": {
      "name": "Apap Noc",
      "code": "100110151", // Wymagany, a jeżeli brak to EAN
      "ean": "05909990960132", // Wymagany a jeżeli code
      "kdlek": "Rp", // Rp lub Rpw
      "payment": "100%", // opcjonalne (domyślnie 100%) R, B, 30%, 50%, 100%

      "package": {
        "quantity": 60.0, // ilość leku w opakowaniu
        "unit": "tabl." // opcjonalne (domyślnie szt.)
      },

      "activeSubstance": [ // opcjonalne
        {
          "name": "Oxycodon", // opcjonalne gdy podano ilość substancji
          "quantity": 0.0004,
          "unit": "g"
        }
      ]
    },

    "kind": "PF", // PA - proauctore, PF - profamiliae, ZW - zwykła (domyślnie)
    "substanceAdminSubstitution": "N", // Nie pozwalaj na zamienniki
    "priorityCode": "UR", // CITO opcjonalne

    "dosageInstruction": "1x1 przed snem", // dawkowanie
    "dispenseRequest": {
        "quantity": 1.0, // ile opakowań/unit wydać
        "unit": "op.", // opcjonalny
        "infoForPerformer": "proszę wymieszać", // opcjonalne
        "validityPeriod": {
          "start": "2020-09-30", // opcjonalnie (domyślnie brak) od kiedy można zrealizować
          "end": "2020-10-30" // opcjonalnie (domyślnie brak) do kiedy można zrealizować (maksymalnie 365 dni od start)
        }
    }
  }
]
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Podmiot lub lekarz nie istnieją w wybranym kontekście
4. `406` - Błąd w wysyłaniu recepty (np. nieprawidłowe dane)

## 6.3. Anulowanie e-recepty

Anulowanie całego pakietu recept (od 1 do 5 leków).
<pre>
DELETE /erecepta/{kluczPakietuRecept}[/{<a href="Kontekst.md">kontekst = 'ZW'</a>}]
</pre>

Anulowanie wybranej pozycji recepty. ***(Not implemented yet)***

```
DELETE /erecepta/{kluczPakietuRecept}/{kodPozycjiLekuWPakiecieRecept}[/{kontekst = 'ZW'}]
```

Odpowiedź:

```json
{
  "erecepta": [
      {
          //lek 1
      },
      {
          //lek N ...
      }
  ]
}
```

W treści odpowiedzi znajdzie się bezpośrednia odpowiedź od CSIOZ na każdy z leków we wskazanym pakiecie recept w postaci tablicy.

## 6.4. Pobieranie pakietu recept

<pre>
GET /erecepta/{kluczPakietuRecept}[/{<a href="Kontekst.md">kontekst = 'ZW'</a>}]
</pre>

Odpowiedź:

```json
{
    "erecepta": [
        {

        }
    ]
}
```


## 6.5. Drukowanie pakietu recept

<pre>
GET /erecepta/{kluczPakietuRecept}/{kodPakietuRecept}/print/[/{<a href="Kontekst.md">kontekst = 'ZW'</a>}]
</pre>

Odpowiedź:

```json
{
    "html": "base64 encoded html document..."
}
```


# 7. eWUŚ

1. `domain` - oddział NFZ

    - 01 - Dolnośląski
    - 02 - Kujawsko-Pomorski
    - 03 - Lubelski
    - 04 - Lubuski
    - 05 - Łódzki
    - 06 - Małopolski
    - 07 - Mazowiecki
    - 08 - Opolski
    - 09 - Podkarpacki
    - 10 - Podlaski
    - 11 - Pomorski
    - 12 - Śląski
    - 13 - Świętokrzyski
    - 14 - Warmińsko-Mazurski
    - 15 - Wielkopolski
    - 16 - Zachodniopomorski
    - 17 - Centrala

1. `operatorType` - typ dostępu

    - doctor - Lekarz
    - provider - Świadczeniodawca

1. `providerId` - identyfikator świadczeniodawcy

1. `doctorId` - identyfikator lekarza

1. `username` - nazwa użytkownika

1. `password` - hasło dostępowe dla użytkownika `username`

## 7.1. Przesyłanie danych konfiguracyjnych

Dołączamy blok `ewus` do sekcji "usługi" w danych użytkownika.

```json
{
    // ...
    "services": {
        "ewus": {
            "domain": "01",
            "operatorType": "doctor",
            "providerId": "abc123",
            "doctorId": "abc321",
            "username": "john",
            "password": "p4ssw0rd"
        }
    }
}
```

## 7.2. GET /ewus/check/{pesel}

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": {
    "operation_date": "2020-10-20T20:26:05+0200",
    "operation_id": "L1712M01200000001",
    "system_name": "eWUS",
    "system_version": "test",
    "status": 1,
    "operator_id": "3",
    "operator_domain": "01",
    "operator_external_id": "TEST3",
    "expiration_date": "2020-10-20T23:59:59+0200",
    "insurance_status": 1,
    "prescription_symbol": "DN",
    "patient_pesel": "19323197988",
    "patient_first_name": "ImięTAK",
    "patient_last_name": "NazwiskoTAK",
    "patient_notes": [
      "Pacjent objęty kwarantanną do dnia 03-11-2020"
    ]
  },
  "error": [],
  "raw": "<?xml version='1.0' encoding='UTF-8'?><soapenv:Envelope xmlns:soapenv=\"http:\/\/schemas.xmlsoap.org\/soap\/envelope\/\"><soapenv:Body><ns3:executeServiceReturn xmlns:ns3=\"http:\/\/xml.kamsoft.pl\/ws\/broker\" xmlns:xsi=\"http:\/\/www.w3.org\/2001\/XMLSchema-instance\" xsi:type=\"ns3:ServiceResponse\"><location xmlns=\"http:\/\/xml.kamsoft.pl\/ws\/common\"><namespace>nfz.gov.pl\/ws\/broker\/cwu<\/namespace><localname>checkCWU<\/localname><version>5.0<\/version><\/location><ns3:date>2020-10-20T20:26:05.363+02:00<\/ns3:date><ns3:payload><ns3:textload><ns2:status_cwu_odp xmlns:ns2=\"https:\/\/ewus.nfz.gov.pl\/ws\/broker\/ewus\/status_cwu\/v5\" data_czas_operacji=\"2020-10-20T20:26:05.357+02:00\" id_operacji=\"L1712M01200000001\"><ns2:status_cwu>1<\/ns2:status_cwu><ns2:numer_pesel>19323197988<\/ns2:numer_pesel><ns2:system_nfz nazwa=\"eWUS\" wersja=\"test\" \/><ns2:swiad><ns2:id_swiad>TEST3<\/ns2:id_swiad><ns2:id_ow>01<\/ns2:id_ow><ns2:id_operatora>3<\/ns2:id_operatora><\/ns2:swiad><ns2:pacjent><ns2:data_waznosci_potwierdzenia>2020-10-20+02:00<\/ns2:data_waznosci_potwierdzenia><ns2:status_ubezp ozn_rec=\"DN\">1<\/ns2:status_ubezp><ns2:imie>ImięTAK<\/ns2:imie><ns2:nazwisko>NazwiskoTAK<\/ns2:nazwisko><ns2:informacje_dodatkowe><ns2:informacja kod=\"KWARANTANNA-COVID19\" poziom=\"O\" wartosc=\"Pacjent objęty kwarantanną do dnia 03-11-2020\" \/><\/ns2:informacje_dodatkowe><\/ns2:pacjent><Signature xmlns=\"http:\/\/www.w3.org\/2000\/09\/xmldsig#\"><SignedInfo><CanonicalizationMethod Algorithm=\"http:\/\/www.w3.org\/TR\/2001\/REC-xml-c14n-20010315\" \/><SignatureMethod Algorithm=\"http:\/\/www.w3.org\/2000\/09\/xmldsig#rsa-sha1\" \/><Reference URI=\"\"><Transforms><Transform Algorithm=\"http:\/\/www.w3.org\/2000\/09\/xmldsig#enveloped-signature\" \/><\/Transforms><DigestMethod Algorithm=\"http:\/\/www.w3.org\/2000\/09\/xmldsig#sha1\" \/><DigestValue>\/LRG3hEBgCjz\/VKJUD42STW4Ppc=<\/DigestValue><\/Reference><\/SignedInfo><SignatureValue>LoXGFPtinfK6KfA9eDzN3\/qXv3KxKKJoXRTRkFtuwIiC5Z154CdrT3R3IusE\/ZoVuXbXuyvVZ5qI\nXs5G94bB0lZE+a9Fgec5bplWhnOsVg\/qnJaCG2EQfuahP8vxmtuKes2O1o8cYC1oBpyMTq\/qI1Wa\nWCtDarXPapLulyJ7N+c=<\/SignatureValue><\/Signature><\/ns2:status_cwu_odp><\/ns3:textload><\/ns3:payload><\/ns3:executeServiceReturn><\/soapenv:Body><\/soapenv:Envelope>"
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

# 8. Usługa eZLA / Konfiguracja

1. `certificate` - treść pliku zus.pfx w base64

2. `password` - hasło do certyfikatu zus.pfx

## 8.1 Przesyłanie danych konfiguracyjnych

Dołączamy blok `ezla` do sekcji "usługi" w danych użytkownika.

```json
{
    // ...
    "services": {
        "zus": {
            "certificate": "base64 ...",
            "password": "p4ssw0rd!"
        }
    }
}
```

> __Uwaga__
>
> Na środowisku testowym należy użyć certyfikatu z ZUS należącego do prawdziwego lekarza. Dane które zwróci API i dane które powstaną w API nie będą miały żadnego związku z tym lekarzem i nie spowodują żadnych konsekwencji.

## 8.2 Pobierz dane lekarza
## GET /ezla/doctor/

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": {
    "Imie": "THEO",
    "Nazwisko": "KANIA",
    "Pesel": "44042102357",
    "NumerPrawaWykonywaniaZawodu": "1063324",
    "NazwaOkregowejIzbyLekarskiej": "Prezydium Wojewódzkiej Rady Narodowej w Częstochowie",
    "StatusLekarza": {
      "DataWydaniaDecyzji": "2000-01-01",
      "Status": "A"
    },
    "PosiadanaSpecjalizacja": [
      {
        "KodSpecjalizacji": "282",
        "NazwaSpecjalizacji": "Ortopedia",
        "StopienSpecjalizacji": "2"
      },
      {
        "KodSpecjalizacji": "281",
        "NazwaSpecjalizacji": "Ortopedia",
        "StopienSpecjalizacji": "1"
      }
    ],
    "Adres": [
      {
        "Ulica": "Maków",
        "NrDomu": "53",
        "KodPocztowy": "41400",
        "Miejscowosc": "Mysłowice"
      }
    ],
    "MiejsceWykonywaniaZawodu": [
      {
        "Nazwa": "PRZYCHODNIA TARCZÓWKOWATYMI",
        "NIP": "3962382940",
        "Ulica": "Zdręby",
        "NrDomu": "112",
        "KodPocztowy": "16423",
        "Miejscowosc": "Zdręby"
      }
    ]
  },
  "error": [],
  "raw": {
    "PobierzDaneLekarzaResponse": {
      "DaneLekarza": {
        "Imie": "THEO",
        "Nazwisko": "KANIA",
        "Pesel": "44042102357",
        "NumerPrawaWykonywaniaZawodu": "1063324",
        "NazwaOkregowejIzbyLekarskiej": "Prezydium Wojewódzkiej Rady Narodowej w Częstochowie",
        "StatusLekarza": {
          "DataWydaniaDecyzji": "2000-01-01",
          "Status": "A"
        },
        "PosiadanaSpecjalizacja": [
          {
            "KodSpecjalizacji": "282",
            "NazwaSpecjalizacji": "Ortopedia",
            "StopienSpecjalizacji": "2"
          },
          {
            "KodSpecjalizacji": "281",
            "NazwaSpecjalizacji": "Ortopedia",
            "StopienSpecjalizacji": "1"
          }
        ],
        "Adres": [
          {
            "Ulica": "Maków",
            "NrDomu": "53",
            "KodPocztowy": "41400",
            "Miejscowosc": "Mysłowice"
          }
        ],
        "MiejsceWykonywaniaZawodu": [
          {
            "Nazwa": "PRZYCHODNIA TARCZÓWKOWATYMI",
            "NIP": "3962382940",
            "Ulica": "Zdręby",
            "NrDomu": "112",
            "KodPocztowy": "16423",
            "Miejscowosc": "Zdręby"
          }
        ]
      },
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      }
    }
  }
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych

## 8.3 Pobierz miejsca pracy lekarza
## GET /ezla/doctor/workplace

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": [
    {
      "Nazwa": "PRZYCHODNIA TARCZÓWKOWATYMI",
      "NIP": "3962382940",
      "Ulica": "Zdręby",
      "NrDomu": "112",
      "KodPocztowy": "16423",
      "Miejscowosc": "Zdręby",
      "IdMiejsca": "231"
    }
  ],
  "error": [],
  "raw": {
    "PobierzMiejsceWykonywaniaZawoduResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "MiejsceWykonywaniaZawodu": [
        {
          "Nazwa": "PRZYCHODNIA TARCZÓWKOWATYMI",
          "NIP": "3962382940",
          "Ulica": "Zdręby",
          "NrDomu": "112",
          "KodPocztowy": "16423",
          "Miejscowosc": "Zdręby",
          "IdMiejsca": "231"
        }
      ]
    }
  }
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

## 8.4 Pobierz dane pacjenta
## GET /ezla/patient/{pesel}

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": {
    "Imie": "OLHA",
    "Nazwisko": "GAJEWSKA",
    "DataUrodzenia": "1968-07-18",
    "Adres": [
      {
        "KodPocztowy": "90118",
        "Miejscowosc": "Łódź (Łódź-Śródmieście)",
        "Ulica": "Kilińskiego Jana",
        "NrDomu": "67"
      },
      {
        "KodPocztowy": "28114",
        "Miejscowosc": "Poręba",
        "Ulica": "Poręba",
        "NrDomu": "96D"
      }
    ]
  },
  "error": [],
  "raw": {
    "PobierzDaneUbezpieczonegoResponse": {
      "DaneUbezpieczonego": {
        "Imie": "OLHA",
        "Nazwisko": "GAJEWSKA",
        "DataUrodzenia": "1968-07-18",
        "Adres": [
          {
            "KodPocztowy": "90118",
            "Miejscowosc": "Łódź (Łódź-Śródmieście)",
            "Ulica": "Kilińskiego Jana",
            "NrDomu": "67"
          },
          {
            "KodPocztowy": "28114",
            "Miejscowosc": "Poręba",
            "Ulica": "Poręba",
            "NrDomu": "96D"
          }
        ]
      },
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "PrzekroczonyDziennyLimitDostepow": false
    }
  }
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

## 8.5. Pobierz członków rodziny pacjenta
## GET /ezla/patient/{pesel}/family

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": [
    {
      "Imie": "TINA",
      "Nazwisko": "SZCZEPAŃSKA",
      "DataUrodzenia": "2000-09-05T00:00:00+00:00"
    }
  ],
  "error": [],
  "raw": {
    "PobierzCzlonkowRodzinyUbezpieczonegoResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "CzlonekRodzinyUbezpieczonego": [
        {
          "Imie": "TINA",
          "Nazwisko": "SZCZEPAŃSKA",
          "DataUrodzenia": "2000-09-05T00:00:00+00:00"
        }
      ]
    }
  }
}

```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

## 8.6. Pobierz płatników pacjenta
## GET /ezla/patient/{pesel}/payers/{issuer}

Gdzie `{issuer}` to:
- ZUS
- KRUS
- INNE_W_POLSCE
- W_INNYM_PANSTWIE

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```json
{
  "message": "",
  "body": [
    {
      "Nazwa": "ALARMOWANO",
      "Imie": "ANDREA",
      "Nazwisko": "KOZŁOWSKA",
      "NIP": "8304421563",
      "Pesel": "68032721861",
      "SeriaNumerPaszportu": "XEV357034",
      "ProfilPUE": false
    }
  ],
  "error": [],
  "raw": {
    "PobierzPlatnikowUbezpieczonegoResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "Platnik": [
        {
          "Nazwa": "ALARMOWANO",
          "Imie": "ANDREA",
          "Nazwisko": "KOZŁOWSKA",
          "NIP": "8304421563",
          "Pesel": "68032721861",
          "SeriaNumerPaszportu": "XEV357034",
          "ProfilPUE": false
        }
      ]
    }
  }
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

## 8.8. Wysyłanie eZLA
## POST /ezla/send
## POST /ezla/validate

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Treść

```json
{
  "institution": "ZUS", // ZUS, KRUS, INNE
  "patient": {
    "identifier": [
      {
        "type": "pesel",
        "value": "40010151673"
      }
    ],
    "name": [
      {
        "family": "Senior",
        "given": [
          "Sylwester"
        ]
      }
    ],
    "telecom": [
      {
        "value": "+48131231230"
      }
    ],
    "gender": "M",
    "birthDate": "1940-01-01",
    "address": [
      {
        "street": "Wroc\u0142awska",
        "houseNumber": "11A",
        "city": "Zielona G\u00f3ra",
        "postalCode": "65-001",
        "country": "Polska"
      }
    ],
    "language": "pl"
  },
  "from": "",
  "to": "",
  "hospitalization": {
    "from": "",
    "to": ""
  },
  "suggestion": "",
  "disease1": "",
  "disease2": "",
  "disease3": "",
  "disease4": "",
  "familyMember": {
    "code": "",
    "date": ""
  },
  "payers": [{
    "type": "",
    "id": ""
  }],
  "organization": {
    "identifier": [
      {
        "type": "nip",
        "value": "6391001234"
      }
    ],
    "name": "BioStat Sp. z o.o.",
    "address": {
      "street": "ul. Dubois",
      "houseNumber": "5A",
      "unitId": "13",
      "city": "Warszawa",
      "postalCode": "00-184"
    }
  },
  "practitioner": {
    "identifier": [
      {
        "type": "npwz",
        "value": "1044400"
      }
    ],
    "name": [
      {
        "family": "Kowalski",
        "given": [
          "Jan",
          "Henryk"
        ]
      }
    ]
  },
  "dateOfDigitalization": "",
  "reasonInPast": "",
  "zoz": false, // bool
  "doNotSend": false, // bool
  "diseaseStatisticalNumber": "A10"
}
```


### Odpowiedź

eZLA składa się z różnych wielu dokumentów.

- 2 dokumenty dla ezla w trybie bieżącym dla jednego płatnika
- 4 dokumenty dla ezla w trybie bieżącym dla 2 płatników
- 8 dokumentów dla ezla w trybie bieżącym i wstecznym dla 2 płatników

Gdzie połowa wszystkich dokumentów to kopie a druga połowa to oryginały.
Każdy dokument jest drukowany osobno i wysyłany osobno do płatników/zus.

```json
{
  "message": "",
  "body": "",
  "error": [],
  "raw": {
    "CustomWalidujDokumentyResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "RezultatWalidacji": [
        {
          "document": 0,
          "content": "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?>\n<KEDU xmlns=\"http:\/\/www.zus.pl\/2015\/KED_ZLA_1\" xmlns:xsi=\"http:\/\/www.w3.org\/2001\/XMLSchema-instance\" wersja_schematu=\"1\">\n  <naglowek.KEDU>\n    <program>\n      <producent>Medfile<\/producent>\n      <symbol>eZLA<\/symbol>\n      <wersja>1.0<\/wersja>\n    <\/program>\n  <\/naglowek.KEDU>\n  <ZUSZLA id_dokumentu=\"155565\" kolejnosc=\"0\">\n    <I>\n      <p1\/>\n      <p2>ORYGINAŁ<\/p2>\n    <\/I>\n    <II>\n      <p1>68071892605<\/p1>\n      <p2>Filip<\/p2>\n      <p3>Jakubowski<\/p3>\n      <p4>1<\/p4>\n    <\/II>\n    <III>\n      <p1>25624<\/p1>\n      <p2>Kielce<\/p2>\n      <p3>Mieszka I<\/p3>\n      <p4>1<\/p4>\n    <\/III>\n    <IV>\n      <p1>\n        <p1>2020-11-27<\/p1>\n        <p2>2020-11-27<\/p2>\n      <\/p1>\n      <p3>2<\/p3>\n      <p8>K02<\/p8>\n    <\/IV>\n    <V>\n      <p1>1<\/p1>\n      <p2>6707035254<\/p2>\n    <\/V>\n    <VI>\n      <p1>PRZYCHODNIA TARCZÓWKOWATYMI<\/p1>\n      <p2>16423<\/p2>\n      <p3>Zdręby<\/p3>\n      <p4>Zdręby<\/p4>\n      <p5>16423<\/p5>\n    <\/VI>\n    <VII>\n      <p1>1063324<\/p1>\n      <p2>THEO<\/p2>\n      <p3>KANIA<\/p3>\n    <\/VII>\n    <VIII>\n      <p1>2020-11-26<\/p1>\n      <p2>2020-11-26<\/p2>\n      <p6>false<\/p6>\n      <p7>false<\/p7>\n      <p8>3962382940<\/p8>\n    <\/VIII>\n  <\/ZUSZLA>\n<\/KEDU>\n",
          "type": "ORYGINAŁ",
          "error": [
            {
              "NrRef": [
                "0",
                "1"
              ],
              "Rodzaj": "OSTRZEZENIE",
              "KodBledu": "W145",
              "OpisBledu": "Wymagane pole: Seria i numer zaświadczenia.",
              "Lokalizacja": "KEDU\/ZUSZLA\/I\/p1\/p1 KEDU\/ZUSZLA\/I\/p1\/p2"
            },
            {
              "NrRef": [
                "0"
              ],
              "Rodzaj": "OSTRZEZENIE",
              "KodBledu": "185",
              "OpisBledu": "Identyfikator KEDU nie został zarezerwowany dla podanej serii i numeru ZLA",
              "Lokalizacja": "\/KEDU\/ZUSZLA[@id_dokumentu] "
            }
          ]
        },
        {
          "document": 1,
          "content": "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?>\n<KEDU xmlns=\"http:\/\/www.zus.pl\/2015\/KED_ZLA_1\" xmlns:xsi=\"http:\/\/www.w3.org\/2001\/XMLSchema-instance\" wersja_schematu=\"1\">\n  <naglowek.KEDU>\n    <program>\n      <producent>Medfile<\/producent>\n      <symbol>eZLA<\/symbol>\n      <wersja>1.0<\/wersja>\n    <\/program>\n  <\/naglowek.KEDU>\n  <ZUSZLA id_dokumentu=\"155566\" kolejnosc=\"1\">\n    <I>\n      <p1\/>\n      <p2>KOPIA<\/p2>\n    <\/I>\n    <II>\n      <p1>68071892605<\/p1>\n      <p2>Filip<\/p2>\n      <p3>Jakubowski<\/p3>\n      <p4>1<\/p4>\n    <\/II>\n    <III>\n      <p1>25624<\/p1>\n      <p2>Kielce<\/p2>\n      <p3>Mieszka I<\/p3>\n      <p4>1<\/p4>\n    <\/III>\n    <IV>\n      <p1>\n        <p1>2020-11-27<\/p1>\n        <p2>2020-11-27<\/p2>\n      <\/p1>\n      <p3>2<\/p3>\n    <\/IV>\n    <V>\n      <p1>1<\/p1>\n      <p2>6707035254<\/p2>\n    <\/V>\n    <VI>\n      <p1>PRZYCHODNIA TARCZÓWKOWATYMI<\/p1>\n      <p2>16423<\/p2>\n      <p3>Zdręby<\/p3>\n      <p4>Zdręby<\/p4>\n      <p5>16423<\/p5>\n    <\/VI>\n    <VII>\n      <p1>1063324<\/p1>\n      <p2>THEO<\/p2>\n      <p3>KANIA<\/p3>\n    <\/VII>\n    <VIII>\n      <p1>2020-11-26<\/p1>\n      <p2>2020-11-26<\/p2>\n      <p6>false<\/p6>\n      <p7>false<\/p7>\n      <p8>3962382940<\/p8>\n    <\/VIII>\n  <\/ZUSZLA>\n<\/KEDU>\n",
          "type": "KOPIA",
          "error": [
            {
              "NrRef": [
                "0",
                "1"
              ],
              "Rodzaj": "OSTRZEZENIE",
              "KodBledu": "W145",
              "OpisBledu": "Wymagane pole: Seria i numer zaświadczenia.",
              "Lokalizacja": "KEDU\/ZUSZLA\/I\/p1\/p1 KEDU\/ZUSZLA\/I\/p1\/p2"
            },
            {
              "NrRef": [
                "1"
              ],
              "Rodzaj": "OSTRZEZENIE",
              "KodBledu": "185",
              "OpisBledu": "Identyfikator KEDU nie został zarezerwowany dla podanej serii i numeru ZLA",
              "Lokalizacja": "\/KEDU\/ZUSZLA[@id_dokumentu] "
            }
          ]
        }
      ]
    }
  }
}
```

### Kody odpowiedzi

1. `200` - Poprawna odpowiedź
2. `403` - Brak uprawnień do wykonania polecenia
3. `404` - Brak danych dla wybranego PESEL

## 8.9 Pobierz uprawnienia lekarza na dzień
## GET /ezla/doctor/permissions/{{today}}

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

### Odpowiedź

```
{
  "message": "",
  "body": true,
  "error": [],
  "raw": {
    "PobierzUprawnieniaNaDzienResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      },
      "MozliwoscWystawianiaZaswiadczenia": true
    }
  }
}
```


## 8.10. Pobierz słownik pokrewieństwa
## GET /ezla/dictionary/kinship

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

```json
{
  "message": "",
  "body": [
    {
      "Kod": "1",
      "Opis": "dziecko"
    },
    {
      "Kod": "2",
      "Opis": "małżonek, rodzice, ojczym, macocha, teściowie, dziadkowie, wnuki, rodzeństwo"
    },
    {
      "Kod": "3",
      "Opis": "inne osoby"
    }
  ],
  "error": [],
  "raw": {
    "PobierzSlownikKodowPokrewienstwaResponse": {
      "KodPokrewienstwa": [
        {
          "Kod": "1",
          "Opis": "dziecko"
        },
        {
          "Kod": "2",
          "Opis": "małżonek, rodzice, ojczym, macocha, teściowie, dziadkowie, wnuki, rodzeństwo"
        },
        {
          "Kod": "3",
          "Opis": "inne osoby"
        }
      ],
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      }
    }
  }
}
```


## 8.11. Pobierz słownik powodów anulowania
## GET /ezla/dictionary/reasonsOfCancel

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

```json
{
  "message": "",
  "body": [
    {
      "Kod": "U",
      "Nazwa": "błąd w danych identyfikacyjnych ubezpieczonego",
      "Opis": "Przyczyna o kodzie \"U\" ustawiana w przypadku błędnych danych identyfikacyjnych ubezpieczonego"
    },
    {
      "Kod": "P",
      "Nazwa": "błąd w danych płatnika składek",
      "Opis": "Przyczyna o kodzie \"P\" ustawiana w przypadku błędnych danych płatnika składek"
    },
    {
      "Kod": "N",
      "Nazwa": "błąd w danych dotyczących niezdolności do pracy",
      "Opis": "Przyczyna o kodzie \"N\" ustawiana w przypadku błędnych danych dotyczących niezdolności do pracy"
    },
    {
      "Kod": "M",
      "Nazwa": "błąd w danych miejsca udzielania świadczeń zdrowotnych",
      "Opis": "Przyczyna o kodzie \"M\" ustawiana w przypadku błędnych danych miejsca udzielania świadczeń zdrowotnych"
    },
    {
      "Kod": "I",
      "Nazwa": "inny powód",
      "Opis": "Przyczyna o kodzie \"I\" ustawiana w przypadku jeśli powód anulowania jest inny niż dostępnie wymienionych"
    },
    {
      "Kod": "B",
      "Nazwa": "błąd systemu",
      "Opis": "Przyczyna o kodzie \"B\" ustawiana w przypadku pojawienia się błędu systemu"
    },
    {
      "Kod": "A",
      "Nazwa": "błąd w danych adresowych ubezpieczonego",
      "Opis": "Przyczyna o kodzie \"A\" ustawiana w przypadku błędnych danych adresowych ubezpieczonego"
    },
    {
      "Kod": "E",
      "Nazwa": "błędne dane",
      "Opis": "Przyczyna o kodzie \"E\" ustawiana w przypadku błędnych danych - przypadek elektronizacji błędnie wypełnionego ZLA"
    },
    {
      "Kod": "X",
      "Nazwa": "błędna data wystawienia",
      "Opis": "Przyczyna o kodzie \"X\" ustawiana w przypadku błędnej dacie wystawienia ZLA - przypadek elektronizacji błędnie wypełnionego ZLA"
    }
  ],
  "error": [],
  "raw": {
    "PobierzSlownikPrzyczynAnulowaniaResponse": {
      "Przyczyna": [
        {
          "Kod": "U",
          "Nazwa": "błąd w danych identyfikacyjnych ubezpieczonego",
          "Opis": "Przyczyna o kodzie \"U\" ustawiana w przypadku błędnych danych identyfikacyjnych ubezpieczonego"
        },
        {
          "Kod": "P",
          "Nazwa": "błąd w danych płatnika składek",
          "Opis": "Przyczyna o kodzie \"P\" ustawiana w przypadku błędnych danych płatnika składek"
        },
        {
          "Kod": "N",
          "Nazwa": "błąd w danych dotyczących niezdolności do pracy",
          "Opis": "Przyczyna o kodzie \"N\" ustawiana w przypadku błędnych danych dotyczących niezdolności do pracy"
        },
        {
          "Kod": "M",
          "Nazwa": "błąd w danych miejsca udzielania świadczeń zdrowotnych",
          "Opis": "Przyczyna o kodzie \"M\" ustawiana w przypadku błędnych danych miejsca udzielania świadczeń zdrowotnych"
        },
        {
          "Kod": "I",
          "Nazwa": "inny powód",
          "Opis": "Przyczyna o kodzie \"I\" ustawiana w przypadku jeśli powód anulowania jest inny niż dostępnie wymienionych"
        },
        {
          "Kod": "B",
          "Nazwa": "błąd systemu",
          "Opis": "Przyczyna o kodzie \"B\" ustawiana w przypadku pojawienia się błędu systemu"
        },
        {
          "Kod": "A",
          "Nazwa": "błąd w danych adresowych ubezpieczonego",
          "Opis": "Przyczyna o kodzie \"A\" ustawiana w przypadku błędnych danych adresowych ubezpieczonego"
        },
        {
          "Kod": "E",
          "Nazwa": "błędne dane",
          "Opis": "Przyczyna o kodzie \"E\" ustawiana w przypadku błędnych danych - przypadek elektronizacji błędnie wypełnionego ZLA"
        },
        {
          "Kod": "X",
          "Nazwa": "błędna data wystawienia",
          "Opis": "Przyczyna o kodzie \"X\" ustawiana w przypadku błędnej dacie wystawienia ZLA - przypadek elektronizacji błędnie wypełnionego ZLA"
        }
      ],
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      }
    }
  }
}
```


## 8.12. Pobierz słownik powodów unieważnienia
## GET /ezla/dictionary/reasonsOfInvalidation

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

```json
{
  "message": "",
  "body": [
    {
      "Kod": "A",
      "Nazwa": "anulowanie",
      "Opis": ""
    },
    {
      "Kod": "S",
      "Nazwa": "zniszczenie",
      "Opis": ""
    },
    {
      "Kod": "Z",
      "Nazwa": "zaginięcie",
      "Opis": ""
    },
    {
      "Kod": "K",
      "Nazwa": "kradzież",
      "Opis": ""
    }
  ],
  "error": [],
  "raw": {
    "PobierzSlownikPrzyczynUniewaznieniaResponse": {
      "Przyczyna": [
        {
          "Kod": "A",
          "Nazwa": "anulowanie",
          "Opis": ""
        },
        {
          "Kod": "S",
          "Nazwa": "zniszczenie",
          "Opis": ""
        },
        {
          "Kod": "Z",
          "Nazwa": "zaginięcie",
          "Opis": ""
        },
        {
          "Kod": "K",
          "Nazwa": "kradzież",
          "Opis": ""
        }
      ],
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      }
    }
  }
}
```


## 8.13. Pobierz profil rehabilitacji
## GET /ezla/dictionary/rehabilitationProfile/{icd10Code}

### Nagłowki
- Authorization: Bearer {JWT TOKEN}

```json
{
  "message": "",
  "body": "",
  "error": [],
  "raw": {
    "SprawdzProfilRehabilitacjiResponse": {
      "Rezultat": {
        "KodBledu": "0",
        "OpisBledu": "OK"
      }
    }
  }
}
```

## 8.14. Pobieranie dokumentu
## GET /ezla/document/{ezlaSeries}/{ezlaNumber}

Zwraca dokument XML z dokumentem eZLA.



## 8.15. Wydruk dokumentu
## GET /ezla/document/{ezlaSeries}/{ezlaNumber}/print

Zwraca dokument HTML z naniesionym dokumentem eZLA.
Gotowy do konwersji na PDF.

# 9. Słowniki

## 9.1. `identifier` dla organizacji i praktyki

1. `type: rpwdl` pierwsza część kodu resortowego którą można odczytać na stronie https://rpwdl.csioz.gov.pl/ dla wybranego podmiotu. Tzw. numer księgi resortowej.
    - Przykład: 000000089955
    - Wymagany dla praktyk oraz podmiotów

1. `type: rpwdlUnit` piąta część kodu resortowego. Tzw. numer jednotki organizacyjnej pod którą wystawiamy receptę. Odczytamy ją z https://rpwdl.csioz.gov.pl/.
    - Przykłady: 01, 02, 99
    - Wymagany tylko dla podmiotów

1. `type: regon` numer REGON podmiotu lub praktyki. REGON 9 cyfrowy to podstawowy numer danego podmiotu lub praktyki. REGON 14 cyfrowy to połączony numer podstawowy podmiotu i dodatkowy numer jednostki np. 00004 (nie jest to tożsame z rpwdlUnit). Możemy to wyczytać z https://rpwdl.csioz.gov.pl/.
    - Przykład: 24154444300004, 241544443
    - Wymagany 9 cyfrowy dla praktyk i 14 cyfrowy dla podmiotów (jednostek).

1. `type: oidRoot` numer OID nadany przez P1 dla każdego certyfikatu który będzie użyty do komunikacji z CSIOZ. Możemy go otrzymać wyłącznie w wiadomości e-mail zaraz po poprawnym wygenerowaniu certyfikatów TLS i WSS.
    - Przykład: 2.16.840.1.113883.3.4424.2.7.67
    - Wymagany dla podmiotów i praktyk

1. `type: chamber` numer organu wydającego pozwolenie działalności dla praktyk i podmiotów. Można go wyczytać z https://rpwdl.csioz.gov.pl/.
    - Przykład: L-63 gdzie L = Okręgowa Rada Lekarska a 63 = Wielkopolska Izba Lekarska
    - Wymagany tylko dla praktyk lekarskich

## 9.2. `telecom` - numer telefonu

Dopuszczalny format numeru telefonicznego to:
```
+48600600600
```
Gdzie +48 to numer kierunkowy kraju (Polski).

## 9.3. `practitioner.role` rola lekarza

Znaczenie: kontekst opisujący stanowisko osoby wysyłającej żądanie do P1.

Przeznaczenie: wystawienie e-recepty i e-skierowania.

Dopuszczalne wartości:
- `LEK` - lekarz / felczer
- `LEKD` - lekarz dentysta

## 9.4. `practitioner.qualifications` specjalizacje lekarza

Znaczenie: kody i nazwy specjalizacji które posiada wybrany lekarz.

Przeznaczenie: wystawianie e-skierowań.

Dopuszczalne wartości: reguluje to rozporządzenie, źródło https://isap.sejm.gov.pl/isap.nsf/download.xsp/WDU20190000602/O/D20190602.pdf

Przykład:
```json
{
  "code": "0713",
  "display": "medycyna rodzinna"
}
```

## 9.5. `erecepta.type` typ recepty

Tym parametrem decydujemy czy wystawiamy receptę na lek gotowy czy recepturę własną.

Dopuszczalne wartości:
- `prepared` lek gotowy np. Apap
- `recipe` receptura np. lista składników

## 9.6. `patient.nfz`

Oddział NFZ do którego należy pacjent.

- 01 - Dolnośląski
- 02 - Kujawsko-Pomorski
- 03 - Lubelski
- 04 - Lubuski
- 05 - Łódzki
- 06 - Małopolski
- 07 - Mazowiecki
- 08 - Opolski
- 09 - Podkarpacki
- 10 - Podlaski
- 11 - Pomorski
- 12 - Śląski
- 13 - Świętokrzyski
- 14 - Warmińsko-Mazurski
- 15 - Wielkopolski
- 16 - Zachodniopomorski

## 9.7. `patient.permissions`

Uprawnienia dodatkowe pacjenta do zakupu bezpłatnych lub tańszych leków.

Np.:

AZ, IB, IW, PO, WP, ZK, S, C, BW, DN, CN, IN.



Szczegółowe informacje można odszukać tutaj (zakładka "Uprawnienia dodatkowe"):

https://www.nfz.gov.pl/dla-pacjenta/recepty-i-leki/

## 9.8. `patient.permissionsDocument`

Dokument potwierdzający uprawnienia dodatkowe pacjenta.

Dopuszczalne wartości:
- tekst np. "Legitymacja morska nr. 123123/123213"

## 9.9 `eskierowanie.type`

Typ skierowania wg klasyfikacji dokumentów P1

- `02.10` - Skierowanie na badanie lub leczenie
- `02.11` - Prośba o objęcie opieką
- `02.12` - Skierowanie na konsultację


# 10. eSkierowania



# eSkierowanie

### Nagłowki
- Authorization: Bearer {token}

### Dostępne endpointy:
#### Wystawianie skierowania
- (PUT|POST) [`/eskierowanie/validate`](PostValidate.md) - walidacja dokumentu skierowania
- (PUT|POST) [`/eskierowanie/send`](PostSend.md) - zapisanie dokumentu skierowania w systemie P1
- DELETE `/eskierowanie`
- GET `/eskierowanie`

#### Realizacja skierowania
- GET [`/eskierowanie/readout`](GetReadout.md) - odczyt dokumentu skierowania do realizacji
- POST [`/eskierowanie/acceptance`](PostAcceptance.md) - przyjęcie dokumentu skierowania do realizacji
- POST [`/eskierowanie/rejection`](PostRejection.md) - odmowa realizacji skierowania
- POST [`/eskierowanie/gaps`](PostGaps.md) - zgłoszenie informacji o braku w dokumentacji
- POST [`/eskierowanie/resignation`](PostResignation.md) - rezygnacja z realizacji skierowania
- POST [`/eskierowanie/completion`](PostCompletion.md) - zamkniecie realizacji skierowania


### Kody odpowiedzi
1. `200` - Poprawna odpowiedź
2. `400` - Błąd składni JSON
3. `403` - Brak uprawnień do wykonania polecenia
4. `404` - Zasób nie istnieje
5. `406` - Błąd w wysyłaniu eskierowania (np. nieprawidłowe dane)


## Pobieranie skierowania

Aby odczytać skierowanie, musimy znać [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Musimy mieć prawa odczytu skierowania. Możemy odczytywać skierowania wystawione przez naszą organizację lub realizowane przez nią.

<pre>
GET /eskierowanie/{idSkierowania}[/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}][/(print|preview)]
</pre>

Tylko *idSkierowania* jest obowiązkowym parametrem.

Jeżeli nie podamy parametru *oidRoot* użyty zostanie oidRoot zawarty w tokenie autoryzacyjnym.
Dzięki temu możemy odczytywać skierowania "naszej" organizacji.

Dodanie znacznika `/print` pozwala na pobranie dokumentu skierowania w formie gotowej do wydruku dla pacjenta.

Dodanie znacznika `/preview` zwróci nam dokument w formie podglądu dla lekarza.

Żądania zwracają dokument HTML.
### Przykłady

[`GET /eskierowanie/0000000000SK1607436862`](examples/GetDocument.json)

[`GET /eskierowanie/0000000000SK1607436862/print`](examples/Print.html)

[`GET /eskierowanie/0000000000SK1607436862/preview`](examples/PreviewDoc.html)

[`GET /eskierowanie/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67/print`](examples/Print.html)

[`GET /eskierowanie/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67/preview`](examples/PreviewDoc.html)


## Drukowanie skierowania

Aby odczytać i wydrukować skierowanie musimy znać [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Musimy mieć prawa odczytu skierowania. Możemy odczytywać skierowania wystawione przez naszą organizację lub realizowane przez nią.

<pre>
GET /eskierowanie/{idSkierowania}[/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}]/(print|preview)
</pre>

Tylko *idSkierowania* jest obowiązkowym parametrem.

Jeżeli nie podamy parametru *oidRoot* użyty zostanie oidRoot zawarty w tokenie autoryzacyjnym.
Dzięki temu możemy odczytywać skierowania "naszej" organizacji.

Dodanie znacznika `/print` pozwala na pobranie dokumentu skierowania w formie gotowej do wydruku dla pacjenta.

Dodanie znacznika `/preview` zwróci nam dokument w formie podglądu dla lekarza.

Żądania zwracają dokument HTML.
### Przykłady

`GET /eskierowanie/0000000000SK1607436862/print`

`GET /eskierowanie/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67/print`

`GET /eskierowanie/0000000000SK1607436862/preview`

`GET /eskierowanie/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67/preview`


## Odczyt dokumentu skierowania do realizacji

<pre>
GET /eskierowanie/readout/key/{eskierowanieKey}[/preview]
</pre>

<pre>
GET /eskierowanie/readout/code/{eskierowanieCode}[/preview]
</pre>


Zgodnie z dokumentacją systemu P1:

*Operacja odczytDokumentuSkierowaniaDoRealizacji umożliwia, na podstawie otrzymanego od Usługobiorcy klucza lub kodu dostępowego, pobranie skierowania wraz z dołączonymi do niego wynikami weryfikacji otrzymanymi w ramach zapisu dokumentu do Systemu P1 oraz komentarzami. W wyniku wykonania operacji zwrotnie odesłane zostanie: treść skierowania wraz z kluczem na podstawie którego można wywołać operację odmowaPrzyjeciaDoRealizacjiSkierowania albo przyjecieDoRealizacjiSkierowania, przy czym każdy podmiot ma dostęp jedynie do skierowań o statusie WYSTAWIONE (operacja nie powoduje zmiany statusu skierowania). (...) Uzyskanie danych wymaga posiadania uprawnień twórcy dokumentu lub Preautoryzacji uprawnień, Autoryzacji uprawnień.*

Zazwyczaj parametr `{eskierowanieCode}` przyjmuje postać:

`XXXXYYYYYYYYYYY`

gdzie

- `XXXX` to czterocyfrowy kod otrzymany przez pacjenta (na wydruku lub przez SMS)

- `YYYYYYYYYYY` to identyfikator pacjenta (np. PESEL)

Parametr `{eskierowanieKey}` znajduje się w postaci kodu kreskowego na wydruku skierowania.

Jeżeli użyjemy opcjonalnej flagi `preview` w odpowiedzi otrzymamy HTML zawierający wizualizację skierowania.
### Przykłady

[`GET /eskierowanie/readout/key/10031894188264384543363841602402324302238120`](examples/Readout.json)

[`GET /eskierowanie/readout/code/0000AC34567890`](examples/Readout.json)

[`GET /eskierowanie/readout/key/10031894188264384543363841602402324302238120/preview`](examples/Preview.html)


## Przyjęcie skierowania do realizacji

<pre>
POST /eskierowanie/acceptance/{eskierowanieKey}[[/R]/{terminWizyty}]
</pre>

Treść (opcjonalnie):
```json
{
  "comment": "Komentarz - przyjęcie"
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja przyjecieDoRealizacjiSkierowania umożliwia, na podstawie klucza skierowania, przyjęcie skierowania do realizacji. Przyjęcie wiąże się ze zmianą statusu skierowania na
"U realizatora" - pozytywny wynik wykonania operacji oznacza potwierdzenie zmiany statusu skierowania. (...)
W ramach przyjęcia skierowania wymagane jest określenie czy Usługobiorca zadeklarował realizację świadczenia jako refundowane czy jako pełnopłatne. System P1 weryfikuje możliwość realizacji takiego świadczenia wystawionego jako refundowane (Usługobiorca ma możliwość realizacji skierowania jako pełnopłatne mimo, iż zostało ono wystawione jako skierowanie refundowane). (...) Uzyskanie danych wymaga posiadania uprawnień twórcy dokumentu lub Preautoryzacji uprawnień, Autoryzacji uprawnień.*

Parametr `{eskierowanieKey}` znajduje się w postaci kodu kreskowego na wydruku skierowania. Możemy go również odczytać przez [odczyt dokumentu skierowania do realizacji](GetReadout.md)

Jeżeli użyjemy opcjonalnej flagi `R`, informujemy system P1, że skierowanie będzie realizowane jako refundowane.

Również opcjonalnie możemy podać termin wizyty w formacie np. YYYY-MM-DD hh:mm (oczywiście parametr należy odpowiednio zakodować)

### Przykłady

`POST /eskierowanie/acceptance/10031894188264384543363841602402324302238120`

`POST /eskierowanie/acceptance/10031894188264384543363841602402324302238120/R`

`POST /eskierowanie/acceptance/10031894188264384543363841602402324302238120/N/2021-03-22 12:30`


## Zamkniecie realizacji skierowania

<pre>
POST /eskierowanie/completion/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Komentarz - zakończenie",
  "payer": "01" // opcjonalnie - oddział NFZ 
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja zakonczenieRealizacjiSkierowania umożliwia, na podstawie numeru skierowania, zmianę statusu skierowania na "Zrealizowane" (nie jest wymagane przekazanie żadnego dokumentu realizacji skierowania). Podmiot który dokonał realizacji skierowania może dodać komentarz dot. realizacji, a w przypadku skierowań refundowanych może wskazać identyfikator płatnika z którym podmiot ma zawartą umowę. Nie ma możliwości wykonania niniejszej operacji jeśli status skierowanie jest "Wystawione" albo "Zrealizowane" albo "Anulowane", lub jest w trakcie realizacji przez inny podmiot (status "U Realizatora").*

### Przykłady

`POST /eskierowanie/completion/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`


## Zgłoszenie informacji o braku w dokumentacji


<pre>
POST /eskierowanie/gaps/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Komentarz o brakach w dokumentacji"
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja przekazanieInformacjiOBrakachDokumentacjiSkierowania umożliwia, na podstawie numeru skierowania, dodanie do skierowania komentarza z informacją o brakach w dokumentacji medycznej i niezbędnych uzupełnieniach (operacja dostępna jedynie dla skierowania o statusie „U realizatora„ dla podmiotu który jako ostatni przyjął to skierowanie do realizacji - aktualnie je realizuje). Dodanie komentarza nie wiąże się ze zmianą statusu skierowania.*

### Przykłady

`POST /eskierowanie/gaps/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`


## Odmowa realizacji skierowania

<pre>
POST /eskierowanie/rejection/{eskierowanieKey}
</pre>

Treść (minimum 5 znaków):
```json
{
  "comment": "Komentarz - odrzucenie skierowania do realizacji"
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja odmowaPrzyjeciaDoRealizacjiSkierowania umożliwia, na podstawie klucza skierowania, dodanie do skierowania informacji o przyczynach odmowy przyjęcia skierowania do realizacji przed dany podmiot. Odmowa nie wiąże się ze zmianą statusu skierowania (pozostaje status "Wystawione").*

Parametr `{eskierowanieKey}` znajduje się w postaci kodu kreskowego na wydruku skierowania. Możemy go również odczytać przez [odczyt dokumentu skierowania do realizacji](GetReadout.md)

### Przykłady

`POST /eskierowanie/rejection/10031894188264384543363841602402324302238120`


## Rezygnacja z realizacji skierowania

<pre>
POST /eskierowanie/resignation/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Rezygnacja z powodu..."
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja rezygnacjaZRealizacjiSkierowania umożliwia, na podstawie numeru skierowania, wycofanie skierowania do realizacji przez innym podmiot leczniczy. Rezygnacja wiąże się ze zmianą statusu skierowania z „U realizatora” na "Wystawione". Rezygnacja może być wykonana tylko przez podmiot, który jako ostatni przyjął to skierowanie do realizacji (aktualnie je realizuje). Po wykonaniu pozytywnym operacji skierowanie może być zrealizowana w każdym podmiocie.*

### Przykłady

`POST /eskierowanie/resignation/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`


## Zapisanie dokumentu skierowania w systemie P1

<pre>
PUT|POST /eskierowanie/send
</pre>

Zapisanie dokumentu skierowania w systemie P1.

Przed zapisem wykonana zostaje jego walidacja

Patrz opis [`/eskierowanie/validate`](PostValidate.md)


<pre>
PUT|POST /eskierowanie/validate
</pre>

Walidacja dokumentu skierowania. 

### Treść

```json
{
  "id": "1234567890abcdef789012",
  // dokładnie 22 znakowy identyfikator dokumentu
  "date": "2021-01-23",
  "type": "02.10",
  // typ dokumentu medycznego (patrz słownik)
  "organization": "idabc",
  "practitioner": "idxyz",
  "patient": {
    "identifier": [
      {
        "type": "pesel",
        "value": "40010175826"
      }
    ],
    "name": [
      {
        "family": "Senior",
        "given": [
          "Sylwester"
        ]
      }
    ],
    "telecom": [
      {
        "value": "+48131231230"
      }
    ],
    "address": [
      {
        "street": "Wrocławska",
        "houseNumber": "11A",
        "unitId": "3",
        "city": "Zielona Góra",
        "postalCode": "00-184",
        "country": "Polska"
      }
    ],
    "nfz": "07",
    // oddział NFZ w którym jest ubezpieczony pacjent
    "gender": "M",
    "birthDate": "1940-01-01"
  },
  "reimbursed": "Y",
  // używać tylko kiedy skierowanie ma być sfinansowane ze środków publicznych
  "nfzDepartment": "01",
  // oddział NFZ z którym organizacja zawarła kontrakt (wymagane jeżeli reimbursed=Y a brak tego w danych organizacji)
  "nfzContract": "01-06-02336-20-03/06",
  // numer umowy z NFZ (wymagane jeżeli reimbursed=Y a brak tego w danych organizacji)
  "title": "Skierowanie do poradni specjalistycznej",
  "description": [
    {
      "title": "Cel badania (uzasadnienie):",
      "content": "Diagnostyka przyczyny niedokrwistości."
    },
    {
      "title": "Badania dotychczas wykonane:",
      "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sed urna nibh.\nDonec lacus mi, varius sed ex at, elementum mattis mauris. In sit amet quam vel sem pellentesque euismod."
    }
  ],
  "condition": [
    {
      "icd10Code": "H60.0",
      "icd10Display": "Ropień ucha zewnętrznego",
      "bodySite": "R"
      // opcjonalne
    }
  ],
  "procedure": [
    {
      "icd9Code": "18.11",
      "icd9Display": "Wziernikowanie ucha"
    },
    {
      "icd9Code": "96.52",
      "icd9Display": "Płukanie ucha"
    }
  ],
  "observation": "Temperatura ciała: 36,6 <sup>o</sup>C<br/>\nCiśnienie krwi: 140/90",
  // opcjonalne
  "priorityCode": "UR",
  // Cito - opcjonalne
  "performer": "Poradnia Otolaryngologiczna",
  "performerCode": "1610"
  // część viii systemu resortowych kodów identyfikacyjnych
}
```

#### Przykłady odpowiedzi:
- [HTTP 200](examples/Validate200_1.json)
- [HTTP 400](examples/Validate400_1.json)
- [HTTP 406](examples/Validate406_1.json)
- [HTTP 406](examples/Validate406_2.json)


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




## 12.0 Baza leków

### 12.1 Wyszukiwanie leków

```
POST /dictionary/drug
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer {{ bearer }}

term=Apap
```

Odpowiedź:

```json
[
  {
    "name": "APAP dla dzieci Forte smak pomarańczowy",
    "dose": "40 mg\/ml",
    "package": "1 butelka 85 ml + strzykawka 6 ml",
    "code": "100443355",
    "ean": "05909991452063",
    "nameEn": null,
    "isReimbursed": false,
    "percent": "",
    "price": 0,
    "unit": "butelka 85 ml + strzykawka 6 ml",
    "form": "Zawiesina doustna",
    "category": "OTC",
    "amount": "1",
    "indications": "",
    "isRpw": false,
    "calculate": false,
    "availability": 0,
    "producer": "US Pharmacia Sp. z o.o.",
    "substance": "Paracetamolum",
    "expired": false,
    "favourite": 0
  }
]
```

### 12.2 Dodawnie ulubionych leków

```
POST {{host}}/dictionary/drug/favourite/{{EAN}}
Authorization: Bearer {{ bearer }}
```

### 12.3 Usuwanie ulubionych leków

```
DELETE /dictionary/drug/favourite/{{EAN}}
Authorization: Bearer {{ bearer }}
```


### 12.4 Pobieranie listy ulubionych leków

```
GET {{host}}/dictionary/drug/favourite
Authorization: Bearer {{ bearer }}
```

### 12.5 Szukanie składników receptur

```
POST {{host}}/dictionary/drug-ingredient
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer {{ bearer }}

term=Etanol
```

