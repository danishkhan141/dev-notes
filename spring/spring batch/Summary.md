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
