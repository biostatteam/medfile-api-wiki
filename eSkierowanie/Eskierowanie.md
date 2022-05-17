# eSkierowanie

### Nagłowki
- Authorization: Bearer {token}

### Dostępne endpointy:
#### Wystawianie skierowania
- (PUT|POST) [`/eskierowanie/validate`](PostValidate.md) - walidacja dokumentu skierowania
- (PUT|POST) [`/eskierowanie/send`](PostSend.md) - zapisanie dokumentu skierowania w systemie P1
- DELETE `/eskierowanie`
- GET `/eskierowanie`

#### Realizacja skierowania
- GET [`/eskierowanie/readout`](GetReadout.md) - odczyt dokumentu skierowania do realizacji
- POST [`/eskierowanie/acceptance`](PostAcceptance.md) - przyjęcie dokumentu skierowania do realizacji
- POST [`/eskierowanie/rejection`](PostRejection.md) - odmowa realizacji skierowania
- POST [`/eskierowanie/gaps`](PostGaps.md) - zgłoszenie informacji o braku w dokumentacji
- POST [`/eskierowanie/resignation`](PostResignation.md) - rezygnacja z realizacji skierowania
- POST [`/eskierowanie/completion`](PostCompletion.md) - zamkniecie realizacji skierowania


### Kody odpowiedzi
1. `200` - Poprawna odpowiedź
2. `400` - Błąd składni JSON
3. `403` - Brak uprawnień do wykonania polecenia
4. `404` - Zasób nie istnieje
5. `406` - Błąd w wysyłaniu eskierowania (np. nieprawidłowe dane)
