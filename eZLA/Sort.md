## Sortowanie wyników

Parametr:
- `sort_col` - jedno lub wiele pól słownikowanych (nazwa pola), po którym ma być sortowanie.
  W przypadku wielu pól sortowanie odbywa się w kolejności ich wystąpienia.
- `sort_order` - kierunek sortowania. Można zdefiniować dla każdego pola oddzielnie lub dla wszystkich oddzielnie.
  - Dostępne wartości:
    - `ASC`
    - `DESC`

### Przykład
`GET /ezla/listprocessedzla?sort_col=DataWystawieniaZla`

`GET /ezla/listprocessedzla?sort_col=DataWystawieniaZla&sort_order=DESC`

`GET /ezla/listprocessedzla?sort_col=DataWystawieniaZla&sort_order=DESC`

`GET /ezla/listprocessedzla?sort_col[0]=DataWystawieniaZla&sort_order[0]=DESC&sort_col[1]=NumerZla&sort_order[1]=ASC`

