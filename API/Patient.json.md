# Struktura obiektu Pacjent (przykład)

```json
{
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
  "gender": "M",
  "birthDate": "1940-01-01"
}
```
Podstawowym identyfikatorem pacjenta jest PESEL. Alternatywnie (w przypadku obcokrajowców) można podać (w zalecanej kolejności):
 - nationalId
 - identityCardNumber
 - driversLicenseNumber
 - sailorId
 - passportNumber

Identyfikator poprzedzamy trzycyfrowym kodem kraju wg ISO 3166-1. Np. dla osoby posługującej się paszportem USA (kod 860) o numerze 1234567890
```json
  {
    "type": "passportNumber",
    "value": "840/1234567890"
  }
```