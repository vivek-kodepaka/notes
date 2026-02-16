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
CREATE OR ALTER PROCEDURE [dbo].[usp_purge_oa_tables_with_facility_nbr_with_limit]
    @TableName       VARCHAR(500),   -- Table to delete from
    @RetentionDays   INT,            -- How old data must be to delete
    @BatchSize       INT = 1000,     -- Number of rows to delete per batch
    @FacilityNbr     INT,            -- Facility number filter
    @Condition       VARCHAR(500) = '' -- Extra condition if needed
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @loop         BIT = 1;
    DECLARE @iteration    INT = 1;
    DECLARE @rowcount     INT = 0;
    DECLARE @days         INT;
    DECLARE @tblname      VARCHAR(500) = @TableName;
    DECLARE @sqlDelCommand NVARCHAR(2048);

    -- Make retention negative so DATEADD works properly
    SET @days = -@RetentionDays;

    WHILE (@loop = 1 AND @iteration <= @BatchSize)
    BEGIN
        BEGIN TRY
            BEGIN TRANSACTION;

            SET @sqlDelCommand = 
                 'DELETE TOP (' + CAST(@BatchSize AS VARCHAR(10)) + ') '
               + 'FROM ' + @tblname
               + ' WHERE facility_nbr = ' + CAST(@FacilityNbr AS VARCHAR(10))
               + ' AND create_ts < DATEADD(DAY, ' + CAST(@days AS VARCHAR(10)) + ', GETDATE()) '
               + @Condition;

            PRINT @sqlDelCommand;

            EXEC sp_executesql @sqlDelCommand;

            SET @rowcount = @@ROWCOUNT;
            SET @iteration = @iteration + 1;

            IF (@rowcount = 0 OR @rowcount < @BatchSize)
                SET @loop = 0;

            COMMIT TRANSACTION;
        END TRY
        BEGIN CATCH
            -- Something went wrong, stop loop
            SET @loop = 0;

            DECLARE @ErrorMessage   NVARCHAR(4000),
                    @ErrorSeverity  INT,
                    @ErrorState     INT;

            SELECT 
                @ErrorMessage  = ERROR_MESSAGE(),
                @ErrorSeverity = ERROR_SEVERITY(),
                @ErrorState    = ERROR_STATE();

            -- Rethrow original error so calling system knows failure reason
            RAISERROR (
                @ErrorMessage,
                @ErrorSeverity,
                @ErrorState
            );

            ROLLBACK TRANSACTION;
        END CATCH
    END
END

```

```sql
EXECUTE (dbo].(usp_purge oa_tables_with_facility_nbr_with_limit] alloc_order obd, 90,1000, 8801, 5000, 'and facility_cntry_code in ('MX','mx')'
EXECUTE [dbo].(usp_purge_oa_tables mith_facility_nbr_with_ Limit] container distribution, 90, 1000, 8801, 5000, 'and facility_cntry_code in ('MX'', 'Mx')'
EXECUTE [dbo].[usp purge oa tables_with facility nbr with limit] alloc_order pick_opv, 90, 1000, 8801, 3000, 'and facility_cntry_code in ('MX', 'mx')'
EXECUTE [dbo].[usp_purge oa tables_with facility_nbr with limit] alloc process_status, 90, 1000, 8801, 1000, 'and facility_cntry_code in ('MX','mx')'
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
profiles:
  - cronjob

notify:
  slack:
    channelName: wcnp-intl-db-purge
  msTeams:
    channelId: "19%3Aa97dd4f165d94d3fa5f09e1c0b53cb4a%40thread.tacv2"

artifact: oa-perish-purge

deploy:
  namespace: atlas-op-mx
  stages:
    - name: stg-cell033
      approvers:
        groups:
          - FLEXPLUS
          - OP
      target:
        cluster_id: uscentral-stage-wut-003
      refs: 
        - "main"
      helm:
        values:
          secrets:
            akeyless: true
          engine: eso
          config:
            akeyless:
              path: |
                WCNP:Prod/wcip/homeoffice/FLEXPLUS_OP/allocation-order-service-purge
                DPS:Non-Prod/DPS/homeoffice/FLEXPLUS_OP
            addr: "https://akeyless.gw.prod.glb.us.walmart.net:8080/v2"

      cronJobs:
        - cronJobName: allocation-db-purge-stg-cel1033
          schedule: "30 5 * * *"   # Run every day at 05:30 UTC
          startingDeadlineSeconds: 200
          activeDeadlineSeconds: 600
          concurrencyPolicy: Forbid
          secrets:
            akeyless: true

      files:
        - destination: dbconfig.json
          content: purge.config
          resources:
            limits:
              cpu: "0.1"
              memory: "256Mi"
            requests:
              cpu: "0.1"
              memory: "256Mi"

      metadata:
        labels:
          wm.app: ATLAS-INTL-DB-PURGE
          wm.env: stg-cel1033

      postDeploy:
        task:
          name: Allocation Order Service Purge MX stg-cell033
          type: deployApp
          kittFilepath: kitt-config/allocation-order-service/stages/allocation-order-service-cell033-prod.yml
          tag: "branch:'($.kitt.build.commitEvent.currentBranch)P'"

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
