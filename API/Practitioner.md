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