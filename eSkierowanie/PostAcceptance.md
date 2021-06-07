## Przyjęcie skierowania do realizacji

<pre>
POST /eskierowanie/acceptance/{eskierowanieKey}[/R]
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

### Przykłady

`POST /eskierowanie/acceptance/10031894188264384543363841602402324302238120`

`POST /eskierowanie/acceptance/10031894188264384543363841602402324302238120/R`
