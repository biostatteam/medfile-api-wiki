## BioStat Medfile API Wiki

1. [Wprowadzenie](Introduction.md)
1. [Odpowiedzi HTTP](API/HttpCodes.md)
1. [Autoryzacja JWT](API/JWTToken.md)
1. [Podmioty i praktyki](API/Organization.md)
1. [Specjaliści i pracownicy](API/Practitioner.md)
1. [Konfiguracja usług](API/Services.md)
1. [Słownik wybranych pojęć](API/Dictionaries.md)

## eRecepta
1. [Wprowadzenie](eRecepta/Configuration.md)
2. [Weryfikacja recepty](eRecepta/PostValidate.md)
3. [Wysłanie recepty](eRecepta/PostSend.md)
4. [Anulowanie recepty](eRecepta/DeleteDocument.md)
5. [Pobranie recepty](eRecepta/GetDocument.md)
6. [Transformacja recepty do HTML](eRecepta/GetPrint.md)

## eWUŚ
1. [Konfiguracja eWUŚ](eWus/Configuration.md)
1. [Użycie usługi eWUŚ](eWus/Routes.md)

## eZLA
1. [Konfiguracja](eZLA/8.Configuration.md)
2. Pobieranie danych lekarza
   1. [Pobierz dane lekarza](eZLA/8.2.GetDoctor.md)
   2. [Pobierz miejsca wykonywania zawodu](eZLA/8.3.GetDoctor_WorkPlace.md)
   3. [Pobierz uprawnienia lekarza](eZLA/8.9.GetDoctor_Permissions.md)
3. Pobieranie danych pacjenta
   1. [Pobierz dane ubezpieczonego](eZLA/8.4.GetPatient.md)
   2. [Pobierz członków rodziny ubezpieczonego](eZLA/8.5.GetPatient_Family.md)
   3. [Pobierz płatników ubezpieczonego](eZLA/8.6.GetPatient_Payers.md)
4. Wysyłanie zwolnień
   1. [Weryfikuj i wyślij zwolnienie](eZLA/8.8.PostSubmit.md)
   2. [Pobierz zwolnienie](eZLA/8.14.GetDocument.md)
   3. [Transformacja zwolnienia do HTML](eZLA/8.15.PrintDocument.md)
5.  Słowniki
    1.  [Pobierz słownik pokrewieństwa](eZLA/8.10.GetDictionary_Kinship.md)
    2.  [Pobierz słownik powodów anulowania](eZLA/8.11.GetDictionary_ReasonsOfCancel.md)
    3.  [Pobierz słowniek powodów inwalidacji](eZLA/8.12.GetDictionary_ReasonsOfInvalidation.md)
    4.  [Pobierz słownik profili rehabilitacyjnych](eZLA/8.13.GetDictionary_RehabilitationProfile.md)


## eSkierowanie
1. [Konfiguracja eSkierowań](eSkierowanie/Configuration.md)
1. [Kody odpowiedzi](eSkierowanie/Eskierowanie.md)
1. [Weryfikuj skierowanie](eSkierowanie/PostValidate.md)
1. [Wyślij skierowanie](eSkierowanie/PostSend.md)
1. [Pobierz skierowanie](eSkierowanie/GetDocument.md)
1. [Transformacja sKierowania do HTML](eSkierowanie/GetPrint.md)
1. [Pobierz skierowanie do realizacji](eSkierowanie/GetReadout.md)
1. [Przyjmij skierowanie do realizacji](eSkierowanie/PostAcceptance.md)
1. [Zakończ realizację skierowania](eSkierowanie/PostCompletion.md)
1. [Wyślij informację o brakach w skierowaniu](eSkierowanie/PostGaps.md)
1. [Odrzuć skierowanie](eSkierowanie/PostRejection.md)
1. [Zrezyguj z realizacji skierowania](eSkierowanie/PostResignation.md)

## Zdarzenia Medyczne
1. [Konfiguracja Zdarzeń Medycznych](ZM/Configuration.md)

## Rejestry
1. [Baza leków](Drugs/Introduction.md)