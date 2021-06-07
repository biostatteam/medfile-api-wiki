- eZLA = z
- eWUŚ = e
- eRecepta = r
- eSkierowanie = s

1. Każdy request do api będzie identyfikowany po obiekcie `Author`.
2. POST /erecepta/{routes=Api Methods}/ + JSON
3. Autoryzacja będzie realizowana przez nagłówek Auth: Bearer {token}
4. Treść dla `route` będzie w ciele - np. element `document`
5. Obiekty które zwracają nam usługodawcy trzeba zmapować do anglojęzycznej znormalizowanej wersji
6. Drukowanie recept - otrzymujemy ID recepty - szukamy w csioz ? - pobieramy - konwertujemy - generujemy PDF - zwracamy.
7. Jako dodatkowy parametr odsyłać RAW response z usługodawcy.

Moduły

- /erecepta/
- /ezla/
- /eskierowanie/
- /ewus/

- /organization/1 - (tlsCert: '...', wssCert: '...')
- /organization/2 - (tlsCert: '...', wssCert: '...')
- /practitioner/ - (zusCert: '...base64', zusPassword, ..., ewuś: {password, account, province, ...})


```
miejscaWykonywaniaZawodu: [{
   adres: 'aaa',
   ulica: 'aaa',
   inne: '...',
   numerDomu: {nr: '1'}
}]
```

```json
{
  "date": "2020-08-31T12:17:13+02:00",
  "authorReference": "Practitioner/acae46da-8158-4854-9858-8aef1cef4613",
  "organizationReference": "Organization/4d64ea27-8518-4584-8589-3164fec1fea8",
  "title": "Skierowanie do laboratorium",
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
        "value": "+48500100200"
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

  // To be finished
  "document": {

  }
}
