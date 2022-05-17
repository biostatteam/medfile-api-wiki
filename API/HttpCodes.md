# 1. Kody odpowiedzi z Medfile API

- `200` - Poprawna odpowiedź
- `400` - Nieprawidłowe żądanie - np. metoda GET gdy wymagany POST
- `401` - Brak autoryzacji - token JWT jest nie tak
- `403` - Brak dostępu do wybranych zasobów
- `404` - Nie znaleziono zasobów np. recepty czy danych lekarza
- `406` - Not Acceptable/Wynik inny od sukcesu w przypadku np. weryfikacji recepty.
- `500` - Gdy wystąpi nieobsłużony problem
- `501` - Gdy funkcjonalność jeszcze nie jest uruchomiona - możemy to zwracać dla `eskierowanie`