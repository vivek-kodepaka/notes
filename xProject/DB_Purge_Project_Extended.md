# DB Purge Project -- Extended Technical Documentation

------------------------------------------------------------------------

# 1. High-Level Architecture Flow

## Flow Steps

1.  Kubernetes CronJob triggers based on schedule.
2.  CronJob container connects to SQL Server using secrets.
3.  Stored Procedure is executed with:
    -   Facility Number
    -   Retention Days
    -   Batch Size
4.  Stored Procedure:
    -   Calculates retention date
    -   Runs batch delete in loop
    -   Commits after each batch
5.  Logs are written to Kubernetes pod logs.
6.  Monitoring validates successful execution.

------------------------------------------------------------------------

# 2. Stored Procedure -- Sample Structure

``` sql
CREATE PROCEDURE usp_purge_oa_tables_with_facility_nbr_with_limit
    @FacilityNbr INT,
    @RetentionDays INT,
    @BatchSize INT
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @RetentionDate DATETIME;
    SET @RetentionDate = DATEADD(DAY, -@RetentionDays, GETDATE());

    BEGIN TRY
        WHILE (1 = 1)
        BEGIN
            BEGIN TRAN;

            DELETE TOP (@BatchSize)
            FROM OrdersTable
            WHERE FacilityNbr = @FacilityNbr
              AND Create_Ts < @RetentionDate;

            IF @@ROWCOUNT = 0
            BEGIN
                COMMIT;
                BREAK;
            END

            COMMIT;
        END
    END TRY
    BEGIN CATCH
        ROLLBACK;
        RAISERROR('Purge Failed', 16, 1);
    END CATCH
END
```

## Key Concepts Used

-   `DELETE TOP (@BatchSize)` → Batch deletion
-   `@@ROWCOUNT` → Detect completion
-   Explicit `BEGIN TRAN / COMMIT`
-   `TRY-CATCH` for error handling
-   Retention logic using `DATEADD`

------------------------------------------------------------------------

# 3. Why Dynamic SQL Was Used

If multiple tables required purge logic, dynamic SQL allows:

-   Table name flexibility
-   Column flexibility
-   Facility-based filtering
-   Reusable purge framework

Example:

``` sql
SET @Sql = '
DELETE TOP (' + CAST(@BatchSize AS VARCHAR) + ')
FROM ' + @TableName + '
WHERE FacilityNbr = @FacilityNbr
AND Create_Ts < @RetentionDate'

EXEC sp_executesql @Sql,
     N'@FacilityNbr INT, @RetentionDate DATETIME',
     @FacilityNbr,
     @RetentionDate;
```

------------------------------------------------------------------------

# 4. Kubernetes CronJob (KITT YAML) Explanation

## Sample YAML Structure

``` yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: db-purge-job
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: purge-container
              image: purge-image:latest
              env:
                - name: DB_CONNECTION
                  valueFrom:
                    secretKeyRef:
                      name: db-secret
                      key: connectionString
              resources:
                limits:
                  memory: "512Mi"
                  cpu: "500m"
          restartPolicy: OnFailure
```

## YAML Explanation

-   `schedule` → Cron expression (runs daily at 2 AM)
-   `secretKeyRef` → Secure DB credentials
-   `resources` → Prevent resource overconsumption
-   `restartPolicy: OnFailure` → Auto-retry if failure occurs

------------------------------------------------------------------------

# 5. Architecture Diagram (Logical Representation)

User Request ↓ Kubernetes CronJob (Scheduled) ↓ Container / Pod ↓ SQL
Server Connection (via Secret) ↓ Stored Procedure Execution ↓ Batch
Delete Loop ↓ Commit per Batch ↓ Logs + Monitoring

------------------------------------------------------------------------

# 6. Production Safety Mechanisms

-   Batch size control
-   Facility-based isolation
-   Configurable retention
-   Explicit transaction handling
-   Error handling with rollback
-   Kubernetes retry capability

------------------------------------------------------------------------

# 7. Improvements for Enterprise Level

-   Add Audit Table:
    -   TableName
    -   FacilityNbr
    -   DeletedRowCount
    -   ExecutionTime
    -   Status
-   Expose metrics to Prometheus
-   Alert if purge fails
-   Track average execution duration

------------------------------------------------------------------------

# 8. Strong Interview Explanation (Technical Version)

> This purge framework was designed as a production-safe, multi-tenant
> data lifecycle solution.\
> It prevents uncontrolled table growth which impacts query latency, IO
> cost, index size, and transaction log usage.\
> We implemented batch deletion using DELETE TOP inside a loop to avoid
> long-running transactions, reduce lock contention,\
> and control log growth. The job runs as a Kubernetes CronJob with
> secure secret-based DB connectivity.\
> Retention policies are configurable per table, and the procedure
> includes proper transaction management and error handling.

------------------------------------------------------------------------

# End of Extended Documentation
