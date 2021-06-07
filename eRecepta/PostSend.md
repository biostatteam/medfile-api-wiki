## 6.2. PUT|POST /erecepta/send

### Nagłowki
- Authorization: Bearer {token}

### Treść

```json
[
  {
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