## Filtrowanie wyników

Parametr:
- `cond_col` - jedno lub wiele pól słownikowanych (nazwa pola), po którym ma być filtrowanie.
  
- `cond_op` - pole słownikowane; operator logiczny filtrowania.
  - Dostępne wartości:
    - `equalTo` - *domyślny operator*
    - `isNotEmpty`  
    - `lessThan`  
    - `lessThanOrEqualTo`  
    - `largerThan` 
    - `largerThanOrEqualTo`
    - `contains`
    - `startsWith`
    - `isEmpty`

- `cond_val` - warunek filtrowania lub warunki (odpowiednio do `cond_col`)
- `cond_concat` - w jaki sposób mają być złączone warunki
  - Dostępne wartości:
    - `AND` - *domyślny operator*
    - `OR`
  - Odpowiedź usługi będzie sumą wyników warunku filtrowania w przypadku logicznego złączenia warunków `OR` oraz iloczynem warunków w przypadku `AND`

### Przykład
`GET /ezla/listprocessedzla?cond_col=NumerZla&cond_val=9952547`

`GET /ezla/listprocessedzla?cond_col[0]=NazwiskoUbezpieczonego&cond_val[0]=Senior`

`GET /ezla/listprocessedzla?cond_col[0]=DataWystawieniaZla&cond_op[0]=largerThanOrEqualTo&cond_val[0]=2021-01-01&cond_col[1]=DataWystawieniaZla&cond_op[1]=lessThan&cond_val[1]=2021-02-01&cond_col=AND`


