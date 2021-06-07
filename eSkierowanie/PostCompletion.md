## Zamkniecie realizacji skierowania

<pre>
POST /eskierowanie/completion/{idSkierowania}/{<a href="../API/Dictionaries.md#9.1.">oidRoot</a>}
</pre>
- idSkierowania - numer skierowania
- [oidRoot](../API/Dictionaries.md#9.1.) organizacji, która wystawiła skierowanie.

Treść:
```json
{
  "comment": "Komentarz - zakończenie",
  "payer": "01" // opcjonalnie - oddział NFZ 
}
```

Zgodnie z dokumentacją systemu P1:

*Operacja zakonczenieRealizacjiSkierowania umożliwia, na podstawie numeru skierowania, zmianę statusu skierowania na "Zrealizowane" (nie jest wymagane przekazanie żadnego dokumentu realizacji skierowania). Podmiot który dokonał realizacji skierowania może dodać komentarz dot. realizacji, a w przypadku skierowań refundowanych może wskazać identyfikator płatnika z którym podmiot ma zawartą umowę. Nie ma możliwości wykonania niniejszej operacji jeśli status skierowanie jest "Wystawione" albo "Zrealizowane" albo "Anulowane", lub jest w trakcie realizacji przez inny podmiot (status "U Realizatora").*

### Przykłady

`POST /eskierowanie/completion/0000000000SK1607436862/2.16.840.1.113883.3.4424.2.7.67`
