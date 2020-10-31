---
title: 在 SQL connector 中處理長時間執行的預存程式
description: 如何處理在 Azure Logic Apps 中使用 SQL 連接器時的預存程式
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102868"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>處理 SQL connector for Azure Logic Apps 中的預存程式超時

當您的邏輯應用程式使用結果集很大時， [SQL connector](../connectors/connectors-create-api-sqlazure.md) 不會同時傳回所有結果，或是如果您想要更充分掌控結果集的大小和結構，您可以建立 [預存](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 程式，以您想要的方式組織結果。 SQL connector 提供許多可利用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 存取的後端功能，讓您可以更輕鬆地將使用 SQL database 資料表的商務工作自動化。

例如，在取得或插入多個資料列時，您的邏輯應用程式可以在這些 [限制](../logic-apps/logic-apps-limits-and-config.md)內使用 [ **Until** 迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)，逐一查看這些資料列。 不過，當您的邏輯應用程式必須使用數千或數百萬個數據列時，您會想要將對資料庫的呼叫所產生的成本降至最低。 如需詳細資訊，請參閱 [使用 SQL Connector 處理大量資料](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)。

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>預存程式執行的超時限制

SQL 連接器的預存程式超時限制 [小於2分鐘](/connectors/sql/#known-issues-and-limitations)。 某些預存程式可能需要超過此限制才能完成，因而導致 `504 Timeout` 錯誤。 有時候，這些長時間執行的進程會針對此用途明確編碼為預存程式。 由於時間限制，從 Azure Logic Apps 呼叫這些程式可能會造成問題。 雖然 SQL connector 原本就不支援非同步模式，但您可以解決這個問題，並使用 SQL 完成觸發程式、原生 SQL 傳遞查詢、狀態資料表和伺服器端作業來模擬此模式。 針對這項工作，您可以使用適用于[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)的[Azure 彈性作業代理程式](../azure-sql/database/elastic-jobs-overview.md)。 針對 [SQL Server 內部部署](/sql/sql-server/sql-server-technical-documentation) 和 [Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，您可以使用 [SQL Server Agent](/sql/ssms/agent/sql-server-agent)。

例如，假設您有下列長時間執行的預存程式，它所花費的時間超過完成執行的時間限制。 如果您使用 SQL 連接器從邏輯應用程式執行這個預存程式，則會收到 `HTTP 504 Gateway Timeout` 錯誤結果。

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

您可以使用 *作業代理程式* ，以非同步方式在背景中執行程式，而不是直接呼叫預存程式。 您可以將輸入和輸出儲存在狀態資料表中，然後您可以透過邏輯應用程式與之互動。 如果您不需要輸入和輸出，或您已經將結果寫入預存程式中的資料表，您可以簡化此方法。

> [!IMPORTANT]
> 請確定您的預存程式和所有作業都是 *等冪* 的，這表示它們可以執行多次，而不會影響結果。 如果非同步處理失敗或超時，作業代理程式可能會重試該步驟，因此，您的預存程式會多次。 若要避免複製輸出，在建立任何物件之前，請先檢查這些 [最佳作法和方法](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)。

下一節說明如何使用 Azure 彈性作業代理程式來 Azure SQL Database。 針對 SQL Server 和 Azure SQL 受控執行個體，您可以使用 SQL Server Agent。 某些管理詳細資料將會不同，但基礎步驟仍會與設定 Azure SQL Database 的作業代理程式相同。

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Azure SQL Database 的作業代理程式

若要建立可針對 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)執行預存程式的作業，請使用 [Azure 彈性作業代理程式](../azure-sql/database/elastic-jobs-overview.md)。 在 Azure 入口網站中建立您的作業代理程式。 這種方法會將數個預存程式加入至代理程式所使用的資料庫，也稱為 *代理程式資料庫* 。 然後，您可以建立在目標資料庫中執行預存程式的作業，並在完成時捕捉輸出。

您必須設定許可權、群組和目標，如 [Azure 彈性作業代理程式的完整檔](../azure-sql/database/elastic-jobs-overview.md)所述，您才能建立作業。 您也需要在目標資料庫中建立支援資料表，如下列各節所述。

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>建立用來註冊參數和儲存輸入的狀態資料表

SQL Agent 作業不接受輸入參數。 相反地，在目標資料庫中，建立您註冊參數的狀態資料表，並儲存用來呼叫預存程式的輸入。 所有的代理程式作業步驟都會針對目標資料庫執行，但是作業的預存程式會針對代理程式資料庫執行。 

若要建立狀態資料表，請使用此架構：

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

以下是產生的資料表在 [SQL Server Management Studio (SMSS) ](/sql/ssms/download-sql-server-management-studio-ssms)中的外觀：

![顯示儲存預存程式之輸入的已建立狀態資料表的螢幕擷取畫面。](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

為了確保良好的效能，並確定代理程式作業可以找到相關聯的記錄，此資料表會使用作業執行識別碼 (`jobid`) 做為主鍵。 如果您想要的話，也可以新增輸入參數的個別資料行。 先前所述的架構可以更一般地處理多個參數，但受限於所計算的大小 `NVARCHAR(MAX)` 。

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>建立最上層作業來執行預存程式

若要執行長時間執行的預存程式，請在代理程式資料庫中建立此最上層作業代理程式：

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

現在，將步驟加入至參數化、執行和完成預存程式的作業。 依預設，作業步驟會在12小時後過期。 如果您的預存程式需要更多時間，或如果您想要讓程式提前時間，您可以將 `step_timeout_seconds` 參數變更為以秒為單位指定的另一個值。 依預設，步驟有10個內建的重試，而且每次重試之間會有輪詢超時時間，您可以將其用於您的優點。

以下是新增的步驟：

1. 等候參數出現在 `LongRunningState` 資料表中。

   第一個步驟會等待資料表中加入的參數 `LongRunningState` ，這會在作業啟動之後立即發生。 如果作業執行識別碼 (`jobid`) 不會加入至 `LongRunningState` 資料表中，此步驟只會失敗，而預設的重試或輪詢超時會等候：

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 查詢狀態資料表中的參數，並將其傳遞至預存程式。 此步驟也會在背景中執行程式。 

   如果您的預存程式不需要參數，只要直接呼叫預存程式即可。 否則，若要傳遞 `@timespan` 參數，請使用 `@callparams` ，您也可以將它擴充以傳遞額外的參數。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 完成工作並記錄結果。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>啟動作業並傳遞參數

若要啟動作業，請使用 [**執行 SQL 查詢** 動作](/connectors/sql/#execute-a-sql-query-(v2)) 的傳遞原生查詢，並立即將作業的參數推送至狀態資料表。 若要 `jobid` 在目標資料表中提供屬性的輸入，Logic Apps 加入 **for each** 迴圈，以逐一查看上述動作的資料表輸出。 針對每個作業執行識別碼，執行使用動態資料輸出的 **插入資料列** 動作， `ResultSets JobExecutionId` 以加入作業的參數以解壓縮並傳遞至目標預存程式。

![螢幕擷取畫面，顯示用於啟動作業和將參數傳遞至預存程式的動作。](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

當工作完成時，作業 `LongRunningState` 會更新資料表，讓您可以使用 [ [**修改專案時** ] 觸發](/connectors/sql/#when-an-item-is-modified-(v2))程式，輕鬆地在結果上觸發。 如果您不需要輸出，或如果您已經有監視輸出資料表的觸發程式，則可以略過此部分。

![顯示修改專案時之 SQL 觸發程式的螢幕擷取畫面。](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server 或 Azure SQL 受控執行個體的作業代理程式

在相同的案例中，您可以使用[SQL Server 內部部署](/sql/sql-server/sql-server-technical-documentation)和[Azure SQL 受控執行個體](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)的[SQL Server Agent](/sql/ssms/agent/sql-server-agent) 。 雖然有些管理細節不同，但基礎步驟仍會與設定 Azure SQL Database 的作業代理程式相同。

## <a name="next-steps"></a>下一步

[連接到 SQL Server、Azure SQL Database 或 Azure SQL 受控執行個體](../connectors/connectors-create-api-sqlazure.md)

