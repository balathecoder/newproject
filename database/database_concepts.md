create table property ( ID serial primary key, UniqueID UUID, PropertyName varchar(100), PropertyLocation varchar(100))
There is a field like text[] to have array of strings int[] to have array of numbers

## Normalization
Normalization helps to avoid redudancy in table data.
Improves data integrity
simplifies database design.
## Denormalization
for performance imporovement instead of joining different tables to get records, still can go with single table.

