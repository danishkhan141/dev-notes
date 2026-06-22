## Spring Batch — Top 50 Interview Questions with Professional Answers

Spring Batch is used for **large-volume, scheduled/offline data processing**, such as reading records from a file/database/API, transforming them, and writing them to another system. Official Spring Batch docs describe the core model around **Job, Step, ItemReader, ItemProcessor, ItemWriter, JobLauncher, and JobRepository**, and its most common style is **chunk-oriented processing**. ([Home][1])

---

# 1. What is Spring Batch?

**Spring Batch** is a framework used to build robust batch processing applications in Java.

It is useful when we need to process large amounts of data without user interaction.

Example use cases:

```text
CSV → Database
Database → CSV
One DB → Another DB
Daily report generation
Billing calculation
Transaction reconciliation
Data migration
```

Spring Batch gives ready-made support for:

```text
Job execution
Restartability
Transaction management
Skip/retry
Chunk processing
Metadata storage
Listeners
Parallel processing
```

---

# 2. What is batch processing?

Batch processing means processing a large amount of data in groups, usually without real-time user interaction.

Example:

```text
At 12 AM, process all pending customer transactions and generate a report.
```

It is different from online processing.

| Batch Processing           | Online Processing               |
| -------------------------- | ------------------------------- |
| Runs in background         | Runs immediately                |
| Processes large data       | Processes one request at a time |
| Example: salary processing | Example: login API              |
| Can be scheduled           | Triggered by user/API           |

---

# 3. What are the main components of Spring Batch?

Main components are:

```text
Job
Step
JobLauncher
JobRepository
JobParameters
ItemReader
ItemProcessor
ItemWriter
ExecutionContext
```

Simple flow:

```text
JobLauncher → Job → Step → Reader → Processor → Writer → JobRepository
```

A **Job** can contain one or more **Steps**, and a Step commonly uses Reader, Processor, and Writer. ([Home][2])

---

# 4. What is a Job in Spring Batch?

A **Job** is the complete batch process.

Example:

```text
Import employee data from CSV to database
```

This complete process is one Job.

A Job can have multiple Steps:

```text
Job: employeeImportJob
    Step 1: Read CSV and save employees
    Step 2: Generate summary report
    Step 3: Send notification
```

---

# 5. What is a Step in Spring Batch?

A **Step** is an individual phase inside a Job.

Example:

```text
Job: Monthly salary processing

Step 1: Read employee salary data
Step 2: Calculate tax
Step 3: Generate payslip
Step 4: Send email
```

Each Step should represent one clear responsibility.

---

# 6. What is JobLauncher?

**JobLauncher** is used to start a Spring Batch Job.

Example:

```java
jobLauncher.run(employeeImportJob, jobParameters);
```

It takes two things:

```text
1. Job
2. JobParameters
```

Interview answer:

> JobLauncher is responsible for launching a batch job with given parameters. It delegates execution details and stores metadata through JobRepository.

---

# 7. What is JobRepository?

**JobRepository** stores metadata of batch jobs.

It stores information like:

```text
Job status
Step status
Start time
End time
Job parameters
Execution context
Read count
Write count
Skip count
Failure details
```

This metadata helps in:

```text
Restarting failed jobs
Monitoring executions
Preventing duplicate execution
Debugging failures
```

In modern Spring Batch configuration, JobRepository is an important infrastructure component used while creating Jobs and Steps. ([Home][3])

---

# 8. What is JobInstance?

A **JobInstance** represents a logical run of a Job with specific parameters.

Example:

```text
Job Name: employeeImportJob
Parameter: date=2026-06-22
```

This combination creates one JobInstance.

If you run the same job again with the same identifying parameters, Spring Batch treats it as the same JobInstance.

---

# 9. What is JobExecution?

**JobExecution** represents one actual execution attempt of a JobInstance.

Example:

```text
JobInstance: employeeImportJob + date=2026-06-22

Attempt 1: Failed
Attempt 2: Completed
```

Here, both attempts are separate **JobExecutions**, but they belong to the same **JobInstance**.

---

# 10. Difference between JobInstance and JobExecution?

| Concept      | Meaning                                |
| ------------ | -------------------------------------- |
| JobInstance  | Logical job run with unique parameters |
| JobExecution | Actual attempt to run that JobInstance |

Example:

```text
employeeImportJob + date=2026-06-22 = JobInstance

First run failed = JobExecution 1
Restarted and completed = JobExecution 2
```

---

# 11. What is StepExecution?

**StepExecution** stores the runtime details of a Step.

It contains:

```text
Step start time
Step end time
Read count
Write count
Commit count
Rollback count
Skip count
Status
Exception details
```

Example:

```text
Step: importEmployeeStep
Read count: 1000
Write count: 980
Skip count: 20
Status: COMPLETED
```

---

# 12. What are JobParameters?

**JobParameters** are values passed to a Job during execution.

Example:

```text
fileName=employees.csv
runDate=2026-06-22
country=IN
```

Java example:

```java
JobParameters params = new JobParametersBuilder()
        .addString("fileName", "employees.csv")
        .addLocalDate("runDate", LocalDate.now())
        .toJobParameters();

jobLauncher.run(employeeImportJob, params);
```

Important interview point:

> JobParameters help uniquely identify a JobInstance.

---

# 13. What is ExecutionContext?

**ExecutionContext** is a key-value storage used by Spring Batch to store state during job/step execution.

Example:

```text
lastProcessedId = 5000
currentFileName = employees.csv
```

It is very useful for restartability.

Example:

```java
stepExecution.getExecutionContext().putLong("lastProcessedId", 5000L);
```

If job fails, Spring Batch can restart from the saved state.

---

# 14. What is chunk-oriented processing?

Chunk processing means Spring Batch reads data item by item, groups them into chunks, and writes each chunk in one transaction.

Example:

```text
chunk size = 100

Read 100 records
Process 100 records
Write 100 records
Commit transaction
```

Official docs describe chunk-oriented processing as reading items one at a time and writing a chunk within a transaction boundary once the commit interval is reached. ([Home][4])

---

# 15. Explain Reader, Processor, Writer flow.

Flow:

```text
ItemReader → ItemProcessor → ItemWriter
```

Example:

```text
Read employee from CSV
Convert employee name to uppercase
Save employee into database
```

Java example:

```java
@Bean
public ItemProcessor<Employee, Employee> processor() {
    return employee -> {
        employee.setName(employee.getName().toUpperCase());
        return employee;
    };
}
```

---

# 16. What is ItemReader?

**ItemReader** reads data from a source.

Sources can be:

```text
CSV file
XML file
JSON file
Database
REST API
Queue
```

Example readers:

```text
FlatFileItemReader
JdbcCursorItemReader
JdbcPagingItemReader
JpaPagingItemReader
MultiResourceItemReader
```

---

# 17. What is ItemProcessor?

**ItemProcessor** is used for business logic, transformation, validation, or filtering.

Example:

```java
@Bean
public ItemProcessor<Employee, Employee> employeeProcessor() {
    return employee -> {
        if (employee.getSalary() <= 0) {
            return null; // filtered, not written
        }
        employee.setName(employee.getName().trim().toUpperCase());
        return employee;
    };
}
```

Important:

```text
If processor returns null, item is filtered and not sent to writer.
```

Official docs describe ItemProcessor as a component that receives one object, applies business logic, and may return the same or a different type. ([Home][5])

---

# 18. What is ItemWriter?

**ItemWriter** writes processed data to destination.

Destinations can be:

```text
Database
CSV file
XML file
JSON file
Kafka
REST API
Email
```

Example:

```java
@Bean
public JdbcBatchItemWriter<Employee> writer(DataSource dataSource) {
    return new JdbcBatchItemWriterBuilder<Employee>()
            .dataSource(dataSource)
            .sql("INSERT INTO employee(name, salary) VALUES (:name, :salary)")
            .beanMapped()
            .build();
}
```

---

# 19. What is Tasklet in Spring Batch?

A **Tasklet** is used when a Step performs one task, not item-by-item processing.

Example use cases:

```text
Delete old files
Call stored procedure
Send email
Create report
Move file from one folder to another
```

Java example:

```java
@Bean
public Tasklet cleanupTasklet() {
    return (contribution, chunkContext) -> {
        System.out.println("Deleting temporary files...");
        return RepeatStatus.FINISHED;
    };
}
```

Official Spring Batch docs show Tasklet-based steps for simple one-time operations such as deleting files. ([Home][6])

---

# 20. Difference between Tasklet and Chunk processing?

| Tasklet                             | Chunk Processing               |
| ----------------------------------- | ------------------------------ |
| Used for one task                   | Used for large item processing |
| No Reader/Processor/Writer required | Uses Reader, Processor, Writer |
| Example: delete file                | Example: CSV to DB             |
| Simple operation                    | Large volume data processing   |

Interview answer:

> Use Tasklet for simple one-time tasks. Use chunk processing when we need to process records one by one in a transactional way.

---

# 21. Give a basic Spring Batch Job example.

Modern Spring Batch 5+ style:

```java
@Configuration
public class EmployeeBatchConfig {

    @Bean
    public Job employeeJob(JobRepository jobRepository, Step employeeStep) {
        return new JobBuilder("employeeJob", jobRepository)
                .start(employeeStep)
                .build();
    }

    @Bean
    public Step employeeStep(JobRepository jobRepository,
                             PlatformTransactionManager transactionManager,
                             ItemReader<Employee> reader,
                             ItemProcessor<Employee, Employee> processor,
                             ItemWriter<Employee> writer) {

        return new StepBuilder("employeeStep", jobRepository)
                .<Employee, Employee>chunk(100, transactionManager)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .build();
    }
}
```

In older Spring Boot 2 projects, you may also see:

```java
JobBuilderFactory
StepBuilderFactory
```

In newer configuration, `JobRepository` and `PlatformTransactionManager` are commonly passed directly to builders. ([Home][3])

---

# 22. What is chunk size?

Chunk size defines how many records are processed before committing a transaction.

Example:

```java
.<Employee, Employee>chunk(100, transactionManager)
```

Meaning:

```text
Read 100
Process 100
Write 100
Commit
```

If chunk size is too small:

```text
Too many database commits
Slow performance
```

If chunk size is too large:

```text
High memory usage
Large rollback impact
```

---

# 23. How does transaction management work in Spring Batch?

In chunk processing, one chunk usually runs inside one transaction.

Example:

```text
chunk size = 100

If all 100 records are written successfully:
    transaction commit

If error occurs:
    transaction rollback
```

Spring Batch Step configuration uses Spring’s `PlatformTransactionManager` to begin and commit transactions during processing. ([Home][7])

---

# 24. What happens if one item fails inside a chunk?

Suppose chunk size is 100.

If item number 75 fails while writing:

```text
Complete chunk may rollback
```

Then behavior depends on configuration:

```text
No fault tolerance → Step fails
Skip configured → bad record skipped
Retry configured → Spring Batch retries
Custom listener → failure logged
```

---

# 25. What is fault tolerance in Spring Batch?

Fault tolerance means the job can handle some errors without completely failing.

Spring Batch supports:

```text
Skip
Retry
No rollback
Listeners
Custom exception handling
```

Example:

```java
.faultTolerant()
.skip(InvalidEmployeeException.class)
.skipLimit(10)
.retry(DeadlockLoserDataAccessException.class)
.retryLimit(3)
```

Meaning:

```text
Skip up to 10 invalid employees
Retry deadlock error 3 times
```

---

# 26. What is skip in Spring Batch?

Skip means ignore a bad record and continue processing.

Example:

```java
@Bean
public Step employeeStep(JobRepository jobRepository,
                         PlatformTransactionManager transactionManager) {
    return new StepBuilder("employeeStep", jobRepository)
            .<Employee, Employee>chunk(100, transactionManager)
            .reader(reader())
            .processor(processor())
            .writer(writer())
            .faultTolerant()
            .skip(InvalidEmployeeException.class)
            .skipLimit(5)
            .build();
}
```

If 5 records are invalid, they are skipped. If the 6th invalid record comes, the Step fails.

---

# 27. What is retry in Spring Batch?

Retry means try the failed operation again before marking it failed.

Useful for temporary issues:

```text
Database deadlock
Network timeout
Temporary API failure
```

Example:

```java
.faultTolerant()
.retry(DeadlockLoserDataAccessException.class)
.retryLimit(3)
```

Meaning:

```text
Retry deadlock exception maximum 3 times.
```

---

# 28. Difference between skip and retry?

| Skip                     | Retry                     |
| ------------------------ | ------------------------- |
| Ignore bad record        | Try same record again     |
| Used for data errors     | Used for temporary errors |
| Example: invalid email   | Example: DB deadlock      |
| Continues with next item | Re-attempts current item  |

Example:

```text
Invalid date format → skip
Database timeout → retry
```

---

# 29. What is restartability in Spring Batch?

Restartability means if a job fails, it can restart from the failure point instead of starting from zero.

Example:

```text
Total records: 10,000
Processed: 6,000
Job failed

Restart:
Starts from record 6,001
```

This is possible because Spring Batch stores execution metadata in JobRepository and ExecutionContext.

---

# 30. How do you make a Job restartable?

By default, Spring Batch jobs are restartable if metadata is available.

Important points:

```text
Use persistent JobRepository
Use proper JobParameters
Use restartable ItemReader
Store state in ExecutionContext
Do not delete metadata tables unnecessarily
Avoid non-idempotent writes
```

Example:

```java
return new JobBuilder("employeeJob", jobRepository)
        .start(employeeStep)
        .preventRestart() // disables restart
        .build();
```

If you use `preventRestart()`, job cannot be restarted.

---

# 31. What is idempotency in Spring Batch?

Idempotency means running the same operation multiple times should not create duplicate or wrong output.

Example problem:

```text
Job fails after writing 500 records.
Restart writes same 500 records again.
Duplicate records created.
```

Solution:

```text
Use unique keys
Use upsert/merge
Check existing records
Use processed flag
Use business transaction ID
```

Interview answer:

> Idempotency is very important in restartable batch jobs because the same chunk may be retried or reprocessed after failure.

---

# 32. What are BatchStatus and ExitStatus?

**BatchStatus** tells technical execution status.

Examples:

```text
STARTING
STARTED
COMPLETED
FAILED
STOPPED
ABANDONED
```

**ExitStatus** is used to decide flow control.

Example:

```text
COMPLETED
FAILED
COMPLETED_WITH_SKIPS
```

Simple difference:

| BatchStatus               | ExitStatus                    |
| ------------------------- | ----------------------------- |
| Internal technical status | Flow decision/status returned |
| Managed by framework      | Can be customized             |
| Example: FAILED           | Example: COMPLETED_WITH_SKIPS |

---

# 33. What is JobExecutionListener?

It runs before and after a Job.

Example:

```java
@Component
public class EmployeeJobListener implements JobExecutionListener {

    @Override
    public void beforeJob(JobExecution jobExecution) {
        System.out.println("Job started: " + jobExecution.getStartTime());
    }

    @Override
    public void afterJob(JobExecution jobExecution) {
        System.out.println("Job ended with status: " + jobExecution.getStatus());
    }
}
```

Use cases:

```text
Logging
Notification
Audit entry
Cleanup
Generate summary
```

---

# 34. What is StepExecutionListener?

It runs before and after a Step.

Example use cases:

```text
Step-level logging
Initialize step data
Store summary
Check read/write counts
```

Example:

```java
@Component
public class EmployeeStepListener implements StepExecutionListener {

    @Override
    public void beforeStep(StepExecution stepExecution) {
        System.out.println("Step started");
    }

    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {
        System.out.println("Read count: " + stepExecution.getReadCount());
        return stepExecution.getExitStatus();
    }
}
```

---

# 35. What are ItemReadListener, ItemProcessListener, and ItemWriteListener?

These listeners are used at item level.

```text
ItemReadListener → before/after/error during read
ItemProcessListener → before/after/error during processing
ItemWriteListener → before/after/error during writing
```

Use cases:

```text
Log failed records
Audit processing
Track bad data
Send metrics
```

---

# 36. What is FlatFileItemReader?

`FlatFileItemReader` reads data from a flat file such as CSV or TXT.

Example CSV:

```csv
id,name,salary
1,Danish,50000
2,Aman,60000
```

Example:

```java
@Bean
public FlatFileItemReader<Employee> reader() {
    return new FlatFileItemReaderBuilder<Employee>()
            .name("employeeReader")
            .resource(new ClassPathResource("employees.csv"))
            .delimited()
            .names("id", "name", "salary")
            .targetType(Employee.class)
            .linesToSkip(1)
            .build();
}
```

---

# 37. Difference between JdbcCursorItemReader and JdbcPagingItemReader?

| JdbcCursorItemReader                  | JdbcPagingItemReader       |
| ------------------------------------- | -------------------------- |
| Uses database cursor                  | Reads page by page         |
| Maintains open connection             | Executes paginated queries |
| Good for moderate data                | Better for large data      |
| Can be sensitive to long transactions | More scalable              |

Interview answer:

> For very large datasets, JdbcPagingItemReader is often preferred because it reads data in pages and avoids keeping one cursor open for a long time.

---

# 38. What is JpaPagingItemReader?

`JpaPagingItemReader` reads records from a database using JPA in pages.

Useful when application uses:

```text
JPA
Hibernate
Entity classes
JPQL queries
```

Example idea:

```text
Read employees page by page from Employee entity.
```

It is useful, but for very high-performance batch jobs, JDBC readers are often faster because they avoid ORM overhead.

---

# 39. What is CompositeItemProcessor?

`CompositeItemProcessor` allows multiple processors to run in sequence.

Example:

```text
EmployeeValidationProcessor
        ↓
EmployeeNormalizationProcessor
        ↓
EmployeeSalaryCalculationProcessor
```

Example:

```java
@Bean
public CompositeItemProcessor<Employee, Employee> compositeProcessor() {
    CompositeItemProcessor<Employee, Employee> processor =
            new CompositeItemProcessor<>();

    processor.setDelegates(List.of(
            validationProcessor(),
            normalizationProcessor()
    ));

    return processor;
}
```

---

# 40. What is CompositeItemWriter?

`CompositeItemWriter` writes the same processed data to multiple destinations.

Example:

```text
Write employee to database
Write same employee to audit file
```

Use carefully because writing to multiple systems can create consistency issues.

---

# 41. How do you pass dynamic file name to Reader?

Use JobParameters with `@StepScope`.

Example:

```java
@Bean
@StepScope
public FlatFileItemReader<Employee> reader(
        @Value("#{jobParameters['fileName']}") String fileName) {

    return new FlatFileItemReaderBuilder<Employee>()
            .name("employeeReader")
            .resource(new FileSystemResource(fileName))
            .delimited()
            .names("id", "name", "salary")
            .targetType(Employee.class)
            .build();
}
```

Run job with:

```text
fileName=/data/employees.csv
```

Important:

> `@StepScope` creates the bean when the Step starts, so job parameters are available.

---

# 42. What is @StepScope?

`@StepScope` means bean lifecycle is bound to Step execution.

It is commonly used when Reader/Processor/Writer needs runtime values.

Example:

```java
@Value("#{jobParameters['date']}")
private String date;
```

Without `@StepScope`, job parameters may not be available at bean creation time.

---

# 43. What is @JobScope?

`@JobScope` means bean lifecycle is bound to Job execution.

Difference:

| @StepScope                      | @JobScope                       |
| ------------------------------- | ------------------------------- |
| Bean created per Step execution | Bean created per Job execution  |
| Used mostly for Reader/Writer   | Used for job-level dependencies |
| Access step/job context         | Access job context              |

---

# 44. How do you create conditional flow in Spring Batch?

Conditional flow means deciding next Step based on previous Step result.

Example:

```java
@Bean
public Job employeeJob(JobRepository jobRepository) {
    return new JobBuilder("employeeJob", jobRepository)
            .start(validateStep())
                .on("FAILED").to(errorStep())
            .from(validateStep())
                .on("COMPLETED").to(processStep())
            .end()
            .build();
}
```

Example flow:

```text
validateStep FAILED → errorStep
validateStep COMPLETED → processStep
```

---

# 45. What is JobExecutionDecider?

`JobExecutionDecider` is used when flow decision requires custom logic.

Example:

```java
@Component
public class FileAvailableDecider implements JobExecutionDecider {

    @Override
    public FlowExecutionStatus decide(JobExecution jobExecution,
                                      StepExecution stepExecution) {

        boolean fileAvailable = true;

        if (fileAvailable) {
            return new FlowExecutionStatus("FILE_FOUND");
        }
        return new FlowExecutionStatus("FILE_NOT_FOUND");
    }
}
```

Use case:

```text
If file exists → process file
If file missing → send alert
```

---

# 46. How can we scale Spring Batch jobs?

Spring Batch can be scaled using:

```text
Multi-threaded Step
Parallel Steps
Partitioning
Remote chunking
Remote partitioning
```

But official docs also recommend first checking whether a simple single-threaded job is enough before adding complexity. ([Home][8])

Simple explanation:

| Technique           | Meaning                                 |
| ------------------- | --------------------------------------- |
| Multi-threaded Step | Multiple threads process chunks         |
| Parallel Steps      | Multiple independent steps run together |
| Partitioning        | Split data into partitions              |
| Remote chunking     | Manager reads, workers process/write    |
| Remote partitioning | Manager assigns partitions to workers   |

---

# 47. What is partitioning in Spring Batch?

Partitioning means splitting a large dataset into smaller parts and processing them in parallel.

Example:

```text
1 to 1,00,000 records

Partition 1: 1 - 25,000
Partition 2: 25,001 - 50,000
Partition 3: 50,001 - 75,000
Partition 4: 75,001 - 1,00,000
```

Each partition can run independently.

Good for:

```text
Large database tables
Huge files
Independent data ranges
```

---

# 48. What is remote chunking?

Remote chunking means:

```text
Manager process reads data
Worker processes process/write chunks
```

Typical flow:

```text
Manager → sends chunks via messaging → Workers
Workers → process/write → reply
```

Useful when:

```text
Processing is heavy
Need distributed workers
Need horizontal scalability
```

In Spring Batch 6, official docs mention remote step support for executing steps on remote machines or clusters. ([Home][9])

---

# 49. How do you test Spring Batch jobs?

Use `spring-batch-test`.

Common testing utilities:

```text
@SpringBatchTest
JobLauncherTestUtils
JobRepositoryTestUtils
```

Example:

```java
@SpringBatchTest
@SpringBootTest
class EmployeeJobTest {

    @Autowired
    private JobLauncherTestUtils jobLauncherTestUtils;

    @Test
    void testEmployeeJob() throws Exception {
        JobParameters params = new JobParametersBuilder()
                .addLong("time", System.currentTimeMillis())
                .toJobParameters();

        JobExecution execution = jobLauncherTestUtils.launchJob(params);

        assertEquals(BatchStatus.COMPLETED, execution.getStatus());
    }
}
```

Official docs say `spring-batch-test` provides utilities for end-to-end batch job testing. ([Home][10])

---

# 50. Is Spring Batch a scheduler?

No. Spring Batch is **not a scheduler**.

Spring Batch is responsible for:

```text
Job execution
Chunk processing
Restartability
Skip/retry
Metadata
Transactions
```

For scheduling, we use:

```text
Spring @Scheduled
Quartz
Control-M
Autosys
Cron
Kubernetes CronJob
AWS EventBridge
Jenkins
```

Example:

```java
@Scheduled(cron = "0 0 1 * * *")
public void runJob() throws Exception {
    JobParameters params = new JobParametersBuilder()
            .addLong("time", System.currentTimeMillis())
            .toJobParameters();

    jobLauncher.run(employeeJob, params);
}
```

Official Spring Batch documentation explicitly notes that Spring Batch is not a scheduling framework. ([Home][11])

---

# Most Important Spring Batch Interview Diagram

```text
                  JobLauncher
                      |
                      v
                    Job
                      |
        -----------------------------
        |                           |
      Step 1                      Step 2
        |
        v
  ItemReader
        |
        v
  ItemProcessor
        |
        v
  ItemWriter
        |
        v
  JobRepository stores metadata
```

---

# One Complete Mini Example: CSV to Database

```java
@Configuration
public class EmployeeBatchConfig {

    @Bean
    public Job employeeImportJob(JobRepository jobRepository, Step employeeStep) {
        return new JobBuilder("employeeImportJob", jobRepository)
                .start(employeeStep)
                .build();
    }

    @Bean
    public Step employeeStep(JobRepository jobRepository,
                             PlatformTransactionManager transactionManager,
                             FlatFileItemReader<Employee> reader,
                             ItemProcessor<Employee, Employee> processor,
                             JdbcBatchItemWriter<Employee> writer) {

        return new StepBuilder("employeeStep", jobRepository)
                .<Employee, Employee>chunk(100, transactionManager)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .faultTolerant()
                .skip(InvalidEmployeeException.class)
                .skipLimit(10)
                .build();
    }

    @Bean
    public FlatFileItemReader<Employee> reader() {
        return new FlatFileItemReaderBuilder<Employee>()
                .name("employeeReader")
                .resource(new ClassPathResource("employees.csv"))
                .delimited()
                .names("id", "name", "salary")
                .targetType(Employee.class)
                .linesToSkip(1)
                .build();
    }

    @Bean
    public ItemProcessor<Employee, Employee> processor() {
        return employee -> {
            if (employee.getSalary() <= 0) {
                throw new InvalidEmployeeException("Invalid salary");
            }

            employee.setName(employee.getName().trim().toUpperCase());
            return employee;
        };
    }

    @Bean
    public JdbcBatchItemWriter<Employee> writer(DataSource dataSource) {
        return new JdbcBatchItemWriterBuilder<Employee>()
                .dataSource(dataSource)
                .sql("""
                    INSERT INTO employee(id, name, salary)
                    VALUES (:id, :name, :salary)
                    """)
                .beanMapped()
                .build();
    }
}
```

---

# Strong Interview Summary

For interviews, explain Spring Batch like this:

> Spring Batch is used for large-scale offline processing. A Job represents the complete batch process. A Job contains one or more Steps. A Step can be tasklet-based or chunk-based. In chunk processing, Spring Batch reads records using ItemReader, applies business logic using ItemProcessor, and writes records using ItemWriter. The JobRepository stores metadata, which helps in monitoring, restartability, and failure recovery. For production-level jobs, we handle transactions, skip, retry, listeners, job parameters, execution context, and idempotency carefully.

---

# Must-Revise Topics Before Interview

Focus most on these:

```text
Job vs Step
JobInstance vs JobExecution
JobRepository
JobParameters
ExecutionContext
Chunk processing
Tasklet vs Chunk
Reader/Processor/Writer
Skip vs Retry
Restartability
Idempotency
Listeners
StepScope and JobScope
Partitioning
Testing Spring Batch jobs
```

These are the areas where interviewers usually go deeper.

[1]: https://docs.spring.io/spring-batch/reference/readersAndWriters.html?utm_source=chatgpt.com "Spring Batch Reference - ItemReaders and ItemWriters"
[2]: https://docs.spring.io/spring-batch/trunk_bkp/reference/htmlsingle/?utm_source=chatgpt.com "Spring Batch - Reference Documentation"
[3]: https://docs.spring.io/spring-batch/reference/5.1/job/java-config.html?utm_source=chatgpt.com "Java Configuration :: Spring Batch Reference"
[4]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html?utm_source=chatgpt.com "Chunk-oriented Processing :: Spring Batch Reference"
[5]: https://docs.spring.io/spring-batch/reference/processor.html?utm_source=chatgpt.com "Item processing :: Spring Batch Reference"
[6]: https://docs.spring.io/spring-batch/reference/step/tasklet.html?utm_source=chatgpt.com "TaskletStep :: Spring Batch Reference"
[7]: https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing/configuring.html?utm_source=chatgpt.com "Configuring a Step :: Spring Batch Reference"
[8]: https://docs.spring.io/spring-batch/reference/scalability.html?utm_source=chatgpt.com "Scaling and Parallel Processing :: Spring Batch Reference"
[9]: https://docs.spring.io/spring-batch/reference/whatsnew.html?utm_source=chatgpt.com "What's new in Spring Batch 6"
[10]: https://docs.spring.io/spring-batch/reference/testing.html?utm_source=chatgpt.com "Unit Testing :: Spring Batch Reference"
[11]: https://docs.spring.io/spring-batch/docs/4.0.x/reference/html/index-single.html?utm_source=chatgpt.com "Spring Batch - Reference Documentation"
