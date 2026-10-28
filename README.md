# Spring Data JPA Uni-Temporal Audit

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE.md)
[![Java](https://img.shields.io/badge/Java-Backend-orange.svg)](https://www.oracle.com/java/)
[![Spring Data JPA](https://img.shields.io/badge/Spring%20Data-JPA-green.svg)](https://spring.io/projects/spring-data-jpa)

A lightweight temporal auditing extension for Spring Data JPA that maintains historical versions of entity data within the same database table. It provides application-level data versioning, configurable batch operations, and parallel processing utilities to help developers build auditable and data-intensive applications.

## 🚀 Key Features

- **Temporal Auditing:** Preserves historical versions of entity data instead of simply overwriting existing values.
- **Single-Table Versioning:** Stores current and historical entity versions in the same table, without requiring separate audit tables.
- **Database Portability:** Implements temporal functionality at the application layer using JPA-compatible database technologies.
- **Batch Processing:** Provides batch save and delete operations for processing large collections of entities.
- **Configurable Batch Size:** Supports custom batch sizes to suit different workloads.
- **Parallel Processing:** Provides utilities for processing batches concurrently using multiple threads and separate `EntityManager` instances.
- **Spring Data Integration:** Extends repository functionality through `TemporalRepository<T, ID>`.
- **Simplified Configuration:** Supports temporal repository configuration through `@EnableJpaTemporalRepositories`.

## 🏗️ How It Works

Traditional database updates replace existing field values, making it difficult to reconstruct previous states.

Temporal auditing preserves historical information so that earlier versions of an entity remain available.

The general workflow is:

1. Create an entity using the temporal entity model.
2. Save the entity through a temporal repository.
3. Update the entity when its business data changes.
4. Preserve historical versions according to the library's temporal implementation.
5. Query the current entity state using supported repository operations.

This approach is useful for audit trails, transaction histories, data change tracking, and applications that need to retain historical records.

## 🛠️ Technology Stack

- Java
- Spring Boot
- Spring Data JPA
- Hibernate/JPA
- Maven
- Relational databases supported by the project's JPA configuration

## 📦 Installation

Add the library dependency to your Maven `pom.xml` file.

```xml
<dependency>
    <groupId>io.github.yash0330</groupId>
    <artifactId>spring-data-jpa-Uni-temporal</artifactId>
    <version>REPLACE_WITH_LATEST_VERSION</version>
</dependency>
```

Replace `REPLACE_WITH_LATEST_VERSION` with the actual version available in the project's releases or Maven Central.

## ⚡ Quick Start

### 1. Enable Temporal Repositories

Configure temporal repository support in your Spring application.

```java
@SpringBootApplication
@EnableJpaTemporalRepositories
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 2. Create a Temporal Entity

Create an entity that extends the library's `Temporal` base class.

The following is an illustrative example. Adapt the entity and identifier fields to the actual library API.

```java
@Entity
public class Transaction extends Temporal {

    @Id
    private Long id;

    private String description;

    private BigDecimal amount;

    private String status;

    // Getters and setters
}
```

### 3. Create a Temporal Repository

Create a repository interface that extends `TemporalRepository<T, ID>`.

```java
@Repository
public interface TransactionRepository
        extends TemporalRepository<Transaction, Long> {
}
```

Use the identifier type and entity mapping required by your application and the library. Some implementations may require a dedicated temporal identifier class.

## ⚙️ Batch Operations

The library provides methods for saving and deleting collections of entities in batches.

### Save Operations

- `saveInBatch(List<T>)`
- `saveAllInBatch(List<T>, EntityManager)`
- `saveInBatch(List<T>, int batchSize)`

### Delete Operations

- `deleteInBatch(List<T>)`
- `deleteAllInBatch(List<T>, EntityManager)`
- `deleteInBatch(List<T>, int batchSize)`

Batch operations process entities in groups instead of handling each record individually. This can reduce persistence overhead when working with large datasets.

### Configure Batch Size

The documented default batch size is 700.

Configure it in `application.properties`:

```properties
jpa.temporal.batchSize=700
```

Alternatively, use `application.yml`:

```yaml
jpa:
  temporal:
    batchSize: 700
```

Confirm the property name and default against the library version you are using.

## 🔄 Parallel Batch Processing

The library also provides a `ParallelBatchUpdater` utility for processing batches concurrently.

Example service structure:

```java
@Service
public class TransactionAuditService {

    private final ParallelBatchUpdater parallelBatchUpdater;

    private final TransactionRepository repository;

    public TransactionAuditService(
            ParallelBatchUpdater parallelBatchUpdater,
            TransactionRepository repository) {
        this.parallelBatchUpdater = parallelBatchUpdater;
        this.repository = repository;
    }

    public void saveTransactions(List<Transaction> transactions) {
        int workerThreads = 4;

        parallelBatchUpdater.saveInParallel(
                transactions,
                workerThreads,
                repository
        );
    }
}
```

Parallel execution can help with large workloads, but the actual performance depends on database capacity, connection-pool configuration, transaction management, and workload characteristics.

Review the library's API and transaction requirements before using this example.

## 🎯 Use Cases

This library may be useful for:

- Financial transaction history
- Audit trails and compliance-related record keeping
- Tracking changes to business entities
- Historical data analysis
- Applications that process large volumes of records
- Systems that need to preserve previous entity states

## ⚠️ Current Limitations

According to the project's documentation, the following limitations apply:

- Derived query methods such as `findByNameAndAddress` and `countByNameAndAddress` are not supported directly.
- Entity relationships such as `@OneToOne` and `@OneToMany` are not supported.

Custom queries using `@Query` may be used where supported by the library.

Review the source code and tests to understand the exact limitations of the version you are using.

## 🔍 Alternative Approaches

Other approaches to auditing and historical data management include:

- Hibernate Envers
- JaVers
- Database-native temporal tables
- Custom auditing using JPA lifecycle callbacks
- Dedicated audit tables

The appropriate approach depends on requirements such as historical querying, relationship support, database portability, and performance.

## 🗺️ Future Improvements

Potential areas for future development include:

- Support for derived query methods
- Support for entity relationships and associations
- Querying entity states at a specific point in time
- Improved logging and diagnostics
- Additional integration tests
- More extensive performance benchmarking

These are potential improvements, not a statement that they are currently implemented.

## 🧪 Testing and Performance

Performance should be evaluated using representative workloads and documented test conditions.

Useful measurements include:

- Records inserted per second
- Time required to update records while preserving history
- Memory consumption
- Database connection usage
- Performance under concurrent workloads
- Correctness of historical records after updates and deletes

Do not report benchmark numbers unless they have been measured and the test environment and methodology are documented.

## 🤝 Contributing

Contributions, bug reports, and suggestions are welcome.

If you would like to contribute:

1. Fork the repository.
2. Create a feature branch.
3. Implement your changes and add appropriate tests.
4. Run the test suite.
5. Submit a pull request describing your changes.

## 📄 License

This project is distributed under the Apache License 2.0. See [LICENSE.md](LICENSE.md) for details.
