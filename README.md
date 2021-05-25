# Spring batch processing to process the records
This repository contains the implementation for spring batch processing to process the records. It will read the records from csv file and save in in-memory database.

### What is the spring batch?
Spring batch is a framework processing large volumes of records, including transaction management, job processing statistics, and resource management. It provides advanced services and features which enable the processing of high-volume data through chunk-based processing and partitioning techniques.

## Spring batch flow:
//image

* <b><i>Job Launcher</i></b>: It starts a job execution for a given job which passed as a parameter. It also accepts job parameters that can be used during job execution.

``jobLauncher.run("job name", jobParameters);``

* <b><i>Job</i></b> is executed from the job launcher. It represents the configuration and updates the meta-information like status and statistics as necessary of jobs in a job execution context. JobExecution indicates the “physical” execution of Job.

* <b><i>Step</i></b> is executed from the job. It fetches the input data by using ItemReader. It processes the input data by using ItemProcessor and outputs the processed data by using ItemWriter.
_Job can have more than one steps in job flow._

* JobLauncher registers JobInstance in Database through <b><i>JobRepository</i></b>.It registers that job execution has started, updates information like i/o records, status, job execution has completed in the database through JobRepository.

# Develop a spring batch application using spring boot.
In this, we will develop a spring application that will run the spring batch job that will read the list of student details from a CSV file, transform it using a custom processor, and stores the final result in an in-memory database.

<b>Prerequisite</b>:
1. JDK 1.8 or later
2. Gradle 4+
3. Intellij (IDE).

* **Getting started**

**1. Add Gradle dependencies for Spring Batch and HyperSQL Database.**

build.gradle:

``implementation 'org.springframework.boot:spring-boot-starter-batch'
runtimeOnly 'org.hsqldb:hsqldb'``

**2. CSV file and SQL script**

student-data.csv: This file contains the data to transform
``Smit,Doe
Joe,Doe
Justin,Drake
Jane,Dyane
John,Shaw``

student-schema.sql: SQL script to create student table and store data.

``DROP TABLE student IF EXISTS;

CREATE TABLE student  (
  person_id BIGINT IDENTITY NOT NULL PRIMARY KEY,
  first_name VARCHAR(20),
  last_name VARCHAR(20)
);``

***Add the following property in the application.yml file.***

``file.input: student-data.csv``

**3. Student.java: Model class to hold the student details.**

https://gist.github.com/SHRADHA-YEWALE/d6889e31b8784cdad7fa99a73067668b

**4. Job configuration:**

https://gist.github.com/SHRADHA-YEWALE/484b52fae3034bcc27f26c88b002b989

**@EnableBatchProcessing** gives us access to many useful beans that support jobs and will save us a lot of leg work.

It also provides us with access to two useful factories:

***JobBuilderFactory*** (Creates a job builder and initializes its job repository). 
***StepBuilderFactory*** (Creates a step builder and initializes its job repository and transaction manager).

**5. StudentItemProcessor.java: To transform student details.**

https://gist.github.com/SHRADHA-YEWALE/38a508906026568082a9a74467e2a07e 

**What all steps are doing:**

* First, we configure our step so that it will write up to ten records at a time using the chunk(10) declaration
* Then, we read in the student data using our reader bean, which we set using the reader method
* Next, we pass each of our student items to a custom processor where we apply some custom business logic
* Finally, we write each student item to the database using the writer we saw previously.

**6. Job Notification listener** which we use to get notified when the job completes. It also tells us the execution start time, end time along job status.

https://gist.github.com/SHRADHA-YEWALE/39c871c4ec0ada52e74dda3e6259cac4

**7. Spring batch application:**

https://gist.github.com/SHRADHA-YEWALE/b7c498177de0968ac9d4aaaa6344a1fc

**8. Run the Job :**

You can either run the job by using:

``java -jar <Jar-file-name.jar>``

Or

Write a test controller and use the JobLauncher to trigger the job and then hit the controller from the postman.
<br/>

**Applications of Spring batch:**
1. Moving high volumes of data between databases, transforming it.
2. Processing a high volume of data/records.
3. Transaction management, Resource management.

Medium article link : https://shradhayewale.medium.com/spring-batch-processing-overview-371f1d9ee536 




