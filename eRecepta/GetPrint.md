## 6.5. Drukowanie pakietu recept

Drukowanie pakietu recept polega na pobraniu z P1 treści recepty, dodaniu koduPakietuRecept i skonwertowaniu XML w HL7 CDA do formatu PDF.
KluczPakietuRecept potrzebny jest aby receptę z P1 pobrać.

Parametr kontekst jest opcjonalne ale należy go ustawić zgodnie z polem kind na dokumencie recepty.
- ZW - dla zwykłych recept (wartość domyślna)
- PF - dla recept pro familiae
- PA - dla recept pro auctorae

<pre>
GET /erecepta/{kluczPakietuRecept}/{kodPakietuRecept}/print/[/{<a href="Kontekst.md">kontekst = 'ZW'</a>}]
</pre>

Odpowiedź:

```json
{
    "html": "base64 encoded html document..."
}
```
