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
