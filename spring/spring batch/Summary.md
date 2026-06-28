I read the GitHub note and prepared this as an interview-focused Spring Batch revision guide. Your notes cover Spring Batch fundamentals, CSV→DB flow, legacy XML + Servlet triggering, DB→DB transfer with stored procedures, chunk/tasklet internals, transactions, skip/retry, XML vs annotation config, production code, and even HTTP vs HTTPS as a peripheral topic. The official Spring Batch docs also confirm the same core model: Spring Batch is for enterprise bulk processing, not scheduling; it provides transaction management, restart, skip, resource management, and high-volume processing support. ([GitHub][1]) ([Home][2])

# 1. High-Level Summary

**Spring Batch** is a framework used to process large volumes of data in a reliable, transactional, restartable, and maintainable way. It is mainly used for offline/background processing, such as DB-to-DB transfer, CSV-to-DB import, report generation, reconciliation, salary processing, invoice generation, ETL jobs, and end-of-day banking jobs.

In interviews, explain it like this:

> Spring Batch is used when we need to read large data from a source, apply business rules, and write the processed output to a target system with transaction handling, restartability, job tracking, skip/retry, and fault tolerance.

The core flow is:

```text
Job
 └── Step
      ├── ItemReader
      ├── ItemProcessor
      └── ItemWriter
```

A `Job` is the complete batch process. A `Step` is one phase of the job. The `ItemReader` reads data, the `ItemProcessor` applies business logic, and the `ItemWriter` writes output. Spring Batch officially describes a job as having one or more steps, with each step having an `ItemReader`, optional `ItemProcessor`, and `ItemWriter`; metadata is stored in `JobRepository`. ([Home][3])

For your profile, the strongest project-based explanation is:

> In my project, Spring Batch is used for DB-to-DB data transfer. Jobs are triggered through a Servlet or scheduler, configured using XML, and process data using a reader, processor, and writer. The processor applies business conditions and invokes stored procedures for validation or CRUD logic. Although the project uses legacy XML configuration, the core Spring Batch execution model remains the same as modern Spring Boot batch jobs.

# 2. Important Concepts

## 2.1 Batch Application

A batch application is non-interactive and usually processes large data periodically or on demand. Examples are salary processing, invoice generation, data migration, daily reconciliation, report generation, and ETL jobs. Spring Batch is designed for these kinds of enterprise bulk operations and is not a scheduling framework; it works with schedulers such as Quartz, Control-M, cron, or Spring Scheduler. ([Home][2])

## 2.2 Why Spring Batch?

Without Spring Batch, we usually write manual loops, manual transaction handling, manual restart logic, manual error handling, and custom job status tracking.

Spring Batch gives:

```text
Restartability
Transaction management
JobRepository metadata
Skip/retry support
Chunk processing
Tasklet support
Scalability using partitioning/parallel processing
Listeners for monitoring
```

Your interview line:

> Spring Batch is not mainly about scheduling; it is about reliable batch processing. Scheduling only triggers the job.

## 2.3 Job

A `Job` represents the complete batch process.

Example:

```text
employeeSalaryProcessingJob
dailyDbTransferJob
invoiceGenerationJob
reconciliationJob
```

A job contains one or more steps and defines the overall flow.

## 2.4 Step

A `Step` is one independent phase inside a job.

Example:

```text
Step 1: Read pending records from DB1
Step 2: Apply business rules and stored procedure validations
Step 3: Write processed records into DB2
```

A step can be:

```text
Chunk-oriented step
Tasklet-based step
```

## 2.5 ItemReader

`ItemReader` reads data one item at a time.

Sources can be:

```text
Database
CSV
XML
JSON
REST API
Kafka
Flat file
```

Interview rule:

> Reader should only read data. Business logic should not be placed inside the reader.

In your project, a common reader is:

```text
JdbcCursorItemReader
```

It opens a DB cursor and streams rows instead of loading all records into memory.

## 2.6 ItemProcessor

`ItemProcessor` applies business logic.

It is used for:

```text
Validation
Transformation
Filtering
Stored procedure calls
Enrichment
Business condition checks
```

Important point:

```java
return null;
```

from processor means the record is filtered and will not go to the writer. Official docs also mention that returning `null` from processor indicates the item should not be written out. ([Home][3])

## 2.7 ItemWriter

`ItemWriter` writes processed data to the target system.

Targets can be:

```text
Database
File
REST API
Kafka
Cloud storage
```

Important point:

> Writer writes a chunk/list of items, not usually one item at a time.

## 2.8 JobRepository

`JobRepository` is the backbone of Spring Batch. It stores metadata related to job execution, step execution, status, start time, end time, commit count, read count, write count, rollback count, skip count, and execution context. Metadata tables include `BATCH_JOB_INSTANCE`, `BATCH_JOB_EXECUTION`, `BATCH_STEP_EXECUTION`, and execution context tables. ([Home][3]) ([Home][4])

Interview line:

> JobRepository enables restartability, monitoring, and tracking of batch executions.

## 2.9 JobLauncher / JobOperator

In older Spring Batch versions and many existing projects, `JobLauncher` is commonly used to start a job with `JobParameters`.

```java
jobLauncher.run(job, jobParameters);
```

Modern Spring Batch documentation also discusses job operations using `JobOperator`, especially for starting, stopping, restarting, and abandoning jobs. ([Home][3])

## 2.10 JobParameters

`JobParameters` identify a unique job instance.

Example:

```text
fileName=salary_2026_06_27.csv
businessDate=2026-06-27
run.id=12345
```

Important trap:

> Same job + same identifying parameters cannot be rerun as a new job instance.

Officially, `JobInstance = Job + identifying JobParameters`. ([Home][3])

## 2.11 Chunk-Oriented Processing

Chunk processing is the most important Spring Batch concept.

```text
Read N records
Process N records
Write N records
Commit transaction
```

If chunk size is 100 and total records are 1,00,000:

```text
1,00,000 / 100 = 1,000 chunks
```

It does not mean all 1,00,000 records are loaded into memory. Spring Batch reads data one by one, groups items up to the commit interval, writes the chunk, and commits the transaction. Official docs describe chunk processing as reading one item at a time, creating chunks, writing them within a transaction boundary, and committing when the commit interval is reached. ([Home][5])

## 2.12 Tasklet

A `Tasklet` is used for a single unit of work.

Examples:

```text
Clean staging table
Delete temporary files
Archive processed file
Call one stored procedure once
Send notification
Move file from input folder to archive folder
```

Interview line:

> Chunk is used for repeated item processing; Tasklet is used for one-time operations.

## 2.13 Skip

Skip means ignoring a bad record and continuing the job.

Use skip for:

```text
Invalid CSV record
Missing non-critical field
Known validation failure
Bad vendor/customer record that can be logged and fixed later
```

Spring Batch docs clearly state that skip should be a business decision; financial data may not be skippable, but vendor loading may allow malformed records to be skipped and logged. ([Home][6])

## 2.14 Retry

Retry means trying the same item again, usually for temporary failures.

Use retry for:

```text
Deadlock
Temporary DB timeout
Network issue
Temporary REST API failure
Lock wait timeout
```

Official Spring Batch retry logic allows retry limits and retryable exceptions, such as retrying a `DeadlockLoserDataAccessException`. ([Home][7])

## 2.15 Listeners

Listeners are hooks around job/step/read/process/write events.

Common listeners:

```text
JobExecutionListener
StepExecutionListener
ItemReadListener
ItemProcessListener
ItemWriteListener
SkipListener
RetryListener
```

Used for:

```text
Logging
Auditing
Monitoring
Sending notifications
Storing failed records
Updating dashboards
```

## 2.16 XML vs Java Config

Your notes correctly position XML as legacy but valid, and Java/annotation config as modern and Spring Boot friendly. Your best answer:

> I have worked with XML-based Spring Batch in legacy systems, but I am comfortable with modern Java-based Spring Boot Batch configuration. The core concepts remain the same; only the configuration style changes.

# 3. Interview-Focused Explanation

For a 7-year Java backend developer, do not answer Spring Batch like a beginner. Connect it with project reality.

Use this answer:

> In enterprise projects, Spring Batch is used for large-volume offline processing where normal REST APIs are not suitable. For example, in my project, we use batch jobs to transfer data from one database to another based on business conditions. The job is triggered through a Servlet or scheduler and configured using XML. The reader fetches eligible records from the source database, the processor applies business validations and calls stored procedures, and the writer persists the final output into the target database. We use chunk-oriented processing so that every chunk is processed inside a transaction. If a failure occurs, the current chunk rolls back, previous chunks remain committed, and the job can be restarted using JobRepository metadata.

For modern Spring Boot microservices:

> If we redesign it today, I would expose a secured REST endpoint to trigger the job, use Spring Boot Batch with Java configuration, store metadata in the batch repository, monitor execution through Actuator/Micrometer logs, and deploy it as a separate batch service on Docker/Kubernetes. React would only trigger or monitor the job; it would not run batch logic.

Strong architecture diagram:

```text
React/Admin UI / Scheduler / Control-M
        ↓
REST API / Servlet trigger
        ↓
JobLauncher / JobOperator
        ↓
Spring Batch Job
        ↓
Step
 ├── Reader: DB1 / File / API
 ├── Processor: validation + transformation + SP calls
 └── Writer: DB2 / File / Kafka
        ↓
JobRepository metadata tables
```

# 4. Top Interview Questions and Answers

## Beginner Level

### 1. What is Spring Batch?

Spring Batch is a framework for processing large volumes of data in a reliable and restartable way. It provides job execution, step execution, transaction management, metadata tracking, skip/retry, and restart support.

Real project use: DB-to-DB transfer, CSV import, reconciliation, report generation.

Common mistake: Saying Spring Batch is a scheduler. It is not; scheduler triggers the job.

---

### 2. What is a batch job?

A batch job is a background process that handles large data without user interaction.

Example:

```text
Daily salary calculation job
End-of-day banking settlement job
Monthly invoice generation job
```

---

### 3. What is the difference between Spring Batch and Spring Scheduler?

Spring Scheduler triggers code at a fixed time. Spring Batch processes large data reliably.

```text
Spring Scheduler = when to run
Spring Batch = how to process
```

In production, Spring Scheduler, Quartz, cron, Jenkins, or Control-M can trigger Spring Batch.

---

### 4. What are the main components of Spring Batch?

Main components:

```text
Job
Step
ItemReader
ItemProcessor
ItemWriter
JobRepository
JobLauncher / JobOperator
JobParameters
ExecutionContext
Listeners
```

---

### 5. What is a Job?

A `Job` is the complete batch workflow.

Example:

```text
customerImportJob
dailySettlementJob
dbToDbTransferJob
```

A job contains one or more steps.

---

### 6. What is a Step?

A `Step` is an independent phase of a job.

Example:

```text
Step 1: Read data
Step 2: Process data
Step 3: Write data
```

A step can be chunk-based or tasklet-based.

---

### 7. What is ItemReader?

`ItemReader` reads data one item at a time from a source like DB, file, API, XML, JSON, or queue.

Example:

```java
public class EmployeeReader implements ItemReader<Employee> {
    @Override
    public Employee read() {
        // read next employee
        return null; // means no more data
    }
}
```

Common mistake: Putting business logic inside reader.

---

### 8. What is ItemProcessor?

`ItemProcessor` applies business logic, validation, filtering, or transformation.

```java
public class EmployeeProcessor implements ItemProcessor<Employee, Employee> {
    @Override
    public Employee process(Employee emp) {
        if (emp.getSalary() <= 0) {
            return null; // filtered
        }
        emp.setDepartment(emp.getDepartment().toUpperCase());
        return emp;
    }
}
```

Follow-up: What happens if processor returns null?
Answer: Item is filtered and not passed to writer.

---

### 9. What is ItemWriter?

`ItemWriter` writes processed items to a target system.

```java
public class EmployeeWriter implements ItemWriter<Employee> {
    @Override
    public void write(Chunk<? extends Employee> employees) {
        employees.forEach(System.out::println);
    }
}
```

Writer receives a chunk/list of processed items.

---

### 10. What is JobRepository?

`JobRepository` stores metadata about job and step execution.

It stores:

```text
Job instance
Job execution
Step execution
Status
Read count
Write count
Commit count
Rollback count
Execution context
```

Real use: restart failed jobs and check production job status.

---

### 11. What is JobParameters?

`JobParameters` are runtime parameters passed while launching a job.

```java
JobParameters params = new JobParametersBuilder()
        .addString("businessDate", "2026-06-27")
        .addLong("run.id", System.currentTimeMillis())
        .toJobParameters();
```

Common issue: Job does not run again because same job parameters were used.

---

### 12. What is RunIdIncrementer?

`RunIdIncrementer` adds or increments a `run.id` parameter so the same job can run multiple times.

Use it carefully. In real business jobs, prefer meaningful parameters like `businessDate`, `fileName`, or `batchId`.

---

## Intermediate Level

### 13. What is chunk-oriented processing?

Chunk processing means reading, processing, writing, and committing records in groups.

Example:

```text
chunk size = 100
Read 100
Process 100
Write 100
Commit
```

Officially, Spring Batch reads items one by one, groups them until the commit interval, writes the chunk, and commits. ([Home][5])

---

### 14. Is chunk a physical collection?

Not exactly. Interview-safe answer:

> A chunk is a logical group of items controlled by Spring Batch and processed within one transaction boundary.

Spring Batch internally buffers records up to the commit interval.

---

### 15. If query returns 1 lakh records and chunk size is 100, how many chunks are created?

```text
100000 / 100 = 1000 chunks
```

All records are not processed in one chunk unless chunk size itself is 100000.

---

### 16. What happens if a record fails inside a chunk?

If skip is not configured, the current chunk transaction rolls back. Previous committed chunks remain safe.

Example:

```text
chunk size = 100
record 57 fails
records 1-100 in current chunk rollback
previous chunks remain committed
```

---

### 17. Why rollback the whole chunk?

Because chunk is the transaction boundary. Rolling back the whole chunk ensures atomicity, consistency, and restart safety.

Practical answer:

> If partial records inside a chunk are committed and some fail, restart becomes complex and data consistency can break. Chunk rollback gives a clean recovery point.

---

### 18. What is commit interval?

Commit interval is the number of items processed before transaction commit.

In XML:

```xml
<chunk reader="reader" processor="processor" writer="writer" commit-interval="100"/>
```

In Java config:

```java
.chunk(100, transactionManager)
```

---

### 19. How do you decide chunk size?

Chunk size depends on:

```text
Record size
DB performance
Transaction timeout
Memory usage
Rollback risk
Writer performance
Business criticality
```

Practical starting point:

```text
Small files: 100–500
Large DB loads: 500–5000
Heavy processor/SP calls: smaller chunk size
```

Common mistake: Setting chunk size too high and causing memory pressure or rollback of too many records.

---

### 20. What is Tasklet?

A `Tasklet` is a single unit of work executed inside a step.

Example:

```java
@Bean
public Step cleanupStep(JobRepository jobRepository,
                        PlatformTransactionManager txManager) {
    return new StepBuilder("cleanupStep", jobRepository)
            .tasklet((contribution, chunkContext) -> {
                System.out.println("Cleaning temp table");
                return RepeatStatus.FINISHED;
            }, txManager)
            .build();
}
```

Use it for cleanup, archive, delete, or one stored procedure call.

---

### 21. Chunk vs Tasklet?

```text
Chunk = repeated item processing
Tasklet = one-time operation
```

Use chunk for DB-to-DB transfers. Use tasklet for staging cleanup or file archival.

---

### 22. Can a job have both Tasklet and Chunk steps?

Yes.

```text
Step 1: Tasklet - clean staging table
Step 2: Chunk - process records
Step 3: Tasklet - archive file/send notification
```

This is common in production.

---

### 23. What is skip?

Skip means ignoring a bad record and continuing the job.

```java
.faultTolerant()
.skip(IllegalArgumentException.class)
.skipLimit(10)
```

Use it for bad data records that business allows you to ignore and log.

---

### 24. What is retry?

Retry means attempting the same failed item again.

```java
.faultTolerant()
.retry(DeadlockLoserDataAccessException.class)
.retryLimit(3)
```

Use it for temporary failures like deadlocks, DB timeout, or network glitches.

---

### 25. Skip vs Retry?

```text
Skip = bad data, ignore and continue
Retry = temporary issue, try again
```

Example:

```text
Invalid email -> skip
DB deadlock -> retry
```

---

### 26. Can skip and retry be used together?

Yes.

Example:

```text
Retry DB deadlock 3 times
If still failing and business allows, skip
Otherwise fail the step
```

Be careful: do not skip financial or critical transaction failures unless business explicitly allows it.

---

### 27. What is ExecutionContext?

`ExecutionContext` stores state during job/step execution.

Use:

```text
Restart from last known point
Store custom counters
Store file position
Store last processed ID
```

Official docs show step execution context storing position/state so the framework can restart after failure. ([Home][3])

---

### 28. What is restartability?

Restartability means a failed job can continue from the last successful checkpoint instead of starting from zero.

Example:

```text
1,00,000 records
chunk size = 1000
job fails after 40,000 committed records
restart resumes from last committed state
```

---

### 29. What is idempotency in Spring Batch?

Idempotency means reprocessing the same record does not create duplicate or incorrect output.

Ways to achieve it:

```text
Use unique business keys
Use processed flag
Use upsert/merge
Use batch_id
Check target before insert
Write audit logs
```

Very important in restart scenarios.

---

### 30. What is a listener in Spring Batch?

Listener hooks into lifecycle events.

Example:

```java
@Component
public class BatchJobListener implements JobExecutionListener {
    @Override
    public void beforeJob(JobExecution jobExecution) {
        System.out.println("Job started");
    }

    @Override
    public void afterJob(JobExecution jobExecution) {
        System.out.println("Job ended: " + jobExecution.getStatus());
    }
}
```

Used for logs, audit, alerting, and monitoring.

---

## Experienced Level

### 31. How does transaction handling work in Spring Batch?

In chunk-based steps, each chunk runs inside one transaction.

```text
Begin transaction
Read N
Process N
Write N
Commit
```

If exception occurs before commit, current chunk rolls back. Spring Batch also lets you configure transaction attributes such as isolation, propagation, and timeout. ([Home][8])

---

### 32. What transaction manager is used?

Depends on persistence technology:

```text
JDBC -> DataSourceTransactionManager
JPA/Hibernate -> JpaTransactionManager
JTA/multiple resources -> JtaTransactionManager
```

For DB-to-DB transfer, be careful if source DB and target DB are different. You may need separate transaction strategy or JTA, or design for idempotent recovery.

---

### 33. If stored procedure is called inside processor, does it participate in chunk transaction?

Usually yes, if it uses the same datasource and transaction manager with default propagation.

Interview answer:

> Stored procedure calls participate in the same chunk transaction unless configured with separate transaction propagation like REQUIRES_NEW.

Common mistake: Calling a stored procedure that commits internally. That can break rollback consistency.

---

### 34. What is propagation in Spring Batch transaction?

Propagation defines how a method participates in an existing transaction.

Common:

```text
REQUIRED -> join existing chunk transaction
REQUIRES_NEW -> start separate transaction
```

For batch consistency, `REQUIRED` is usually safer.

---

### 35. What happens if writer fails after processor succeeds?

The current chunk rolls back. On restart, items in that chunk can be reprocessed.

Processor should avoid irreversible side effects like sending emails or calling external APIs without idempotency.

---

### 36. What are common Spring Batch metadata tables?

Common tables:

```text
BATCH_JOB_INSTANCE
BATCH_JOB_EXECUTION
BATCH_JOB_EXECUTION_PARAMS
BATCH_STEP_EXECUTION
BATCH_JOB_EXECUTION_CONTEXT
BATCH_STEP_EXECUTION_CONTEXT
```

These tables store job/step status and counts. The official schema includes fields like status, commit count, read count, filter count, write count, skip count, and rollback count. ([Home][4])

---

### 37. How do you monitor a Spring Batch job in production?

Check:

```text
Application logs
BATCH_JOB_EXECUTION
BATCH_STEP_EXECUTION
Read/write/skip counts
Exit code and exit message
Actuator/Micrometer if Spring Boot
Grafana/Prometheus dashboards if integrated
Scheduler logs
DB locks and slow queries
```

---

### 38. What is partitioning?

Partitioning divides a large step into multiple smaller partitions and processes them in parallel. Spring Batch supports partitioning with manager/worker step execution; workers can be local threads or remote services. ([Home][9])

Use case:

```text
Process customer IDs 1-1M by partitioning ranges:
1-100000
100001-200000
...
```

---

### 39. What is remote chunking?

Remote chunking means manager reads data and sends chunks to remote workers for processing/writing.

Use when processing is heavy and needs distribution.

Common mistake: Using remote chunking too early. First optimize SQL, indexing, chunk size, and partitioning.

---

### 40. Spring Batch vs Kafka?

```text
Spring Batch = offline, bulk, transactional processing
Kafka = real-time/event-driven streaming
```

Example:

```text
Kafka: order created event
Spring Batch: nightly reconciliation of orders and payments
```

They can coexist in the same enterprise system.

---

### 41. Can Spring Batch be used in microservices?

Yes. A batch job can run as a separate Spring Boot microservice.

Example:

```text
batch-service
 ├── REST endpoint to trigger job
 ├── batch job config
 ├── metadata datasource
 └── business datasource
```

React can trigger or monitor the job through APIs, but React does not execute the batch.

---

### 42. How do you prevent duplicate records during restart?

Use:

```text
Unique constraints
Business keys
MERGE/UPSERT
processed_status column
batch_id
idempotent writer
staging table
```

Example:

```sql
MERGE INTO target_employee t
USING staging_employee s
ON (t.emp_id = s.emp_id)
WHEN MATCHED THEN UPDATE SET t.salary = s.salary
WHEN NOT MATCHED THEN INSERT (emp_id, salary) VALUES (s.emp_id, s.salary);
```

---

### 43. How do you handle bad records?

Use:

```text
Validation in processor
Skip policy
Skip listener
Error table
Error file
Alerting
Manual correction process
```

Do not silently ignore bad records.

---

### 44. How do you handle DB deadlocks?

Use:

```text
Retry on deadlock exception
Optimize indexes
Reduce chunk size
Avoid processing same rows in parallel
Consistent update order
Check isolation level
Avoid long transactions
```

---

### 45. XML vs Java Config in Spring Batch?

XML is common in legacy projects. Java config is modern, type-safe, Spring Boot friendly, and easier to refactor.

Interview answer:

> I can work with XML-based batch jobs in legacy systems, but for new development I prefer Java configuration with Spring Boot.

# 5. Scenario-Based Questions

### Scenario 1: DB-to-DB transfer with stored procedures

**Question:** Your batch reads records from DB1, calls stored procedures, and writes to DB2. How will you design it?

**Answer:**

```text
Reader: JdbcCursorItemReader from DB1
Processor: business condition + stored procedure call
Writer: JdbcBatchItemWriter to DB2
Transaction: chunk-level
Metadata: JobRepository
Failure handling: retry transient DB exceptions, skip only allowed data errors
```

---

### Scenario 2: Batch fails at record 73 with chunk size 100

Current chunk rolls back. Records from previous chunks remain committed. On restart, job resumes from the last committed checkpoint.

---

### Scenario 3: Query returns 1 lakh records

Use `JdbcCursorItemReader` or `JdbcPagingItemReader`.

```text
chunk size = 1000
chunks = 100
```

Do not load all records into memory.

---

### Scenario 4: Stored procedure commits internally

This is risky. If SP commits internally and writer fails later, chunk rollback cannot rollback SP changes.

Solution:

```text
Avoid commit inside SP
Keep SP in same transaction
Make operation idempotent
Use audit/staging tables
```

---

### Scenario 5: Batch job not running second time

Reason: Same job parameters.

Solution:

```java
new JobParametersBuilder()
    .addString("businessDate", "2026-06-27")
    .addLong("run.id", System.currentTimeMillis())
    .toJobParameters();
```

---

### Scenario 6: Invalid records in file

Use processor validation + skip listener.

```text
Invalid record -> skip
Log into error table
Continue processing
Generate rejection report
```

---

### Scenario 7: DB deadlock during writer

Use retry.

```java
.retry(DeadlockLoserDataAccessException.class)
.retryLimit(3)
```

Also tune indexes, chunk size, and update order.

---

### Scenario 8: Batch is slow in production

Check:

```text
SQL execution plan
Indexes
Chunk size
Fetch size
Writer batch size
Stored procedure performance
DB locks
Network latency
Parallelism/partitioning
```

---

### Scenario 9: Need to trigger batch from REST API

Design:

```text
POST /jobs/customer-import?businessDate=2026-06-27
Controller validates request
Service builds JobParameters
JobLauncher starts job asynchronously
Return jobExecutionId
GET /jobs/{id}/status checks metadata
```

Secure it using Spring Security and role-based access.

---

### Scenario 10: Batch service in Kubernetes

Use:

```text
Kubernetes CronJob for scheduled batch
Deployment + REST trigger for manual batch
ConfigMap for configuration
Secret for DB credentials
Persistent volume/S3 for files
Liveness/readiness probes carefully
Resource limits
Centralized logs
```

---

### Scenario 11: Jenkins triggers batch

Jenkins can trigger a shell command, REST endpoint, or Kubernetes job. Keep job parameters explicit and log the job execution ID.

---

### Scenario 12: Batch writes to Kafka

Use Spring Batch writer to publish processed records to Kafka only when you can handle idempotency and duplicate events.

Best practice:

```text
Write business data first
Publish event with unique eventId
Consumer should be idempotent
```

---

### Scenario 13: Batch with Hibernate/JPA is slow

Possible reasons:

```text
Persistence context grows too large
Flush/clear not configured
N+1 queries
Large transaction
No batching
```

Use JDBC writer for very high-volume inserts or configure JPA batch settings.

---

### Scenario 14: Oracle/DB2 duplicate key error

Handle using:

```text
Business key validation
MERGE/UPSERT
Skip duplicate if business allows
Move duplicate to error table
Fix source data
```

---

### Scenario 15: Job status shows STARTED but application crashed

The process may have died before updating metadata. Check scheduler logs, application logs, DB metadata, and restart policy. Decide whether to mark execution failed/abandoned based on business recovery rules.

# 6. Code Examples

## 6.1 Modern Spring Boot Batch Job

```java
@Configuration
@RequiredArgsConstructor
public class CustomerBatchConfig {

    private final JobRepository jobRepository;
    private final PlatformTransactionManager transactionManager;
    private final CustomerProcessor processor;

    @Bean
    public Job customerImportJob(Step customerImportStep) {
        return new JobBuilder("customerImportJob", jobRepository)
                .start(customerImportStep)
                .build();
    }

    @Bean
    public Step customerImportStep(ItemReader<CustomerCsvRow> reader,
                                   ItemWriter<Customer> writer) {
        return new StepBuilder("customerImportStep", jobRepository)
                .<CustomerCsvRow, Customer>chunk(500, transactionManager)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .faultTolerant()
                .skip(IllegalArgumentException.class)
                .skipLimit(50)
                .retry(DeadlockLoserDataAccessException.class)
                .retryLimit(3)
                .build();
    }
}
```

## 6.2 Processor with Validation

```java
@Component
public class CustomerProcessor implements ItemProcessor<CustomerCsvRow, Customer> {

    @Override
    public Customer process(CustomerCsvRow row) {
        if (row.getEmail() == null || !row.getEmail().contains("@")) {
            throw new IllegalArgumentException("Invalid email: " + row.getEmail());
        }

        Customer customer = new Customer();
        customer.setName(row.getName().trim());
        customer.setEmail(row.getEmail().trim().toLowerCase());
        customer.setCity(row.getCity());
        customer.setCreatedAt(LocalDateTime.now());

        return customer;
    }
}
```

## 6.3 Job Launcher from REST API

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/batch/jobs")
public class BatchJobController {

    private final JobLauncher jobLauncher;
    private final Job customerImportJob;

    @PostMapping("/customer-import")
    public ResponseEntity<String> runJob(@RequestParam String filePath) throws Exception {
        JobParameters params = new JobParametersBuilder()
                .addString("filePath", filePath)
                .addLong("run.id", System.currentTimeMillis())
                .toJobParameters();

        JobExecution execution = jobLauncher.run(customerImportJob, params);

        return ResponseEntity.ok("Job started with executionId=" + execution.getId());
    }
}
```

## 6.4 Legacy Servlet Trigger

```java
public class BatchTriggerServlet extends HttpServlet {

    private JobLauncher jobLauncher;
    private Job dbTransferJob;

    @Override
    public void init() {
        ApplicationContext context =
                WebApplicationContextUtils.getWebApplicationContext(getServletContext());

        this.jobLauncher = context.getBean(JobLauncher.class);
        this.dbTransferJob = context.getBean("dbTransferJob", Job.class);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        try {
            JobParameters params = new JobParametersBuilder()
                    .addString("businessDate", req.getParameter("businessDate"))
                    .addLong("run.id", System.currentTimeMillis())
                    .toJobParameters();

            JobExecution execution = jobLauncher.run(dbTransferJob, params);

            resp.getWriter().write("Batch started. ExecutionId=" + execution.getId());
        } catch (Exception e) {
            resp.setStatus(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
            resp.getWriter().write("Batch failed to start: " + e.getMessage());
        }
    }
}
```

## 6.5 XML-Based Chunk Step

```xml
<job id="dbTransferJob" xmlns="http://www.springframework.org/schema/batch">
    <step id="dbTransferStep">
        <tasklet transaction-manager="transactionManager">
            <chunk reader="sourceDbReader"
                   processor="transferProcessor"
                   writer="targetDbWriter"
                   commit-interval="100"/>
        </tasklet>
    </step>
</job>
```

## 6.6 RowMapper

```java
public class TransferDataRowMapper implements RowMapper<TransferData> {

    @Override
    public TransferData mapRow(ResultSet rs, int rowNum) throws SQLException {
        TransferData data = new TransferData();
        data.setRequestId(rs.getLong("REQUEST_ID"));
        data.setStatus(rs.getString("STATUS"));
        data.setAmount(rs.getBigDecimal("AMOUNT"));
        return data;
    }
}
```

## 6.7 Processor Calling Stored Procedure

```java
public class TransferDataProcessor implements ItemProcessor<TransferData, TransferData> {

    private final JdbcTemplate jdbcTemplate;

    public TransferDataProcessor(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public TransferData process(TransferData item) {
        Boolean valid = jdbcTemplate.queryForObject(
                "SELECT validate_request(?) FROM dual",
                Boolean.class,
                item.getRequestId()
        );

        if (Boolean.FALSE.equals(valid)) {
            return null; // filtered
        }

        item.setStatus("PROCESSED");
        return item;
    }
}
```

# 7. Difference Between / Comparison Questions

| Comparison                                   | Key Difference                                                                  | Use Case                                  |
| -------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------- |
| Spring Batch vs Scheduler                    | Batch processes data; scheduler triggers execution                              | Quartz triggers Spring Batch              |
| Job vs Step                                  | Job is complete workflow; step is one phase                                     | Import job has read/process/write step    |
| Reader vs Processor                          | Reader fetches data; processor applies logic                                    | DB reader + validation processor          |
| Processor vs Writer                          | Processor transforms; writer persists                                           | Validate customer, then insert            |
| Chunk vs Tasklet                             | Chunk is repeated item processing; tasklet is one-time work                     | DB transfer vs cleanup                    |
| Skip vs Retry                                | Skip ignores bad data; retry handles temporary failure                          | Invalid email vs DB deadlock              |
| JobParameters vs ExecutionContext            | Parameters identify/input job; context stores execution state                   | businessDate vs lastReadId                |
| JobRepository vs Business DB                 | Repository stores metadata; business DB stores domain data                      | batch status vs employee table            |
| XML Config vs Java Config                    | XML legacy; Java modern and type-safe                                           | Legacy apps vs Spring Boot                |
| JdbcCursorItemReader vs JdbcPagingItemReader | Cursor streams rows; paging reads page by page                                  | Simple DB stream vs large scalable paging |
| Spring Batch vs Kafka                        | Batch is offline bulk; Kafka is real-time streaming                             | nightly reconciliation vs order event     |
| Spring Batch vs REST API                     | Batch handles large background processing; REST handles request-response        | report generation vs get customer         |
| Per-record commit vs chunk commit            | Per-record safer but slow; chunk balances safety/performance                    | high-volume DB insert                     |
| Partitioning vs Multithreaded Step           | Partitioning divides data; multithreaded step parallelizes chunk processing     | ID ranges vs concurrent chunks            |
| Retry vs Restart                             | Retry happens during same execution; restart happens after failure and relaunch | DB timeout vs app crash                   |

# 8. Common Production Issues

## 8.1 Job not running again

Cause:

```text
Same JobParameters
```

Fix:

```text
Use unique run.id or correct businessDate/fileName
```

## 8.2 Duplicate records after restart

Cause:

```text
Writer not idempotent
No unique key
Reprocessing after rollback
```

Fix:

```text
Use MERGE/UPSERT
Unique constraints
processed_flag
batch_id
```

## 8.3 Batch is very slow

Check:

```text
SQL query plan
Indexes
Chunk size
Fetch size
Stored procedure execution time
Writer batch update
Network latency
DB locks
```

## 8.4 OutOfMemoryError

Cause:

```text
Loading too much data
Large chunk size
JPA persistence context not cleared
Bad reader implementation
```

Fix:

```text
Use cursor/paging reader
Reduce chunk size
Use JDBC writer
Flush/clear JPA context
```

## 8.5 Deadlock / lock timeout

Fix:

```text
Retry transient DB exceptions
Optimize indexes
Reduce chunk size
Avoid parallel update on same rows
Keep consistent update order
```

## 8.6 Stuck STARTED job

Check:

```text
Application crash
Server restart
Metadata tables
Scheduler logs
Thread dump
DB locks
```

Recovery depends on business rules: restart, abandon, or mark failed.

## 8.7 Skip limit exceeded

Cause:

```text
Too many bad records
Incorrect file format
Wrong validation rule
```

Fix:

```text
Check skip logs
Correct source data
Increase skip limit only after business approval
```

## 8.8 Stored procedure issue

Possible problems:

```text
SP commits internally
SP is slow
SP locks table
SP returns unexpected result
SP not idempotent
```

Fix:

```text
Check DB logs
Analyze execution plan
Avoid internal commit
Add retry only for transient errors
```

## 8.9 Metadata table issue

Cause:

```text
Missing Spring Batch tables
Wrong schema
Permission issue
Version mismatch
```

Fix:

```text
Create correct metadata schema
Check datasource configuration
Check DB permissions
```

## 8.10 REST-triggered job timeout

Do not keep HTTP request waiting for full job completion.

Better:

```text
Start job asynchronously
Return jobExecutionId
Provide status API
```

# 9. Best Practices

1. Keep reader, processor, and writer responsibilities separate.
2. Do not put business logic in reader.
3. Keep processor idempotent and avoid irreversible side effects.
4. Use meaningful JobParameters like `businessDate`, `fileName`, and `batchId`.
5. Choose chunk size based on DB performance, memory, rollback risk, and transaction timeout.
6. Use skip only when business allows data loss/rejection.
7. Use retry only for transient failures.
8. Log skipped records into an error table or rejection file.
9. Make writer idempotent using unique keys, merge, or processed flags.
10. Monitor `BATCH_JOB_EXECUTION` and `BATCH_STEP_EXECUTION`.
11. Use listeners for audit, logs, and alerts.
12. Do not trigger long jobs synchronously from REST without async handling.
13. Use scheduler only for triggering, not for processing logic.
14. Use partitioning only after basic SQL/chunk tuning.
15. Avoid stored procedures that commit internally inside chunk processing.
16. For legacy XML projects, confidently explain that only configuration style is old; Spring Batch concepts are the same.
17. For Spring Boot projects, prefer Java config.
18. Secure manual job trigger APIs with Spring Security.
19. In Kubernetes, prefer CronJob for scheduled batch jobs.
20. Always test restart scenarios before production.

# 10. Quick Revision Notes

```text
Spring Batch = large-volume offline processing framework.

Core:
Job -> Step -> Reader -> Processor -> Writer

Job:
Complete batch process.

Step:
One phase of job.

Reader:
Reads data from DB/file/API.

Processor:
Validation, transformation, filtering, SP calls.
return null = filtered record.

Writer:
Writes chunk to DB/file/API/Kafka.

Chunk:
Logical group of items processed in one transaction.
chunk size = commit interval.

If record fails:
Current chunk rolls back.
Previous chunks remain committed.

Skip:
Ignore bad record and continue.

Retry:
Try temporary failure again.

Tasklet:
Single operation like cleanup/archive/SP call once.

JobRepository:
Stores metadata and enables restart.

JobParameters:
Identify job instance.
Same parameters = same job instance.

XML vs Java Config:
XML = legacy.
Java Config = modern Spring Boot.
Core concepts are same.
```

# 11. 2-Minute Interview Answer

> Spring Batch is a framework used for large-volume, offline, and reliable data processing. It is commonly used for DB-to-DB transfer, file-to-DB import, reconciliation, report generation, salary processing, and ETL jobs. The core structure is Job, Step, ItemReader, ItemProcessor, and ItemWriter. A Job represents the complete batch process, and a Step represents one phase of that job. The reader reads records from a source like DB or file, the processor applies validation and business logic, and the writer writes the final output to the target system.
>
> In my project, Spring Batch is used for DB-to-DB data transfer based on business conditions. Jobs are triggered through Servlet or scheduler and configured using XML. The reader fetches eligible records from the source DB, the processor applies business logic and calls stored procedures, and the writer persists records into the target DB. We use chunk-oriented processing, where records are processed in chunks, and each chunk has its own transaction. If any record fails and skip is not configured, the current chunk rolls back, while previously committed chunks remain safe.
>
> Spring Batch also provides JobRepository metadata tables, which help track job status, step status, counts, failures, and restart information. For bad records, we can configure skip; for temporary failures like deadlocks or DB timeouts, we can configure retry. In modern Spring Boot systems, the same batch job can be exposed through a secured REST API or scheduled using cron, Jenkins, Control-M, or Kubernetes CronJob. React can be used only to trigger or monitor the job, not to execute batch logic.

# 12. Final Interview Preparation Checklist

Before saying Spring Batch is interview-ready, make sure you can explain:

```text
[ ] What Spring Batch is and when to use it
[ ] Why Spring Batch is not a scheduler
[ ] Job vs Step
[ ] Reader vs Processor vs Writer
[ ] Chunk processing with commit interval
[ ] What happens when a record fails inside chunk
[ ] Why chunk rollback is useful
[ ] How chunk size is decided
[ ] Tasklet and when to use it
[ ] Chunk vs Tasklet
[ ] JobRepository and metadata tables
[ ] JobParameters and why same job may not run again
[ ] ExecutionContext and restartability
[ ] Skip vs Retry
[ ] Listener usage
[ ] XML-based legacy configuration
[ ] Java config / Spring Boot Batch configuration
[ ] Servlet-triggered batch explanation
[ ] REST-triggered batch explanation
[ ] DB-to-DB transfer with stored procedure flow
[ ] Transaction manager role
[ ] Stored procedure transaction risk
[ ] Idempotency and duplicate prevention
[ ] Production debugging steps
[ ] Slow batch performance tuning
[ ] Deadlock/timeout handling
[ ] Monitoring through metadata tables/logs
[ ] Spring Batch vs Kafka
[ ] Spring Batch in microservices
[ ] Spring Batch deployment using Docker/Kubernetes/CronJob
```

Your strongest positioning is:

> “My real project uses legacy XML + Servlet-based Spring Batch for stable DB-to-DB batch processing, but I understand the same concept in modern Spring Boot, microservices, REST-triggered, and Kubernetes-deployed batch architectures.”

[1]: https://github.com/danishkhan141/dev-notes/raw/refs/heads/main/spring/spring%20batch/Spring%20Batch.md "raw.githubusercontent.com"
[2]: https://docs.spring.io/spring-batch/reference/spring-batch-intro.html "Spring Batch Introduction :: Spring Batch Reference"
[3]: https://docs.spring.io/spring-batch/reference/domain.html "The Domain Language of Batch :: Spring Batch Reference"
[4]: https://docs.spring.io/spring-batch/reference/schema-appendix.html "Meta-Data Schema :: Spring Batch Reference"
[5]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html "Chunk-oriented Processing :: Spring Batch Reference"
[6]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring-skip.html "Configuring Skip Logic :: Spring Batch Reference"
[7]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/retry-logic.html?utm_source=chatgpt.com "Configuring Retry Logic :: Spring Batch Reference"
[8]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/transaction-attributes.html "Transaction Attributes :: Spring Batch Reference"
[9]: https://docs.spring.io/spring-batch/reference/scalability.html "Scaling and Parallel Processing :: Spring Batch Reference"

```
1. Give me the example for the difference between job vs step as per the project, how job contains 1 or more steps?
2. How to achieve the skip/retry in spring batch both xml/java config, and which type of data should be skipped/retry?
3. Explain execution context/Listener, how to configure it?
```
```
## 1. Job vs Step — Project-Based Example

Think of **Job** as the complete batch process and **Step** as one phase inside that process.

Suppose your project has a requirement:

> Fetch pending request records from source DB, validate them, call stored procedure, insert/update into target DB, and finally update batch summary.

This complete process is one **Job**.

```text
Job: dailyRequestTransferJob

    Step 1: cleanupStagingStep
            Delete old temporary/staging records

    Step 2: requestTransferStep
            Read records from source DB
            Validate/process records
            Write records to target DB

    Step 3: summaryUpdateStep
            Update batch summary table / audit table

    Step 4: notificationStep
            Send success/failure mail or log final status
```

So:

```text
Job = complete business batch flow
Step = one unit/phase of that flow
```

A job can have **one step**:

```text
Job: csvToDbImportJob
    Step 1: read CSV -> validate -> insert DB
```

Or a job can have **multiple steps**:

```text
Job: monthlyInvoiceJob
    Step 1: Clean staging table
    Step 2: Load invoice data
    Step 3: Generate invoice file
    Step 4: Archive file
    Step 5: Send notification
```

### XML Example: Job with Multiple Steps

```xml
<job id="dailyRequestTransferJob" xmlns="http://www.springframework.org/schema/batch">

    <step id="cleanupStagingStep" next="requestTransferStep">
        <tasklet ref="cleanupTasklet"/>
    </step>

    <step id="requestTransferStep" next="summaryUpdateStep">
        <tasklet transaction-manager="transactionManager">
            <chunk reader="requestReader"
                   processor="requestProcessor"
                   writer="requestWriter"
                   commit-interval="100"/>
        </tasklet>
    </step>

    <step id="summaryUpdateStep" next="notificationStep">
        <tasklet ref="summaryUpdateTasklet"/>
    </step>

    <step id="notificationStep">
        <tasklet ref="notificationTasklet"/>
    </step>

</job>
```

### Java Config Example

```java
@Configuration
@EnableBatchProcessing
public class RequestTransferJobConfig {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job dailyRequestTransferJob() {
        return jobBuilderFactory.get("dailyRequestTransferJob")
                .start(cleanupStagingStep())
                .next(requestTransferStep())
                .next(summaryUpdateStep())
                .next(notificationStep())
                .build();
    }

    @Bean
    public Step cleanupStagingStep() {
        return stepBuilderFactory.get("cleanupStagingStep")
                .tasklet(cleanupTasklet())
                .build();
    }

    @Bean
    public Step requestTransferStep() {
        return stepBuilderFactory.get("requestTransferStep")
                .<SourceRequest, TargetRequest>chunk(100)
                .reader(requestReader())
                .processor(requestProcessor())
                .writer(requestWriter())
                .build();
    }

    @Bean
    public Step summaryUpdateStep() {
        return stepBuilderFactory.get("summaryUpdateStep")
                .tasklet(summaryUpdateTasklet())
                .build();
    }

    @Bean
    public Step notificationStep() {
        return stepBuilderFactory.get("notificationStep")
                .tasklet(notificationTasklet())
                .build();
    }
}
```

### Interview Answer

> In Spring Batch, a Job represents the complete batch process, and a Step represents one phase inside that job. For example, in a DB-to-DB transfer project, the job can be `dailyRequestTransferJob`. It may contain multiple steps: first cleaning the staging table, second reading records from source DB, processing business validations and writing to target DB, third updating summary tables, and fourth sending notification. So a job can have one or more steps depending on business flow.

---

# 2. Skip and Retry in Spring Batch

## What is Skip?

**Skip** means bad data is ignored and the job continues.

Example:

```text
Total records = 10,000
One record has invalid email / invalid amount / wrong date format
Instead of failing the full job, skip that record and continue
```

Use skip when the error is related to **bad data** and business allows you to continue.

### Data/Exceptions that can be skipped

| Situation                            |            Should Skip? | Reason                       |
| ------------------------------------ | ----------------------: | ---------------------------- |
| Invalid CSV row format               |                     Yes | Bad input data               |
| Missing non-critical field           |                     Yes | Can log and continue         |
| Invalid email/mobile                 |                     Yes | Validation failure           |
| Duplicate record                     | Yes, if business allows | Can move to error table      |
| Wrong date format                    |                     Yes | Bad source data              |
| Optional reference data missing      |               Sometimes | Depends on business          |
| NullPointerException due to code bug |                      No | Fix code                     |
| DB down                              |                      No | Retry/fail, not skip         |
| Payment amount mismatch              |              Usually no | Critical business data       |
| Security/authentication error        |                      No | Configuration/security issue |

## What is Retry?

**Retry** means Spring Batch tries the same failed operation again.

Use retry when the issue is **temporary/transient**.

### Data/Exceptions that should be retried

| Situation                          | Should Retry? | Reason                         |
| ---------------------------------- | ------------: | ------------------------------ |
| DB deadlock                        |           Yes | Temporary lock issue           |
| DB lock timeout                    |           Yes | May pass after retry           |
| Network timeout                    |           Yes | Temporary                      |
| REST API 503/504                   |           Yes | External service may recover   |
| Kafka broker temporary unavailable |           Yes | Infrastructure issue           |
| Connection reset                   |           Yes | Temporary                      |
| Invalid data                       |            No | Retrying will not fix bad data |
| NullPointerException               |            No | Code bug                       |
| SQL syntax error                   |            No | Query bug                      |
| Constraint violation               |    Usually no | Data/design issue              |

Simple rule:

```text
Bad data      -> Skip
Temporary error -> Retry
Code/config bug -> Fail
```

---

## Java Config: Skip and Retry

```java
@Bean
public Step requestTransferStep() {
    return stepBuilderFactory.get("requestTransferStep")
            .<SourceRequest, TargetRequest>chunk(100)
            .reader(requestReader())
            .processor(requestProcessor())
            .writer(requestWriter())
            .faultTolerant()

            // Skip bad data exceptions
            .skip(InvalidRecordException.class)
            .skip(FlatFileParseException.class)
            .skipLimit(50)

            // Retry temporary exceptions
            .retry(DeadlockLoserDataAccessException.class)
            .retry(CannotAcquireLockException.class)
            .retryLimit(3)

            .build();
}
```

### Custom Exception Example

```java
public class InvalidRecordException extends RuntimeException {

    public InvalidRecordException(String message) {
        super(message);
    }
}
```

### Processor Example with Skip

```java
@Component
public class RequestProcessor implements ItemProcessor<SourceRequest, TargetRequest> {

    @Override
    public TargetRequest process(SourceRequest request) {

        if (request.getRequestId() == null) {
            throw new InvalidRecordException("Request ID is missing");
        }

        if (request.getAmount() == null || request.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
            throw new InvalidRecordException("Invalid amount for request: " + request.getRequestId());
        }

        TargetRequest target = new TargetRequest();
        target.setRequestId(request.getRequestId());
        target.setAmount(request.getAmount());
        target.setStatus("PROCESSED");

        return target;
    }
}
```

Here `InvalidRecordException` will be skipped if it is configured in `.skip()`.

---

## XML Config: Skip and Retry

```xml
<step id="requestTransferStep" xmlns="http://www.springframework.org/schema/batch">
    <tasklet transaction-manager="transactionManager">
        <chunk reader="requestReader"
               processor="requestProcessor"
               writer="requestWriter"
               commit-interval="100"
               skip-limit="50"
               retry-limit="3">

            <skippable-exception-classes>
                <include class="com.example.batch.exception.InvalidRecordException"/>
                <include class="org.springframework.batch.item.file.FlatFileParseException"/>
            </skippable-exception-classes>

            <retryable-exception-classes>
                <include class="org.springframework.dao.DeadlockLoserDataAccessException"/>
                <include class="org.springframework.dao.CannotAcquireLockException"/>
            </retryable-exception-classes>

        </chunk>
    </tasklet>
</step>
```

### Interview Answer

> In Spring Batch, skip and retry are used for fault tolerance. Skip is used for bad records where business allows the job to continue, such as invalid CSV row, missing optional field, invalid date format, or duplicate data. Retry is used for temporary technical failures like DB deadlock, lock timeout, network timeout, REST API 503, or Kafka temporary failure. I would not skip code bugs, SQL syntax issues, security errors, or critical financial mismatches. In Java config, we use `.faultTolerant().skip().skipLimit().retry().retryLimit()`. In XML config, we configure `skip-limit`, `retry-limit`, `skippable-exception-classes`, and `retryable-exception-classes`.

---

# 3. ExecutionContext in Spring Batch

## What is ExecutionContext?

`ExecutionContext` is a key-value storage used by Spring Batch during job execution.

It stores runtime data like:

```text
last processed ID
file name
record count
custom status
error count
business date
temporary values needed between steps
```

There are two types:

```text
JobExecutionContext  -> shared at job level
StepExecutionContext -> specific to one step
```

## Example Use Case

Suppose your batch has 3 steps:

```text
Step 1: Read pending request count
Step 2: Process requests
Step 3: Update summary
```

Step 1 calculates total pending records:

```text
totalPendingRecords = 5000
```

You want Step 3 to use this value while updating summary.

That value can be stored in `ExecutionContext`.

---

## StepExecutionListener Example

```java
@Component
public class RequestStepListener implements StepExecutionListener {

    @Override
    public void beforeStep(StepExecution stepExecution) {
        System.out.println("Step started: " + stepExecution.getStepName());

        String businessDate = stepExecution
                .getJobParameters()
                .getString("businessDate");

        stepExecution.getExecutionContext()
                .putString("businessDate", businessDate);
    }

    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {

        int readCount = stepExecution.getReadCount();
        int writeCount = stepExecution.getWriteCount();
        int skipCount = stepExecution.getSkipCount();

        stepExecution.getExecutionContext().putInt("readCount", readCount);
        stepExecution.getExecutionContext().putInt("writeCount", writeCount);
        stepExecution.getExecutionContext().putInt("skipCount", skipCount);

        System.out.println("Read Count: " + readCount);
        System.out.println("Write Count: " + writeCount);
        System.out.println("Skip Count: " + skipCount);

        return ExitStatus.COMPLETED;
    }
}
```

## Configure Listener in Java Config

```java
@Bean
public Step requestTransferStep(RequestStepListener requestStepListener) {
    return stepBuilderFactory.get("requestTransferStep")
            .<SourceRequest, TargetRequest>chunk(100)
            .reader(requestReader())
            .processor(requestProcessor())
            .writer(requestWriter())
            .listener(requestStepListener)
            .build();
}
```

---

## JobExecutionListener Example

```java
@Component
public class RequestJobListener implements JobExecutionListener {

    @Override
    public void beforeJob(JobExecution jobExecution) {
        System.out.println("Job started: " + jobExecution.getJobInstance().getJobName());

        jobExecution.getExecutionContext()
                .putString("startedBy", "SYSTEM");
    }

    @Override
    public void afterJob(JobExecution jobExecution) {

        BatchStatus status = jobExecution.getStatus();

        System.out.println("Job completed with status: " + status);

        if (status == BatchStatus.COMPLETED) {
            System.out.println("Send success notification");
        } else {
            System.out.println("Send failure notification");
        }
    }
}
```

## Configure Job Listener in Java Config

```java
@Bean
public Job dailyRequestTransferJob(RequestJobListener requestJobListener) {
    return jobBuilderFactory.get("dailyRequestTransferJob")
            .listener(requestJobListener)
            .start(cleanupStagingStep())
            .next(requestTransferStep())
            .next(summaryUpdateStep())
            .build();
}
```

---

# 4. ExecutionContext Promotion Between Steps

Important interview point:

> StepExecutionContext is local to one step. If one step wants to pass data to another step, we need to promote that value to JobExecutionContext.

Example:

```java
@Bean
public ExecutionContextPromotionListener promotionListener() {
    ExecutionContextPromotionListener listener = new ExecutionContextPromotionListener();
    listener.setKeys(new String[]{"totalProcessedCount", "businessDate"});
    return listener;
}
```

Configure it in step:

```java
@Bean
public Step requestTransferStep() {
    return stepBuilderFactory.get("requestTransferStep")
            .<SourceRequest, TargetRequest>chunk(100)
            .reader(requestReader())
            .processor(requestProcessor())
            .writer(requestWriter())
            .listener(promotionListener())
            .build();
}
```

Now values from StepExecutionContext can be promoted to JobExecutionContext and used by the next step.

---

# 5. XML Config for Listener and ExecutionContext

## XML Listener Configuration

```xml
<bean id="requestJobListener" class="com.example.batch.listener.RequestJobListener"/>
<bean id="requestStepListener" class="com.example.batch.listener.RequestStepListener"/>
<bean id="skipRecordListener" class="com.example.batch.listener.SkipRecordListener"/>

<job id="dailyRequestTransferJob" xmlns="http://www.springframework.org/schema/batch">

    <listeners>
        <listener ref="requestJobListener"/>
    </listeners>

    <step id="requestTransferStep">
        <tasklet transaction-manager="transactionManager">
            <chunk reader="requestReader"
                   processor="requestProcessor"
                   writer="requestWriter"
                   commit-interval="100"
                   skip-limit="50">

                <skippable-exception-classes>
                    <include class="com.example.batch.exception.InvalidRecordException"/>
                </skippable-exception-classes>

            </chunk>

            <listeners>
                <listener ref="requestStepListener"/>
                <listener ref="skipRecordListener"/>
            </listeners>

        </tasklet>
    </step>

</job>
```

---

# 6. Skip Listener Example

Skip listener is very useful in production because skipped records should not disappear silently.

```java
@Component
public class SkipRecordListener implements SkipListener<SourceRequest, TargetRequest> {

    @Override
    public void onSkipInRead(Throwable t) {
        System.out.println("Skipped during read: " + t.getMessage());
    }

    @Override
    public void onSkipInProcess(SourceRequest item, Throwable t) {
        System.out.println("Skipped during process. RequestId: "
                + item.getRequestId()
                + ", reason: "
                + t.getMessage());

        // Real project:
        // Insert into BATCH_ERROR_LOG table
    }

    @Override
    public void onSkipInWrite(TargetRequest item, Throwable t) {
        System.out.println("Skipped during write. RequestId: "
                + item.getRequestId()
                + ", reason: "
                + t.getMessage());
    }
}
```

Configure in Java:

```java
@Bean
public Step requestTransferStep(SkipRecordListener skipRecordListener) {
    return stepBuilderFactory.get("requestTransferStep")
            .<SourceRequest, TargetRequest>chunk(100)
            .reader(requestReader())
            .processor(requestProcessor())
            .writer(requestWriter())
            .faultTolerant()
            .skip(InvalidRecordException.class)
            .skipLimit(50)
            .listener(skipRecordListener)
            .build();
}
```

---

# 7. Listener Types in Spring Batch

| Listener                | Purpose                | Real Project Use                    |
| ----------------------- | ---------------------- | ----------------------------------- |
| `JobExecutionListener`  | Before/after job       | Send notification, audit job status |
| `StepExecutionListener` | Before/after step      | Log read/write/skip count           |
| `ItemReadListener`      | Before/after read      | Debug source data issues            |
| `ItemProcessListener`   | Before/after process   | Debug transformation issues         |
| `ItemWriteListener`     | Before/after write     | Debug DB insert/update issues       |
| `SkipListener`          | When record is skipped | Store rejected records              |
| `ChunkListener`         | Before/after chunk     | Monitor chunk performance           |

---

# 8. Combined Real Project Flow

For your kind of project, you can explain like this:

```text
Job: dailyRequestTransferJob

Step 1: cleanupStagingStep
    Type: Tasklet
    Work: Delete old staging records

Step 2: requestTransferStep
    Type: Chunk
    Reader: Reads pending requests from source DB
    Processor: Validates request, applies business rules, calls stored procedure
    Writer: Inserts/updates records into target DB
    Skip: Invalid business records
    Retry: DB deadlock/lock timeout
    Listener: Logs read/write/skip count

Step 3: summaryUpdateStep
    Type: Tasklet
    Work: Update summary table using ExecutionContext values

Step 4: notificationStep
    Type: Tasklet
    Work: Send success/failure notification
```

---

# 9. Strong Interview Answer

> In Spring Batch, a Job is the complete batch process and it can contain one or more Steps. For example, in a DB-to-DB transfer project, the job can be `dailyRequestTransferJob`. It can have a cleanup step to clear staging data, a chunk step to read records from source DB, process validations and stored procedure logic, then write to target DB, and finally a summary or notification step.
>
> For fault tolerance, Spring Batch provides skip and retry. Skip is used for bad records, such as invalid file row, missing non-critical field, invalid date format, or duplicate record if business allows it. Retry is used for temporary technical failures like DB deadlock, lock timeout, network timeout, REST API 503, or temporary Kafka failure. I would not skip code bugs, SQL syntax errors, security issues, or critical financial mismatches.
>
> ExecutionContext is a key-value store maintained by Spring Batch at job and step level. It is useful for storing runtime state like last processed ID, business date, read/write count, or values required by later steps. Listeners are lifecycle hooks used before or after job, step, read, process, write, skip, or chunk events. In production, I use listeners for audit logging, monitoring, skipped record tracking, and notification.
```
```
1. Tasklet/chunk in xml/java config based application?
2. Skip/Retry in xml/java config based application?
3. How to achieve idempotency in spring batch?
```
```
## 1. Tasklet vs Chunk in XML / Java Config

Spring Batch has two main styles of step processing:

| Type        | Meaning                              | Best Use Case                                                                        |
| ----------- | ------------------------------------ | ------------------------------------------------------------------------------------ |
| **Tasklet** | One unit of work                     | Cleanup table, move file, archive file, call one stored procedure, send notification |
| **Chunk**   | Read-process-write records in groups | CSV to DB, DB to DB transfer, report data processing, large record processing        |

Spring Batch docs describe **chunk processing** as reading items one at a time, grouping them into chunks, writing the chunk, and committing the transaction after the commit interval. A **Tasklet** has an `execute()` method and is useful for one operation like SQL update, script call, stored procedure, or file operation. ([Home][1])

---

# A. Tasklet Example

## Real Project Example

Suppose your batch job starts by deleting old temporary records:

```text
Job: dailyRequestTransferJob

Step 1: cleanupStagingStep       -> Tasklet
Step 2: requestTransferStep      -> Chunk
Step 3: summaryUpdateStep        -> Tasklet
```

`cleanupStagingStep` is a **Tasklet** because it does not read 10,000 records one by one. It performs one direct operation.

---

## Java Config — Tasklet

```java
@Configuration
public class BatchConfig {

    @Bean
    public Step cleanupStagingStep(JobRepository jobRepository,
                                   PlatformTransactionManager transactionManager,
                                   JdbcTemplate jdbcTemplate) {

        return new StepBuilder("cleanupStagingStep", jobRepository)
                .tasklet((contribution, chunkContext) -> {

                    int deletedRows = jdbcTemplate.update(
                            "DELETE FROM STAGING_REQUEST WHERE CREATED_DATE < SYSDATE - 7"
                    );

                    System.out.println("Deleted staging rows: " + deletedRows);

                    return RepeatStatus.FINISHED;

                }, transactionManager)
                .build();
    }
}
```

### Interview Explanation

> I use Tasklet when the step has one specific task, for example cleaning a staging table, moving processed files to archive, calling one stored procedure, or sending notification. It is not suitable when I need to process records one by one. For record-based processing, I use chunk.

---

## XML Config — Tasklet

```xml
<bean id="cleanupTasklet" class="com.example.batch.tasklet.CleanupTasklet"/>

<batch:step id="cleanupStagingStep">
    <batch:tasklet ref="cleanupTasklet"
                   transaction-manager="transactionManager"/>
</batch:step>
```

```java
public class CleanupTasklet implements Tasklet {

    private JdbcTemplate jdbcTemplate;

    public CleanupTasklet(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public RepeatStatus execute(StepContribution contribution,
                                ChunkContext chunkContext) {

        int deletedRows = jdbcTemplate.update(
                "DELETE FROM STAGING_REQUEST WHERE CREATED_DATE < SYSDATE - 7"
        );

        System.out.println("Deleted rows: " + deletedRows);

        return RepeatStatus.FINISHED;
    }
}
```

---

# B. Chunk Example

## Real Project Example

Suppose you need to transfer records from source DB to target DB:

```text
Reader    -> read pending requests from source DB
Processor -> validate request + call stored procedure + transform data
Writer    -> insert/update into target DB
```

This is a **Chunk** step.

```text
Chunk size = 100

Read 100 records
Process 100 records
Write 100 records
Commit transaction
```

---

## Java Config — Chunk Step

```java
@Bean
public Step requestTransferStep(JobRepository jobRepository,
                                PlatformTransactionManager transactionManager,
                                ItemReader<SourceRequest> requestReader,
                                ItemProcessor<SourceRequest, TargetRequest> requestProcessor,
                                ItemWriter<TargetRequest> requestWriter) {

    return new StepBuilder("requestTransferStep", jobRepository)
            .<SourceRequest, TargetRequest>chunk(100, transactionManager)
            .reader(requestReader)
            .processor(requestProcessor)
            .writer(requestWriter)
            .build();
}
```

---

## XML Config — Chunk Step

```xml
<batch:step id="requestTransferStep">
    <batch:tasklet transaction-manager="transactionManager">
        <batch:chunk reader="requestReader"
                     processor="requestProcessor"
                     writer="requestWriter"
                     commit-interval="100"/>
    </batch:tasklet>
</batch:step>
```

---

## Job Containing Tasklet + Chunk Steps

## Java Config

```java
@Bean
public Job dailyRequestTransferJob(JobRepository jobRepository,
                                   Step cleanupStagingStep,
                                   Step requestTransferStep,
                                   Step summaryUpdateStep) {

    return new JobBuilder("dailyRequestTransferJob", jobRepository)
            .start(cleanupStagingStep)
            .next(requestTransferStep)
            .next(summaryUpdateStep)
            .build();
}
```

## XML Config

```xml
<batch:job id="dailyRequestTransferJob">

    <batch:step id="cleanupStagingStep" next="requestTransferStep">
        <batch:tasklet ref="cleanupTasklet"/>
    </batch:step>

    <batch:step id="requestTransferStep" next="summaryUpdateStep">
        <batch:tasklet transaction-manager="transactionManager">
            <batch:chunk reader="requestReader"
                         processor="requestProcessor"
                         writer="requestWriter"
                         commit-interval="100"/>
        </batch:tasklet>
    </batch:step>

    <batch:step id="summaryUpdateStep">
        <batch:tasklet ref="summaryUpdateTasklet"/>
    </batch:step>

</batch:job>
```

---

# 2. Skip / Retry in XML / Java Config

## A. Skip

**Skip** means bad records are ignored and the batch continues.

Use skip for **bad data**, not technical failures.

Examples:

```text
Invalid CSV row
Invalid date format
Missing optional field
Invalid mobile/email
Duplicate record, if business allows
Reference data missing, if business allows
```

Spring Batch docs clearly say skipping is a business decision. For example, financial data may not be skippable, but vendor/customer loading may allow invalid records to be skipped and reviewed later. ([Home][2])

---

## B. Retry

**Retry** means the same operation is tried again.

Use retry for **temporary/transient issues**.

Examples:

```text
DB deadlock
DB lock timeout
Temporary network issue
REST API 503/504
Kafka broker temporary unavailable
Connection reset
```

Spring Batch supports retry by configuring retryable exceptions and retry limits for an individual item. ([Home][3])

---

## Simple Interview Rule

```text
Bad Data           -> Skip
Temporary Failure  -> Retry
Code Bug           -> Fail
Critical Data      -> Fail
```

---

## Java Config — Skip + Retry

```java
@Bean
public Step requestTransferStep(JobRepository jobRepository,
                                PlatformTransactionManager transactionManager,
                                ItemReader<SourceRequest> requestReader,
                                ItemProcessor<SourceRequest, TargetRequest> requestProcessor,
                                ItemWriter<TargetRequest> requestWriter,
                                SkipRecordListener skipRecordListener) {

    return new StepBuilder("requestTransferStep", jobRepository)
            .<SourceRequest, TargetRequest>chunk(100, transactionManager)
            .reader(requestReader)
            .processor(requestProcessor)
            .writer(requestWriter)

            .faultTolerant()

            // Skip bad data
            .skip(InvalidRecordException.class)
            .skip(NumberFormatException.class)
            .skipLimit(50)

            // Retry temporary DB/infra issue
            .retry(DeadlockLoserDataAccessException.class)
            .retry(CannotAcquireLockException.class)
            .retryLimit(3)

            .listener(skipRecordListener)
            .build();
}
```

---

## Custom Skip Exception

```java
public class InvalidRecordException extends RuntimeException {

    public InvalidRecordException(String message) {
        super(message);
    }
}
```

---

## Processor Throwing Skippable Exception

```java
@Component
public class RequestProcessor implements ItemProcessor<SourceRequest, TargetRequest> {

    @Override
    public TargetRequest process(SourceRequest request) {

        if (request.getRequestId() == null) {
            throw new InvalidRecordException("Request ID is missing");
        }

        if (request.getAmount() == null ||
                request.getAmount().compareTo(BigDecimal.ZERO) <= 0) {
            throw new InvalidRecordException(
                    "Invalid amount for requestId: " + request.getRequestId()
            );
        }

        TargetRequest target = new TargetRequest();
        target.setRequestId(request.getRequestId());
        target.setAmount(request.getAmount());
        target.setStatus("PROCESSED");

        return target;
    }
}
```

If `InvalidRecordException` is thrown and skip is configured, Spring Batch skips that item and continues until skip limit is reached.

---

## XML Config — Skip + Retry

```xml
<batch:step id="requestTransferStep">
    <batch:tasklet transaction-manager="transactionManager">

        <batch:chunk reader="requestReader"
                     processor="requestProcessor"
                     writer="requestWriter"
                     commit-interval="100"
                     skip-limit="50"
                     retry-limit="3">

            <batch:skippable-exception-classes>
                <batch:include class="com.example.batch.exception.InvalidRecordException"/>
                <batch:include class="java.lang.NumberFormatException"/>
            </batch:skippable-exception-classes>

            <batch:retryable-exception-classes>
                <batch:include class="org.springframework.dao.DeadlockLoserDataAccessException"/>
                <batch:include class="org.springframework.dao.CannotAcquireLockException"/>
            </batch:retryable-exception-classes>

        </batch:chunk>

        <batch:listeners>
            <batch:listener ref="skipRecordListener"/>
        </batch:listeners>

    </batch:tasklet>
</batch:step>
```

---

## Skip Listener

In production, do not silently skip records. Always log them into an error table, rejection file, or monitoring system.

```java
@Component
public class SkipRecordListener implements SkipListener<SourceRequest, TargetRequest> {

    @Override
    public void onSkipInRead(Throwable t) {
        System.out.println("Skipped during read: " + t.getMessage());
    }

    @Override
    public void onSkipInProcess(SourceRequest item, Throwable t) {
        System.out.println("Skipped in processor. requestId="
                + item.getRequestId()
                + ", reason="
                + t.getMessage());

        // Real project:
        // insert into BATCH_ERROR_LOG table
    }

    @Override
    public void onSkipInWrite(TargetRequest item, Throwable t) {
        System.out.println("Skipped in writer. requestId="
                + item.getRequestId()
                + ", reason="
                + t.getMessage());
    }
}
```

Spring Batch guarantees that `SkipListener` is called once per skipped item and is called before transaction commit, which is useful when logging skipped records transactionally. ([Home][4])

---

## When Not to Skip

Do **not** skip these blindly:

```text
Payment amount mismatch
Tax calculation mismatch
Security/authentication error
SQL syntax error
NullPointerException due to code bug
Table not found
DB connection down
Wrong stored procedure logic
Critical business validation failure
```

These should usually fail the job.

---

## When Not to Retry

Do **not** retry these:

```text
Invalid input data
Wrong date format
Missing mandatory field
Duplicate key due to bad source data
SQL syntax error
NullPointerException
Invalid credentials
```

Retrying will not solve these issues.

---

# 3. How to Achieve Idempotency in Spring Batch?

## What is Idempotency?

**Idempotency** means running the same batch again should not create duplicate or incorrect data.

Simple meaning:

```text
Same input + same job rerun = same final result
```

This is very important because Spring Batch jobs can fail, restart, rollback, or reprocess records. Spring Batch docs mention that when a chunk rolls back, cached items may be reprocessed, so processors should be implemented idempotently. ([Home][5])

---

## Why Idempotency is Important?

Suppose your batch processes 10,000 records.

```text
Chunk size = 100
Job fails after 5,000 records
Job is restarted
```

If your writer blindly inserts records again, duplicates may happen.

Bad writer:

```sql
INSERT INTO TARGET_REQUEST (REQ_ID, STATUS)
VALUES (?, ?);
```

If the same `REQ_ID` is processed again, duplicate record issue may happen.

---

# Practical Ways to Achieve Idempotency

## 1. Use Unique Business Key

Add unique constraint on business columns.

Example:

```sql
ALTER TABLE TARGET_REQUEST
ADD CONSTRAINT UK_TARGET_REQUEST UNIQUE (REQ_ID, BUSINESS_DATE);
```

Now even if the same record is inserted twice, DB will prevent duplicate data.

Interview line:

> I always prefer DB-level protection because application-level checks alone are not enough in concurrent or restart scenarios.

---

## 2. Use UPSERT / MERGE Instead of Blind INSERT

Instead of blindly inserting, use `MERGE`.

### Oracle / DB2 Style Example

```sql
MERGE INTO TARGET_REQUEST tgt
USING (
    SELECT ? AS REQ_ID,
           ? AS BUSINESS_DATE,
           ? AS STATUS
    FROM dual
) src
ON (
    tgt.REQ_ID = src.REQ_ID
    AND tgt.BUSINESS_DATE = src.BUSINESS_DATE
)
WHEN MATCHED THEN
    UPDATE SET tgt.STATUS = src.STATUS
WHEN NOT MATCHED THEN
    INSERT (REQ_ID, BUSINESS_DATE, STATUS)
    VALUES (src.REQ_ID, src.BUSINESS_DATE, src.STATUS);
```

Meaning:

```text
If record exists    -> update it
If record not exists -> insert it
```

This is one of the best ways to make writer idempotent.

---

## 3. Use Processed Flag in Source Table

Add status in source table:

```text
PENDING
IN_PROGRESS
PROCESSED
FAILED
SKIPPED
```

Reader should pick only pending records:

```sql
SELECT *
FROM SOURCE_REQUEST
WHERE STATUS = 'PENDING'
ORDER BY REQ_ID;
```

After successful write, update source status:

```sql
UPDATE SOURCE_REQUEST
SET STATUS = 'PROCESSED'
WHERE REQ_ID = ?;
```

This prevents already processed records from being picked again.

---

## 4. Use Batch ID / Run ID

Add a `BATCH_ID` column in target or audit table.

```text
REQ_ID | BUSINESS_DATE | STATUS    | BATCH_ID
101    | 2026-06-28    | PROCESSED | BATCH_001
```

This helps in:

```text
Tracking which batch processed which record
Finding duplicate processing
Rollback/reconciliation
Debugging production issues
```

---

## 5. Use Staging Table

For complex enterprise jobs, use staging.

```text
Step 1: Load data into staging table
Step 2: Validate staging records
Step 3: Move valid records to target table using MERGE
Step 4: Mark invalid records as rejected
```

Example:

```text
SOURCE_FILE
   ↓
STAGING_REQUEST
   ↓ validation
TARGET_REQUEST
```

This is very useful in DB-to-DB, CSV-to-DB, and reconciliation jobs.

---

## 6. Make Processor Idempotent

Processor should not perform irreversible side effects.

Bad processor:

```java
@Override
public TargetRequest process(SourceRequest item) {
    sendEmail(item); // bad: may send duplicate email during retry/restart
    item.setStatus("PROCESSED"); // mutating original object may be risky
    return convert(item);
}
```

Better processor:

```java
@Override
public TargetRequest process(SourceRequest item) {

    TargetRequest target = new TargetRequest();
    target.setRequestId(item.getRequestId());
    target.setBusinessDate(item.getBusinessDate());
    target.setAmount(item.getAmount());
    target.setStatus("PROCESSED");

    return target;
}
```

Spring Batch docs also recommend that fault-tolerant processors should avoid changing the input item and should update only the result instance. ([Home][5])

---

## 7. Avoid External Side Effects Inside Processor

Avoid doing this inside processor:

```text
Sending email
Calling payment API
Calling third-party irreversible API
Publishing Kafka event without idempotency
Updating another DB outside transaction
```

If needed, use:

```text
Outbox table
Event ID
Idempotency key
Separate notification step
Post-processing after successful commit
```

---

## 8. Use Idempotency Key for External Calls

If your batch calls REST API or Kafka, pass a unique idempotency key.

Example:

```text
idempotencyKey = requestId + businessDate
```

For Kafka:

```text
eventId = requestId + "_" + businessDate
```

Consumer should ignore duplicate `eventId`.

---

## 9. Use Correct JobParameters

Do not rely only on `run.id`.

Better:

```java
JobParameters params = new JobParametersBuilder()
        .addString("businessDate", "2026-06-28")
        .addString("fileName", "request_20260628.csv")
        .addLong("run.id", System.currentTimeMillis())
        .toJobParameters();
```

For business uniqueness:

```text
businessDate + fileName
```

should identify the business run.

---

## 10. Be Careful with Multiple Datasources

If JobRepository and business DB are not in the same transaction, failure can happen after business data is committed but before metadata is updated. Spring Batch docs mention this can cause step re-execution and duplicate processing, so idempotent processing or external transaction management such as JTA may be needed. ([Home][6])

This is very important for your DB-to-DB project.

---

# Idempotent Writer Example

```java
@Component
public class RequestWriter implements ItemWriter<TargetRequest> {

    private final JdbcTemplate jdbcTemplate;

    public RequestWriter(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public void write(Chunk<? extends TargetRequest> chunk) {

        for (TargetRequest item : chunk) {

            jdbcTemplate.update(
                    """
                    MERGE INTO TARGET_REQUEST tgt
                    USING (
                        SELECT ? AS REQ_ID,
                               ? AS BUSINESS_DATE,
                               ? AS STATUS
                        FROM dual
                    ) src
                    ON (
                        tgt.REQ_ID = src.REQ_ID
                        AND tgt.BUSINESS_DATE = src.BUSINESS_DATE
                    )
                    WHEN MATCHED THEN
                        UPDATE SET tgt.STATUS = src.STATUS
                    WHEN NOT MATCHED THEN
                        INSERT (REQ_ID, BUSINESS_DATE, STATUS)
                        VALUES (src.REQ_ID, src.BUSINESS_DATE, src.STATUS)
                    """,
                    item.getRequestId(),
                    item.getBusinessDate(),
                    item.getStatus()
            );
        }
    }
}
```

---

# Idempotency in Your Project Style

For your kind of legacy DB-to-DB Spring Batch project, you can explain like this:

```text
Reader:
Reads only PENDING records from source DB.

Processor:
Validates data.
Calls stored procedure carefully.
Does not perform irreversible side effects.
Returns a new target object.

Writer:
Uses MERGE/UPSERT instead of blind INSERT.
Uses unique key like REQ_ID + BUSINESS_DATE.
Updates source status as PROCESSED after successful write.

JobRepository:
Tracks job and step execution.

Error Handling:
Bad records go to error table.
Temporary DB errors are retried.
Duplicate records are handled using unique key/MERGE.
```

---

# Strong Interview Answer

> In Spring Batch, I use Tasklet for one-time operations like cleanup, file movement, calling one stored procedure, or sending notification. I use chunk processing when I need to process records one by one, like reading pending records from source DB, validating them in processor, and writing to target DB. In XML configuration, tasklet and chunk are configured using batch namespace, while in Java config, we use `StepBuilder` with either `.tasklet()` or `.chunk()`.
>
> For fault tolerance, I use skip for bad data and retry for temporary technical issues. For example, invalid date format, invalid CSV row, or missing optional field can be skipped if business allows it. DB deadlock, lock timeout, network timeout, or REST API 503 should be retried. I never skip code bugs, SQL syntax errors, authentication errors, or critical financial mismatches.
>
> For idempotency, I make sure rerunning or restarting the job does not create duplicate data. I use unique business keys, MERGE/UPSERT instead of blind insert, processed flags, batch IDs, staging tables, and idempotent processors. This is especially important because after rollback or restart, Spring Batch may reprocess records. In DB-to-DB jobs, idempotency is mandatory to avoid duplicate inserts and inconsistent target data.

[1]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html?utm_source=chatgpt.com "Chunk-oriented Processing :: Spring Batch Reference"
[2]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring-skip.html?utm_source=chatgpt.com "Configuring Skip Logic :: Spring Batch Reference"
[3]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/retry-logic.html?utm_source=chatgpt.com "Configuring Retry Logic :: Spring Batch Reference"
[4]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/intercepting-execution.html?utm_source=chatgpt.com "Intercepting Step Execution :: Spring Batch Reference"
[5]: https://docs.spring.io/spring-batch/reference/processor.html?utm_source=chatgpt.com "Item processing :: Spring Batch Reference"
[6]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring.html?utm_source=chatgpt.com "Configuring a Step :: Spring Batch Reference"
```
```
1. How to access spring batch metadata tables?
2. What is meant by database deadlocks?
```
```
## 1. How to access Spring Batch metadata tables?

Spring Batch stores job/step execution details in **metadata tables**. These tables help with job tracking, restartability, status checking, failure analysis, read/write count, skip count, rollback count, etc. Official Spring Batch docs define metadata tables like `BATCH_JOB_INSTANCE`, `BATCH_JOB_EXECUTION`, `BATCH_STEP_EXECUTION`, and execution-context tables. ([Home][1])

---

# A. Important Spring Batch Metadata Tables

| Table                          | Purpose                                                  |
| ------------------------------ | -------------------------------------------------------- |
| `BATCH_JOB_INSTANCE`           | Stores unique job instance details                       |
| `BATCH_JOB_EXECUTION`          | Stores each job execution/run status                     |
| `BATCH_JOB_EXECUTION_PARAMS`   | Stores job parameters                                    |
| `BATCH_STEP_EXECUTION`         | Stores step-level status, read/write/skip/rollback count |
| `BATCH_JOB_EXECUTION_CONTEXT`  | Stores job-level execution context                       |
| `BATCH_STEP_EXECUTION_CONTEXT` | Stores step-level execution context                      |

Simple relation:

```text
BATCH_JOB_INSTANCE
        ↓
BATCH_JOB_EXECUTION
        ↓
BATCH_STEP_EXECUTION
```

---

# B. Access Using SQL Queries

In production, most commonly we access these tables through SQL Developer, DB2 Data Studio, DBeaver, MySQL Workbench, etc.

## 1. Check latest job executions

```sql
SELECT 
    JE.JOB_EXECUTION_ID,
    JI.JOB_NAME,
    JE.STATUS,
    JE.START_TIME,
    JE.END_TIME,
    JE.EXIT_CODE,
    JE.EXIT_MESSAGE
FROM BATCH_JOB_INSTANCE JI
JOIN BATCH_JOB_EXECUTION JE
    ON JI.JOB_INSTANCE_ID = JE.JOB_INSTANCE_ID
ORDER BY JE.START_TIME DESC;
```

---

## 2. Check step execution details

```sql
SELECT
    SE.STEP_EXECUTION_ID,
    SE.JOB_EXECUTION_ID,
    SE.STEP_NAME,
    SE.STATUS,
    SE.READ_COUNT,
    SE.WRITE_COUNT,
    SE.FILTER_COUNT,
    SE.COMMIT_COUNT,
    SE.ROLLBACK_COUNT,
    SE.READ_SKIP_COUNT,
    SE.PROCESS_SKIP_COUNT,
    SE.WRITE_SKIP_COUNT,
    SE.START_TIME,
    SE.END_TIME,
    SE.EXIT_CODE,
    SE.EXIT_MESSAGE
FROM BATCH_STEP_EXECUTION SE
ORDER BY SE.START_TIME DESC;
```

This is very useful in production.

Example:

```text
READ_COUNT = 10000
WRITE_COUNT = 9950
PROCESS_SKIP_COUNT = 50
STATUS = COMPLETED
```

Meaning:

```text
10,000 records read
9,950 records written
50 records skipped during processing
Job completed successfully
```

---

## 3. Find failed jobs

```sql
SELECT 
    JI.JOB_NAME,
    JE.JOB_EXECUTION_ID,
    JE.STATUS,
    JE.START_TIME,
    JE.END_TIME,
    JE.EXIT_CODE,
    JE.EXIT_MESSAGE
FROM BATCH_JOB_INSTANCE JI
JOIN BATCH_JOB_EXECUTION JE
    ON JI.JOB_INSTANCE_ID = JE.JOB_INSTANCE_ID
WHERE JE.STATUS = 'FAILED'
ORDER BY JE.START_TIME DESC;
```

---

## 4. Check failed step reason

```sql
SELECT
    STEP_NAME,
    STATUS,
    EXIT_CODE,
    EXIT_MESSAGE,
    READ_COUNT,
    WRITE_COUNT,
    ROLLBACK_COUNT
FROM BATCH_STEP_EXECUTION
WHERE JOB_EXECUTION_ID = 101;
```

---

## 5. Check job parameters

```sql
SELECT *
FROM BATCH_JOB_EXECUTION_PARAMS
WHERE JOB_EXECUTION_ID = 101;
```

Example data:

```text
businessDate = 2026-06-28
fileName = request_20260628.csv
run.id = 123456789
```

---

# C. Access Metadata Using Java Code

In modern Spring Batch, you can access metadata using `JobExplorer`.

## Example: Read Job Execution Status

```java
@Service
public class BatchStatusService {

    private final JobExplorer jobExplorer;

    public BatchStatusService(JobExplorer jobExplorer) {
        this.jobExplorer = jobExplorer;
    }

    public String getJobStatus(Long jobExecutionId) {

        JobExecution jobExecution = jobExplorer.getJobExecution(jobExecutionId);

        if (jobExecution == null) {
            return "No job execution found for id: " + jobExecutionId;
        }

        return "Job Status: " + jobExecution.getStatus()
                + ", Exit Status: " + jobExecution.getExitStatus().getExitCode();
    }
}
```

---

## REST API to Check Batch Status

```java
@RestController
@RequestMapping("/batch")
public class BatchStatusController {

    private final BatchStatusService batchStatusService;

    public BatchStatusController(BatchStatusService batchStatusService) {
        this.batchStatusService = batchStatusService;
    }

    @GetMapping("/status/{jobExecutionId}")
    public ResponseEntity<String> getStatus(@PathVariable Long jobExecutionId) {
        return ResponseEntity.ok(batchStatusService.getJobStatus(jobExecutionId));
    }
}
```

Example:

```text
GET /batch/status/101
```

Response:

```text
Job Status: COMPLETED, Exit Status: COMPLETED
```

---

# D. Access Metadata Using JobRepository / JobExplorer / JobOperator

| Component       | Usage                              |
| --------------- | ---------------------------------- |
| `JobRepository` | Stores job metadata                |
| `JobExplorer`   | Reads job metadata                 |
| `JobOperator`   | Start, stop, restart, abandon jobs |
| SQL queries     | Production debugging               |

Interview answer:

> In production, we usually access Spring Batch metadata tables directly using SQL for debugging. For application-level access, we use `JobExplorer` to read job and step execution details. `JobRepository` is internally used by Spring Batch to persist metadata, and `JobOperator` can be used to start, stop, restart, or abandon jobs.

---

# E. Important Warning

Do **not** casually update metadata tables manually.

Avoid this unless it is a production recovery case approved by the team:

```sql
UPDATE BATCH_JOB_EXECUTION 
SET STATUS = 'FAILED'
WHERE JOB_EXECUTION_ID = 101;
```

Why?

Because manual changes can break restartability and job consistency.

Better approach:

```text
Use JobOperator
Use proper restart
Check logs
Check scheduler history
Check DB metadata
Take DBA/team approval before manual update
```

---

# 2. What is meant by database deadlock?

A **database deadlock** happens when two or more transactions are waiting for each other’s locked resources, and none of them can proceed. Oracle defines a deadlock as a situation where two or more users are waiting for data locked by each other, preventing transactions from continuing. ([Oracle Documentation][2])

---

## Simple Example

Suppose two transactions are running at the same time.

### Transaction 1

```sql
UPDATE ACCOUNT SET BALANCE = BALANCE - 100 WHERE ACCOUNT_ID = 1;
```

Transaction 1 locks `ACCOUNT_ID = 1`.

Then it tries:

```sql
UPDATE ACCOUNT SET BALANCE = BALANCE + 100 WHERE ACCOUNT_ID = 2;
```

But `ACCOUNT_ID = 2` is locked by Transaction 2.

---

### Transaction 2

```sql
UPDATE ACCOUNT SET BALANCE = BALANCE - 50 WHERE ACCOUNT_ID = 2;
```

Transaction 2 locks `ACCOUNT_ID = 2`.

Then it tries:

```sql
UPDATE ACCOUNT SET BALANCE = BALANCE + 50 WHERE ACCOUNT_ID = 1;
```

But `ACCOUNT_ID = 1` is locked by Transaction 1.

---

## Deadlock Situation

```text
Transaction 1 locked ACCOUNT_ID = 1
Transaction 1 is waiting for ACCOUNT_ID = 2

Transaction 2 locked ACCOUNT_ID = 2
Transaction 2 is waiting for ACCOUNT_ID = 1

Both are waiting for each other.
This is a deadlock.
```

Database will usually detect this and kill/rollback one transaction.

---

# Deadlock in Spring Batch Project

In Spring Batch, deadlock can happen when multiple chunks, jobs, or threads update the same tables/rows.

Example:

```text
Job A processing request IDs: 1, 2, 3, 4
Job B processing request IDs: 4, 3, 2, 1
```

If both jobs update records in different order, locks can conflict.

---

## Common Causes of Deadlock

| Cause                                    | Example                                        |
| ---------------------------------------- | ---------------------------------------------- |
| Multiple transactions updating same rows | Two batch jobs updating same request records   |
| Different update order                   | Job 1 updates A then B, Job 2 updates B then A |
| Large chunk size                         | Transaction holds locks for long time          |
| Missing indexes                          | DB locks more rows/table scans                 |
| Long-running stored procedure            | Locks remain active for longer                 |
| Parallel batch processing                | Multiple threads touching same data            |
| High isolation level                     | More strict locking                            |
| Uncommitted transactions                 | One process holds lock too long                |

---

# How to Solve / Reduce Deadlocks

## 1. Update records in same order

Bad:

```text
Job 1: update ID 1 then ID 2
Job 2: update ID 2 then ID 1
```

Better:

```text
Both jobs update records in ascending ID order
```

SQL:

```sql
SELECT *
FROM SOURCE_REQUEST
WHERE STATUS = 'PENDING'
ORDER BY REQUEST_ID;
```

---

## 2. Reduce chunk size

If chunk size is too large:

```text
chunk size = 5000
```

Locks are held for a long time.

Better:

```text
chunk size = 100 or 500
```

depending on data volume and DB performance.

---

## 3. Add proper indexes

If your query is:

```sql
SELECT *
FROM SOURCE_REQUEST
WHERE STATUS = 'PENDING'
AND BUSINESS_DATE = ?
```

Then useful index may be:

```sql
CREATE INDEX IDX_SOURCE_REQ_STATUS_DATE
ON SOURCE_REQUEST (STATUS, BUSINESS_DATE);
```

Without index, DB may scan many rows and lock more data.

---

## 4. Keep transactions short

Avoid heavy logic inside one transaction:

```text
Long stored procedure
External API call
Large chunk
Complex joins
```

Long transactions increase lock time.

---

## 5. Avoid parallel update on same records

If partitioning or multi-threaded batch is used, divide data properly:

```text
Thread 1: request_id 1 - 10000
Thread 2: request_id 10001 - 20000
Thread 3: request_id 20001 - 30000
```

Do not allow all threads to update the same range.

---

## 6. Use retry for deadlock

Deadlock is usually a temporary issue, so retry is valid.

### Java Config

```java
@Bean
public Step requestTransferStep(JobRepository jobRepository,
                                PlatformTransactionManager transactionManager,
                                ItemReader<SourceRequest> reader,
                                ItemProcessor<SourceRequest, TargetRequest> processor,
                                ItemWriter<TargetRequest> writer) {

    return new StepBuilder("requestTransferStep", jobRepository)
            .<SourceRequest, TargetRequest>chunk(100, transactionManager)
            .reader(reader)
            .processor(processor)
            .writer(writer)
            .faultTolerant()
            .retry(DeadlockLoserDataAccessException.class)
            .retry(CannotAcquireLockException.class)
            .retryLimit(3)
            .build();
}
```

### XML Config

```xml
<batch:step id="requestTransferStep">
    <batch:tasklet transaction-manager="transactionManager">
        <batch:chunk reader="requestReader"
                     processor="requestProcessor"
                     writer="requestWriter"
                     commit-interval="100"
                     retry-limit="3">

            <batch:retryable-exception-classes>
                <batch:include class="org.springframework.dao.DeadlockLoserDataAccessException"/>
                <batch:include class="org.springframework.dao.CannotAcquireLockException"/>
            </batch:retryable-exception-classes>

        </batch:chunk>
    </batch:tasklet>
</batch:step>
```

---

# Interview Answer

> Spring Batch metadata tables are used to track job and step execution details. Important tables are `BATCH_JOB_INSTANCE`, `BATCH_JOB_EXECUTION`, `BATCH_JOB_EXECUTION_PARAMS`, `BATCH_STEP_EXECUTION`, and execution context tables. In production, I can query these tables to check job status, failed steps, read count, write count, skip count, rollback count, and exit messages. In Java, I can use `JobExplorer` to read metadata and `JobOperator` to manage jobs.
>
> A database deadlock happens when two or more transactions hold locks on different resources and each transaction waits for the other to release its lock. For example, Transaction 1 locks row A and waits for row B, while Transaction 2 locks row B and waits for row A. In Spring Batch, this can happen when multiple jobs or chunks update the same tables/rows in different order. To solve it, we keep update order consistent, reduce chunk size, add proper indexes, keep transactions short, avoid overlapping parallel processing, and configure retry for deadlock exceptions.

[1]: https://docs.spring.io/spring-batch/reference/schema-appendix.html?utm_source=chatgpt.com "Meta-Data Schema :: Spring Batch Reference"
[2]: https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/data-concurrency-and-consistency.html?utm_source=chatgpt.com "Data Concurrency and Consistency"
```