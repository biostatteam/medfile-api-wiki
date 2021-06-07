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
