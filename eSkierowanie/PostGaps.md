## Zgłoszenie informacji o braku w dokumentacji


<pre>
POST /eskierowanie/gaps/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Komentarz o brakach w dokumentacji"
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja przekazanieInformacjiOBrakachDokumentacjiSkierowania umożliwia, na podstawie numeru skierowania, dodanie do skierowania komentarza z informacją o brakach w dokumentacji medycznej i niezbędnych uzupełnieniach (operacja dostępna jedynie dla skierowania o statusie „U realizatora„ dla podmiotu który jako ostatni przyjął to skierowanie do realizacji - aktualnie je realizuje). Dodanie komentarza nie wiąże się ze zmianą statusu skierowania.*

### Przykłady

`POST /eskierowanie/gaps/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`
