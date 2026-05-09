# ReSync Patch for PostgreSQL

```sql
SHOW encoding_validation;
SET encoding_validation = 'native';
SET encoding_validation = 'read_compatible';
SET encoding_validation = 'read_compatible_lookup';
SET encoding_validation = 'read_compatible_lookup_bit';
```
