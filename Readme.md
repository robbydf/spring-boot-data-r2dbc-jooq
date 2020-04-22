
# Reactive JOOQ - an R2DBC adapter for Spring Boot

![GitHub tag (latest SemVer)](https://img.shields.io/github/tag/gofabian/spring-boot-data-r2dbc-jooq)

This library integrates JOOQ into Spring Data R2DBC.

Usage:

```java
var selectQuery = dslContext.selectFrom(table("book"));
Flux<Record> flux = ReactiveJooq.fetch(selectQuery);
Mono<Record> monoOne = ReactiveJooq.fetchOne(selectQuery);
Mono<Record> monoAny = ReactiveJooq.fetchAny(selectQuery);
Mono<Integer> monoCount = ReactiveJooq.fetchCount(selectQuery);
Mono<Boolean> monoExists = ReactiveJooq.fetchExists(selectQuery);

var insertQuery = dslContext.insertInto(table("books"), field("name")).values("book");
Mono<Integer> monoUpdateCount = ReactiveJooq.execute(insertQuery);

var myRecord = dslContext.newRecord(MY_TABLE);
Mono<Integer> monoInsertRecordCount = ReactiveJooq.executeInsert(myRecord);
Mono<Integer> monoUpdateRecordCount = ReactiveJooq.executeUpdate(myRecord);
Mono<Integer> monoDeleteRecordCount = ReactiveJooq.executeDelete(myRecord);
```


## Getting Started with Maven

Add this library and a database driver (e. g. h2) to your `pom.xml`:

```xml
<dependency>
    <groupId>de.gofabian</groupId>
    <artifactId>spring-boot-data-r2dbc-jooq</artifactId>
    <version>0.1.1</version>
</dependency>
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-h2</artifactId>
    <version>0.8.3.RELEASE</version>
</dependency>
```

If you use Spring Boot the library auto-configures a DSLContext with the correct SQL dialect for you. You can autowire
the DSLContext in your code. 

If you do not use Spring Boot make sure that `R2dbcJooqAutoConfiguration` is detected by Spring.


## Support

- H2, MySQL, Postgresql
- Java 8+
- Spring @Transactional annotation


## Realization status

| JOOQ | Reactive JOOQ |
| --- | --- |
| `query.execute()` -> `int` | `ReactiveJooq.execute(query)` -> `Mono<Integer>` |
| `query.fetch()` -> `Result<R>` | `ReactiveJooq.fetch(query)` -> `Flux<Record>` |
| `query.fetchOne()` -> `R` | `ReactiveJooq.fetchOne(query)` -> `Mono<Record>` |
| `query.fetchAny()` -> `R` | `ReactiveJooq.fetchAny(query)` -> `Mono<Record>` |
| `dslContext.fetchExists(query)` -> `boolean` | `ReactiveJooq.fetchExists(query)` -> `Mono<Boolean>` |
| `dslContext.fetchCount(query)` -> `int` | `ReactiveJooq.fetchCount(query)` -> `Mono<Integer>` |
| `record.insert()` -> `int` | `ReactiveJooq.executeInsert(record)` -> `Mono<Integer>` |
| `record.update()` -> `int` | `ReactiveJooq.executeUpdate(record)` -> `Mono<Integer>` |
| `record.delete()` -> `int` | `ReactiveJooq.executeDelete(record)` -> `Mono<Integer>` |
| `record.store()` -> `int` | ? |
| `record.refresh()` | ? |


## Restrictions

The auto-configured DSLContext does not have a JDBC connection. It is only intended to generate SQL strings which is 
given to the R2DBC database client for execution.

Do not use JOOQ methods that require a JDBC connection (every method that calls the database)! Use the `ReactiveJooq` 
methods instead.

Incomplete list of methods that will not work:

- `DslContext.fetchXxx()`
- `DslContext.executeXxx()`
- `DslContext.transactionXxx()`
- `Record.store()`
- `Record.insert()`
- `Record.update()`
- `Record.refresh()`
- `Record.delete()`


## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details