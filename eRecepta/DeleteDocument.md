## 6.3. Anulowanie e-recepty

Anulowanie całego pakietu recept (od 1 do 5 leków).
<pre>
DELETE /erecepta/{kluczPakietuRecept}[/{<a href="Kontekst.md">kontekst = 'ZW'</a>}]
</pre>

Anulowanie wybranej pozycji recepty. ***(Not implemented yet)***

```
DELETE /erecepta/{kluczPakietuRecept}/{kodPozycjiLekuWPakiecieRecept}[/{kontekst = 'ZW'}]
```

Odpowiedź:

```json
{
  "erecepta": [
      {
          //lek 1
      },
      {
          //lek N ...
      }
  ]
}
```

W treści odpowiedzi znajdzie się bezpośrednia odpowiedź od CSIOZ na każdy z leków we wskazanym pakiecie recept w postaci tablicy.