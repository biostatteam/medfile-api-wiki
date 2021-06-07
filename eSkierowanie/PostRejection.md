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
