# ReSync Patch for PostgreSQL

## Compile and start the patched PostgreSQL

```bash
git clone git@github.com:SWUFE-DB-Group/postgresql-encoding-validation.git
```

```bash
meson setup build-release \
  --prefix=/tmp/pg-dev-install \
  --buildtype=release \
  -Dcassert=false

ninja -C build-release
ninja -C build-release install
```

```bash
export PATH=/tmp/pg-dev-install/bin:$PATH
export PGDATA=/tmp/pg-dev-data
initdb -D "$PGDATA" --encoding=UTF8 --locale=C
pg_ctl -D "$PGDATA" -l /tmp/pg-dev.log start

createdb -E EUC_CN -T template0 --locale=C demo_euc_cn_db
psql demo_euc_cn_db -c "SELECT version();"

```

If everything goes well, you should see:

```
                                         version
------------------------------------------------------------------------------------------
 PostgreSQL 18.3 on x86_64-linux, compiled by gcc-15.2.1, 64-bit [encoding-valiation-dev]
(1 row)
```

where `[encoding-valiation-dev]` indicates that the patch for encoding validation is applied.

## Usage

```sql
SHOW encoding_validation;
SET encoding_validation = 'native';
SET encoding_validation = 'read_compatible';
SET encoding_validation = 'read_compatible_lookup';
SET encoding_validation = 'read_compatible_lookup_bit';
```

### Example

```
demo_euc_cn_db=# CREATE TABLE test(id int, s text);
CREATE TABLE
demo_euc_cn_db=# SHOW encoding_validation;
 encoding_validation
---------------------
 native
(1 row)

demo_euc_cn_db=# INSERT INTO test VALUES(1, E'\xA2\xA1');
INSERT 0 1
```

```
demo_euc_cn_db=# SET encoding_validation = 'read_compatible';
SET
demo_euc_cn_db=# SHOW encoding_validation;
 encoding_validation
---------------------
 read_compatible
(1 row)

demo_euc_cn_db=# INSERT INTO test VALUES(2, E'\xA2\xA1');
2026-05-10 10:22:44.907 CST [3867252] ERROR:  invalid byte sequence for encoding "EUC_CN": 0xa2 0xa1
2026-05-10 10:22:44.907 CST [3867252] STATEMENT:  INSERT INTO test VALUES(2, E'\xA2\xA1');
ERROR:  invalid byte sequence for encoding "EUC_CN": 0xa2 0xa1

```
