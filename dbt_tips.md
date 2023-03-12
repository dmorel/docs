# dbt notes

## Useful refresh commands

- only **materialized** tables (for cron jobs):

    `dbt run --select config.materialized:table`

- models **modified** since last run:

    `dbt run --select state:modified+ --state target/`

## Useful command line switches

- increase parallelism, for speed:

    `dbt run --threads 8 # number of cores or more`

- run on a non-default target

    `dbt run --target prod`

