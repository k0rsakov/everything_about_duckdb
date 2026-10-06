# Взаимодействие DuckDB с MinIO S3

> [!NOTE]
> **TL;DR** Заменил нерабочий Minio S3 на рабочий Silo S3. Проект снова актуален. Все изменения отображены в PR [#1](https://github.com/k0rsakov/everything_about_duckdb/pull/1)

> [!IMPORTANT]
> [Minio](https://github.com/minio/minio) ушел из OpenSource, но Docker-образы были доступны.<br><br>
> 2026-09-14 на Reddit был опубликован
пост — [MinIO just removed their DockerHub image](https://www.reddit.com/r/minio/s/KxwCW1gKNE).<br><br>
> Я не сижу на Reddit 24/7 и поэтому увидел пост не сразу. Узнал об этом когда получил сообщение по типу: "_проект не
работает_".
> Проинформировал, что в курсе проблемы в своем tg-канале — [пост](https://t.me/DataLikeQWERTY/194).<br><br>
> Исследовал аналоги, думал взять совсем что-то другое, что описывал в этом [посте](https://t.me/DataLikeQWERTY/150)
>, но решил остановить свой выбор на fork Minio — [PGSTY Silo](https://github.com/pgsty/silo).
> На текущий момент (2026-10-05) Minio заменен на Silo. Более подробно описано в PR [#1](https://github.com/k0rsakov/everything_about_duckdb/pull/1)

___

Документация:

- [Python API](https://duckdb.org/docs/current/clients/python/overview)
- [Python DB API](https://duckdb.org/docs/current/clients/python/dbapi)
- Чтение файлов:
    - [Parquet](https://duckdb.org/docs/current/data/parquet/overview)
- [TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- [COPY Statement](https://duckdb.org/docs/current/sql/statements/copy)
- [Pattern Matching](https://duckdb.org/docs/current/sql/functions/pattern_matching)
- [Reading Multiple Files](https://duckdb.org/docs/current/data/multiple_files/overview)
- [Partitioned Writes](https://duckdb.org/docs/lts/data/partitioning/partitioned_writes)

## Сборка проекта

```bash
docker compose up -d
```

## `VIEW` для упрощения работы с S3

Не всегда удобно и легко прописывать пути в S3. Поэтому можно создавать представления (`VIEW`) для более быстрой работы
с данными.

**\# Пример:**

```sql
CREATE OR REPLACE VIEW trips_20_21_22 AS
SELECT
  *
FROM
  's3://prod/yellow_tripdata/202[0-2]/*/data.parquet'
```