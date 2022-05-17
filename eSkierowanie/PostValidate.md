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
