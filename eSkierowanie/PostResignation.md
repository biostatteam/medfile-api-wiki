## Rezygnacja z realizacji skierowania

<pre>
POST /eskierowanie/resignation/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Rezygnacja z powodu..."
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja rezygnacjaZRealizacjiSkierowania umożliwia, na podstawie numeru skierowania, wycofanie skierowania do realizacji przez innym podmiot leczniczy. Rezygnacja wiąże się ze zmianą statusu skierowania z „U realizatora” na "Wystawione". Rezygnacja może być wykonana tylko przez podmiot, który jako ostatni przyjął to skierowanie do realizacji (aktualnie je realizuje). Po wykonaniu pozytywnym operacji skierowanie może być zrealizowana w każdym podmiocie.*

### Przykłady

`POST /eskierowanie/resignation/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`
