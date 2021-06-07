# 3. Organizacja - podmiot lub praktyka

Organizacją nazywamy dowolny podmiot medyczny lub praktykę lekarską.

Do pracy z Medfile API wymagane jest utworzenie przynajmniej jednej organizacji która będzie używana we właściwych usługach w tym np. erecepta.


```json
{
  "uuid": "dbc48e96-51a7-4f4c-93f0-2c243c62bac3",
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