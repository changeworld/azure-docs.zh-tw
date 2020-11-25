---
title: 在 Azure Data Factory 中建立排程觸發程式
description: 了解如何在 Azure Data Factory 中建立依排程執行管線的處發程序。
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.author: chez
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: a6f53d6ce41085b2348857ccb5b45c06132d6a99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001978"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>建立依排程執行管線的觸發程序
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有關排程觸發程序的資訊，以及建立、啟動和監視排程觸發程序的步驟。 如需了解其他類型的觸發程序，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md)。

建立排程觸發程序時，您需為觸發程序指定排程 (開始日期、週期、結束日期等)，然後與管線建立關聯。 管道和觸發程序具有多對多關聯性。 多個觸發程序可以啟動單一管道。 單一觸發程序可以啟動多個管道。

下列各節提供以各種不同方式建立排程觸發程序的步驟。 

## <a name="data-factory-ui"></a>Data Factory UI
您可以建立 **排程觸發程序**，將管線排定為定期執行 (每小時、每天等)。 

> [!NOTE]
> 如需建立管線和排程觸發程式的完整逐步解說，以將觸發程式與管線產生關聯，以及執行和監視管線，請參閱 [快速入門：使用 DATA FACTORY UI 建立資料](quickstart-create-data-factory-portal.md)處理站。

1. 切換至 [ **編輯** ] 索引標籤，並以鉛筆符號顯示。 

    ![切換至 [編輯] 索引標籤](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. 選取功能表上的 [ **觸發** 程式]，然後選取 [ **新增/編輯**]。 

    ![新增觸發程序功能表](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. 在 [ **新增觸發** 程式] 頁面上，選取 **[選擇觸發程式 ...**]，然後選取 [ **+ 新增**]。 

    ![新增觸發程序 - 新增觸發程序](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. 在 [ **新增觸發** 程式] 頁面上，執行下列步驟： 

    1. 確認已針對 [類型] 選取 [排程]。
    1. 指定 **開始日期** 觸發程式的開始日期時間。 根據預設，它會設定為國際標準時間 (UTC) 中目前的日期時間。
    1. 指定將在其中建立觸發程式的時區。 時區設定將會套用至 [ **開始日期**]、[ **結束日期**] 和 [排定的週期選項] 中的 [ **排程執行時間** ]。 [變更時區] 設定不會自動變更您的開始日期。 請確定指定的時區中的開始日期正確。 請注意，觸發程式的排程執行時間將會被視為在開始日期之後 (確定開始日期至少1minute 小於執行時間，否則會在下一個週期) 觸發管線。 

        > [!NOTE]
        > 對於觀察日光節約的時區，觸發程式時間會自動調整一年的兩次。 若選擇不使用日光節約變更，請選取不會觀察日光節約的時區，例如 UTC

    1. 指定觸發程序的 [週期]。 請從下拉式清單 (每分鐘、每小時、每天、每週及每月) 中選取其中一個值。 在文字方塊中輸入乘數。 例如，如果您想要讓觸發程序每隔 15 分鐘執行一次，您可以選取 [每分鐘]，然後在文字方塊中輸入 **15**。 
    1. 若要指定結束日期時間，請選取 [ **指定結束日期**]，並指定 [ _結束_ 日期]，然後選取 **[確定]**。 每次執行管線都會產生相關成本。 如果您是在進行測試，您可能會想要確保只觸發管線幾次。 不過，請確定發佈時間與結束時間之間有足夠的時間可供管線執行。 在您將方案發佈至 Data Factory 之後，觸發程序才會生效，而不是在您將觸發程序儲存在 UI 時生效。

        ![觸發程序設定](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![結束日期的觸發程式設定](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. 在 [**新增觸發** 程式] 視窗中，選取 [**啟用**] 選項中的 **[是**]，然後選取 **[確定]**。 您可以稍後使用此核取方塊來停用觸發程序。 

    ![觸發程序設定 - 下一步按鈕](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. 在 [ **新增觸發** 程式] 視窗中，檢查警告訊息，然後選取 **[確定]**。

    ![觸發程序設定 - 完成按鈕](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. 選取 [ **全部發佈** ]，將變更發佈至 Data Factory。 在您將變更發佈至 Data Factory 之前，觸發程式不會開始觸發管線執行。 

    ![發佈按鈕](./media/how-to-create-schedule-trigger/publish-2.png)

1. 切換至左側的 [ **管線執行** ] 索引標籤， **然後選取 [** 重新整理] 以重新整理清單。 您會看到排程的觸發程式所觸發的管線執行。 請注意 [ **觸發者** ] 資料行中的值。 如果您使用 [ **立即觸發** ] 選項，就會在清單中看到手動觸發程式執行。 

    ![監視已觸發的執行](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. 切換至 [**觸發程式執行**  \  **排程**] view。 

    ![監視觸發程序執行](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本節說明如何使用 Azure PowerShell 來建立、啟動及監視排程觸發程序。 若要查看此範例如何運作，請先完成[快速入門：使用 Azure PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)。 然後，將下列程式碼新增至 main 方法中，以建立並啟動一個每隔 15 分鐘執行一次的排程觸發程序。 此觸發程序會與您在快速入門中所建立之名為 **Adfv2QuickStartPipeline** 的管線關聯。

1. 在 C:\ADFv2QuickStartPSH\ 資料夾中，使用下列內容建立名為 **MyTrigger.json** 的 JSON 檔案：

    > [!IMPORTANT]
    > 在您儲存 JSON 檔案之前，請先將 **startTime** 元素的值設定為目前的 UTC 時間。 將 **endTime** 元素的值設定為目前 UTC 時間的一小時之後。

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    在 JSON 程式碼片段中：
    - 觸發程式的 **type** 元素設定為 "ScheduleTrigger"。
    - **frequency** 元素是設定為 "Minute"，而 **interval** 元素是設定為 15。 因此，觸發程式會在開始與結束時間之間每隔15分鐘執行管線。
    - **時區** 元素會指定建立觸發程式的時區。 這項設定會影響 **startTime** 和 **endTime**。
    - **endTime** 元素是在 **startTime** 元素值的一小時之後。 因此，觸發程式會在開始時間之後的15分鐘、30分鐘和45分鐘執行管線。 請別忘記將開始時間更新為目前的 UTC 時間，並將結束時間更新為開始時間的一小時之後。 

        > [!IMPORTANT]
        > 針對 UTC 時區，startTime 和 endTime 必須遵循格式 ' yyyy-mm-dd ddTHH： MM： ss **Z**'，而針對其他時區，Starttime 和 endtime 則遵循 ' yyyy-mm-dd： mm： ss '。 
        > 
        > 根據 ISO 8601 標準，時間戳記的 _Z_ 尾碼會將 datetime 標示為 UTC 時區，並將時區欄位呈現為無用。 當缺少 UTC 時區的 _Z_ 尾碼時，將會在觸發程式 _啟用_ 時產生錯誤。

    - 此觸發程序與 **Adfv2QuickStartPipeline** 管線關聯。 若要將多個管線與觸發程序建立關聯，請新增更多個 **pipelineReference** 區段。
    - 快速入門中的管線會採用兩個 **參數** 值：**inputPath** 和 **outputPath**。 您可以從觸發程式傳遞這些參數的值。

1. 使用 **>start-azdatafactoryv2trigger** Cmdlet 建立觸發程式：

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. 使用 **>start-azdatafactoryv2trigger** 指令程式確認觸發程式的狀態已 **停止**：

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. 使用 **>start-azdatafactoryv2trigger** Cmdlet 來啟動觸發程式：

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. 使用 **>start-azdatafactoryv2trigger** 指令程式確認觸發程式的狀態已 **啟動**：

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  使用 **AzDataFactoryV2TriggerRun** 指令程式取得在 Azure PowerShell 中執行的觸發程式。 若要取得有關觸發程序回合的資訊，請定期執行以下命令。 更新 **TriggerRunStartedAfter** 和 **TriggerRunStartedBefore** 的值，以符合您的觸發程序定義中的值：

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > 排程觸發程式的觸發時間是以 UTC 時間戳記來指定。 _TriggerRunStartedAfter_ 和 _TriggerRunStartedBefore_ 也預期會有 UTC 時間戳記

    若要在 Azure 入口網站中監視觸發程序回合和管線回合，請參閱[監視管線回合](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。



## <a name="net-sdk"></a>.NET SDK
本節說明如何使用 .NET SDK 來建立、啟動及監視觸發程序。 若要查看此範例如何運作，請先完成[快速入門：使用 .NET SDK 來建立資料處理站](quickstart-create-data-factory-dot-net.md)。 然後，將下列程式碼新增至 main 方法中，以建立並啟動一個每隔 15 分鐘執行一次的排程觸發程序。 此觸發程序會與您在快速入門中所建立之名為 **Adfv2QuickStartPipeline** 的管線關聯。

若要建立並啟動一個每隔 15 分鐘執行一次的排程觸發程序，請將下列程式碼新增至 main 方法中：

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

若要在不同時區（UTC 以外）建立觸發程式，則需要下列設定：
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

若要監視觸發程序回合，請將下列程式碼新增至範例中的最後一個 `Console.WriteLine` 陳述式之前：

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

若要在 Azure 入口網站中監視觸發程序回合和管線回合，請參閱[監視管線回合](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。


## <a name="python-sdk"></a>Python SDK
本節說明如何使用 Python SDK 來建立、啟動及監視觸發程序。 若要查看此範例如何運作，請先完成[快速入門：使用 Python SDK 來建立資料處理站](quickstart-create-data-factory-python.md)。 然後，將下列程式碼新增至 Python 指令碼中的 "monitor the pipeline run" 程式碼區塊之後。 此程式碼會建立一個在指定的開始和結束時間之間，每隔 15 分鐘執行一次的排程觸發程序。 請將 **start_time** 變數更新為目前的 UTC 時間，並將 **end_time** 變數更新為目前 UTC 時間的一小時之後。

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

若要在 Azure 入口網站中監視觸發程序回合和管線回合，請參閱[監視管線回合](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
您可以使用 Azure Resource Manager 範本來建立觸發程序。 如需逐步指示，請參閱[使用 Azure Resource Manager 範本建立 Azure Data Factory](quickstart-create-data-factory-resource-manager-template.md)。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>將觸發程序開始時間傳遞給管線
第 1 版 Azure Data Factory 支援使用下列系統變數來讀取或寫入分割的資料：**SliceStart****SliceEnd****WindowStart** 及 **WindowEnd**。 在目前版本的 Azure Data Factory 中，您可以使用管線參數來實現此行為。 觸發程序的開始時間和排程時間會設定為管線參數的值。 在以下範例中，會將觸發程序的排程時間傳遞給管線的 **scheduledRunTime** 參數來作為值：

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON 結構描述
下列 JSON 定義將說明如何使用排程和週期來建立排程觸發程序：

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **parameters** 屬性是 **pipelines** 元素的必要屬性。 如果您的管線未採用任何參數，您就必須為 **parameters** 屬性加入一個空的 JSON 定義。


### <a name="schema-overview"></a>結構描述概觀
下表提供與觸發程序之週期和排程相關的主要結構描述元素概觀：

| JSON 屬性 | 描述 |
|:--- |:--- |
| **startTime** | 日期時間值。 在簡易排程中，**startTime** 屬性的值會套用至第一個發生項目。 在複雜的排程中，觸發程序會在到了指定的 **startTime** 值才啟動。 <br> 針對 UTC 時區，格式為 `'yyyy-MM-ddTHH:mm:ssZ'` ，其他時區的格式為 `'yyyy-MM-ddTHH:mm:ss'` 。 |
| **endTime** | 觸發程序的結束日期和時間。 觸發程序在指定的結束日期和時間之後便不再執行。 此屬性的值不可以是過去的時間。 這是選用屬性。  <br> 針對 UTC 時區，格式為 `'yyyy-MM-ddTHH:mm:ssZ'` ，其他時區的格式為 `'yyyy-MM-ddTHH:mm:ss'` 。 |
| **時區** | 觸發程式建立所在的時區。 此設定會影響 **startTime**、 **endTime** 和 **schedule**。 查看 [支援的時區清單](#time-zone-option) |
| **復發** | 指定觸發程序之週期規則的 recurrence 物件。 recurrence 物件支援 **frequency**、**interval**、**endTime**、**count** 及 **schedule** 元素。 定義 recurrence 物件時，必須一併定義 **frequency** 元素。 其他 recurrence 物件元素則為選用元素。 |
| **頻率** | 觸發程序重複執行時的頻率單位。 支援的值包括 "minute"、"hour"、"day"、"week" 及 "month"。 |
| **區間** | 代表 **frequency** 值之間隔的整數值，用來決定觸發程序執行的頻率。 例如，如果 **interval** 為 3，而 **frequency** 為 "week"，觸發程序就會每隔 3 週重複執行一次。 |
| **附表** | 觸發程序的週期排程。 具有指定之 **frequency** 值的觸發程序會根據週期排程來改變其週期。 **schedule** 屬性會根據分鐘、小時、星期幾、月日及週數來修改週期。

> [!IMPORTANT]
> 針對 UTC 時區，startTime 和 endTime 必須遵循格式 ' yyyy-mm-dd ddTHH： MM： ss **Z**'，而針對其他時區，Starttime 和 endtime 則遵循 ' yyyy-mm-dd： mm： ss '。 
> 
> 根據 ISO 8601 標準，時間戳記的 _Z_ 尾碼會將 datetime 標示為 UTC 時區，並將時區欄位呈現為無用。 當缺少 UTC 時區的 _Z_ 尾碼時，將會在觸發程式 _啟用_ 時產生錯誤。

### <a name="schema-defaults-limits-and-examples"></a>結構描述預設值、限制及範例

| JSON 屬性 | 類型 | 必要 | 預設值 | 有效值 | 範例 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | 是 | None | ISO 8601 日期時間 | UTC 時區 `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> 針對其他時區 `"2013-01-09T09:30:00-08:00"` |
| **時區** | String | 是 | None | [時區值](#time-zone-option)  | `"UTC"` |
| **復發** | Object | 是 | None | Recurrence 物件 | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **區間** | 數字 | 否 | 1 | 1 到 1,000 | `"interval":10` |
| **endTime** | String | 是 | None | 代表未來時間的日期時間值。 | UTC 時區 `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> 針對其他時區 `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **附表** | Object | 否 | None | Schedule 物件 | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>時區選項

以下是排程觸發程式支援的部分時區：

| 時區 | UTC 時差 (非日光節約)  | 時區值 | 觀察日光節約 | 時間戳記格式 |
| :--- | :--- | :--- | :--- | :--- |
| 國際標準時間 | 0 | `UTC` | 否 | `'yyyy-MM-ddTHH:mm:ssZ'`|
| 太平洋時間 (PT)  | -8 | `Pacific Standard Time` | 是 | `'yyyy-MM-ddTHH:mm:ss'` |
| 中部時間 (CT)  | -6 | `Central Standard Time` | 是 | `'yyyy-MM-ddTHH:mm:ss'` |
| 東部時間 (ET)  | -5 | `Eastern Standard Time` | 是 | `'yyyy-MM-ddTHH:mm:ss'` |
| 格林尼治平均時間 (GMT)  | 0 | `GMT Standard Time` | 是 | `'yyyy-MM-ddTHH:mm:ss'` |
| 歐洲中部標準時間 | +1 | `W. Europe Standard Time` | 是 | `'yyyy-MM-ddTHH:mm:ss'` |
| 印度標準時間 (IST)  | + 5:30 | `India Standard Time` | 否 | `'yyyy-MM-ddTHH:mm:ss'` |
| 中國標準時間 | + 8 | `China Standard Time` | 否 | `'yyyy-MM-ddTHH:mm:ss'` |

這份清單不完整。 如需時區選項的完整清單，請流覽 Data Factory 入口網站 [觸發程式建立頁面](#data-factory-ui)

### <a name="starttime-property"></a>startTime 屬性
下表說明 **startTime** 屬性如何控制觸發程序執行：

| startTime 值 | 週期性無排程 | 週期性有排程 |
|:--- |:--- |:--- |
| 開始時間已過去 | 計算開始時間之後的第一個未來執行時間，並在該時間執行。<br/><br/>根據從上次執行時間算出的時間來執行後續的執行作業。<br/><br/>請參閱本表後面的範例。 | 觸發程式啟動的時間 _不會超過_ 指定的開始時間。 第一次執行是根據從開始時間算出的排程。<br/><br/>根據週期排程執行後續的執行作業。 |
| 開始時間在未來或現在 | 在指定的開始時間執行一次。<br/><br/>根據從上次執行時間算出的時間來執行後續的執行作業。 | 觸發程序會在「到了」指定的開始時間才啟動。 第一次執行是根據從開始時間算出的排程。<br/><br/>根據週期排程執行後續的執行作業。 |

我們來看看一個範例：當開始時間在過去、具有週期性但無排程時，會發生什麼情況。 假設目前時間是 `2017-04-08 13:00`，開始時間是 `2017-04-07 14:00`，而週期是每隔兩天。  (週期性 **值是** 藉由將 **frequency** 屬性設為 "day"，並將 **interval** 屬性設定為2來定義。 ) 注意 **startTime** 值是在過去，並且發生在目前時間之前。

在這些情況下，第一次執行的位置是 `2017-04-09` `14:00` 。 排程器引擎會從開始時間計算執行週期。 過去的任何執行個體都會遭到捨棄。 引擎會使用下一個在未來發生的執行個體。 在此案例中，開始時間是 `2017-04-07` 在上 `2:00pm` ，因此下一個實例是該時間的兩天，也就是 `2017-04-09` `2:00pm` 。

即使 **startTime** 值為 `2017-04-05 14:00` 或 `2017-04-01 14:00`，第一次執行時間仍然相同。 在第一次執行之後，就會使用排程來算出後續的執行時間。 因此，後續的執行作業位於 at、 `2017-04-11` `2:00pm` then `2017-04-13` at、 `2:00pm` and `2017-04-15` 等 `2:00pm` 。

最後，當未在觸發程序的排程中設定小時或分鐘時，將會使用第一次執行的小時或分鐘作為預設值。

### <a name="schedule-property"></a>schedule 屬性
一方面，使用排程可以限制觸發程序的執行次數。 例如，如果將每月執行的觸發程序排定為只在 31 日執行，則該觸發程序將只會在有第 31 天的月份執行。

另一方面，排程也可以增加觸發程序的執行次數。 例如，如果將每月執行的觸發程序排定在月份的 1 日和 2 日執行，該觸發程序就會在月份的第 1 天和第 2 天執行，而不是一個月執行一次。

指定多個 **schedule** 元素時，評估順序會從最大到最小排程設定。 評估會從週數開始，然後依序是月日、星期幾、小時，最後是分鐘。

下表詳細說明 **schedule** 元素：


| JSON 元素 | 描述 | 有效值 |
|:--- |:--- |:--- |
| **分鐘** | 一小時內觸發程序執行的分鐘數。 | <ul><li>整數</li><li>一連串整數</li></ul>
| **小時** | 一天內觸發程序執行的小時數。 | <ul><li>整數</li><li>一連串整數</li></ul> |
| **平日** | 觸發程序在一週中的執行日。 此值只能與 weekly 頻率搭配指定。 | <ul><li>Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday</li><li>日期值陣列 (最大陣列大小為 7)</li><li>日值不區分大小寫</li></ul> |
| **monthlyOccurrences** | 觸發程序在一個月中的執行日。 此值只能與 monthly 頻率搭配指定。 | <ul><li>**monthlyOccurrence** 物件的陣列︰`{ "day": day,  "occurrence": occurrence }`。</li><li>**day** 屬性是觸發程序在一週中的執行日。 例如，**day** 值為 `{Sunday}` 的 **monthlyOccurrences** 屬性意謂著月份中的每個星期日。 **day** 屬性為必要屬性。</li><li>**occurrence** 屬性係指所指定的 **day** 在月份中出現的位置。 例如，**day** 和 **occurrence** 值為 `{Sunday, -1}` 的 **monthlyOccurrences** 屬性意謂著月份中的最後一個星期日。 **occurrence** 屬性為選用屬性。</li></ul> |
| **monthDays** | 觸發程序在一個月中的執行日。 此值只能與 monthly 頻率搭配指定。 | <ul><li><= -1 和 >= -31 的任何值</li><li>>= 1 和 <= 31 任何值</li><li>值的陣列</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>觸發程序週期排程的範例
本節提供週期排程的範例，並將焦點放在 **schedule** 物件及其元素。

這些範例會假設 **interval** 值為 1，且 **frequency** 值根據排程定義是正確的。 例如，您不能既將 **frequency** 值設定為 "day"，又在 **schedule** 物件中包含 "monthDays"修改。 在上一節的表格中已提到這類限制。

| 範例 | 描述 |
|:--- |:--- |
| `{"hours":[5]}` | 在每天的上午 5:00 執行。 |
| `{"minutes":[15], "hours":[5]}` | 在每天的上午 5:15 執行。 |
| `{"minutes":[15], "hours":[5,17]}` | 在每天的上午 5:15 和下午 5:15 執行。 |
| `{"minutes":[15,45], "hours":[5,17]}` | 在每天的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |
| `{"minutes":[0,15,30,45]}` | 每隔 15 分鐘執行一次。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | 每小時執行一次。 此觸發程序每小時執行一次。 已指定值時，分鐘會受 **startTime** 值控制。 如果未指定值，分鐘就會受建立時間控制。 例如，如果開始時間或建立時間 (無論套用哪一個) 為下午 12:25，觸發程序就會在 00:25、01:25、02:25、… 及 23:25 執行。<br/><br/>此排程相當於將觸發程序設定成 **frequency** 值為 "hour"、**interval** 值為 1 且沒有 **schedule**。  此排程可以與不同的 **frequency** 和 **interval** 值搭配使用，以建立其他觸發程序。 例如，當 **frequency** 值為 "month" 時，排程一個月只會執行一次，而不是像 **frequency** 值為 "day" 時會每天執行。 |
| `{"minutes":[0]}` | 在每小時整點執行。 此觸發程序會在每小時整點執行，從上午 12:00 開始，接著在上午 1:00、上午 2:00，依此類推。<br/><br/>此排程相當於將觸發程序設定成 **frequency** 值為 "hour"、**startTime** 值為零分鐘，或沒有 **schedule** 但 **frequency** 值為 "day"。 如果 **frequency** 值為 "week" 或 "month"，排程將只會分別在每週的一天或每個月的一天執行。 |
| `{"minutes":[15]}` | 在每小時的 15 分執行。 此觸發程序會在每小時的 15 分執行，從上午 00:15 開始，接著在上午 1:15、上午 2:15，依此類推，並在下午 11:15 結束。 |
| `{"hours":[17], "weekDays":["saturday"]}` | 在每週星期六的下午 5:00 執行。 |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三及星期五的下午 5:00 執行。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | 在每週星期一、星期三及星期五的下午 5:15 和 5:45 執行。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日每隔 15 分鐘執行一次。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 在工作日的上午 9:00 與下午 4:45 之間每隔 15 分鐘執行一次。 |
| `{"weekDays":["tuesday", "thursday"]}` | 在星期二和星期四的指定開始時間執行。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | 在每個月的第 28 天上午 6:00 執行 (假設 **frequency** 值為 "month")。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | 在月份最後一天的上午 6:00 執行。 若要在月份的最後一天執行觸發程序，請使用 -1，而不要使用 28、29、30 或 31。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | 在每個月第一天和最後一天的上午 6:00 執行。 |
| `{monthDays":[1,14]}` | 在每個月第一天和第十四天的指定開始時間執行。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五的上午 5:00 執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | 在每個月第一個星期五的指定開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | 在每個月倒數第三個星期五的指定開始時間執行。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五的上午 5:15 執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | 在每個月第一個和最後一個星期五的指定開始時間執行。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | 在每個月第五個星期五的指定開始時間執行。 如果月份中沒有第五個星期五，管線就不會執行，因為它已排定為只在第五個星期五執行。 若要在月份中最後一個出現的星期五執行觸發程序，請考慮使用 -1 而不是 5 來作為 **occurrence** 的值。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | 在月份中最後一個星期五每隔 15 分鐘執行一次。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | 在每個月第三個星期三的上午 5:15、上午 5:45、下午 5:15 及下午 5:45 執行。 |


## <a name="next-steps"></a>後續步驟
如需有關觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#trigger-execution)。
