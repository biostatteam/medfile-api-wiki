# 2. Autoryzacja w Medfile API

## 2.1 JWT Token

Do autoryzacji używamy tokentów JWT o krótkim terminie ważności z podpisem.
Po podpisaniu umowy firma BioStat udostępnia klucze do szyfrowania i weryfikacji tokentów JWT.

Token JWT wymagana następujących pól:
- *iss* - nadany identyfiakator integratora
- *aud* - stała wartość "medfile"
- *iat* - czas (unix timestamp) wygenerowania tokena
- *exp* - czas (unix timestamp) wygaśnięcia tokena (zalecany maks. iat + 60)
- *practitioner* - opcjonalnie (kiedy wymagany) identyfikator lekarza w którego kontekście wywoływana jest akcja
- *organization* - opcjonalnie (kiedy wymagany) identyfikator podmiotu w którego kontekście wywoływana jest akcja

Ponadto:

- pole *sub* nie powinno być używane (zarezerwowane do przyszłych zastosowań)

## 2.2 Przekazywanie tokenu do API

Token przekazujemy w każdym request wysyłanym do Medfile API jako nagłówek HTTP.

Tj.

```
Authorization: Bearer JWT_TOKEN
```

## 2.3 Nieprawidłowa autoryzacja

Nieprawidłowo podpisany token będzie skutkował odpowiedzą HTTP: `401` - Brak autoryzacji - token JWT jest nie tak