---
title: '使用 Azure 資料總管 (preview 從 Azure 監視器查詢匯出的資料) '
description: 使用 Azure 資料總管來查詢從 Log Analytics 工作區匯出到 Azure 儲存體帳戶的資料。
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: ec695912dcd59b474df132cac97d9069da4c5d51
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049699"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管 (preview 從 Azure 監視器查詢匯出的資料) 
將資料從 Azure 監視器匯出至 Azure 儲存體帳戶可提供低成本的保留，以及將記錄重新配置到不同區域的能力。 使用 Azure 資料總管來查詢從 Log Analytics 工作區匯出的資料。 一旦設定之後，從您的工作區傳送到 Azure 儲存體帳戶的支援資料表將可作為 Azure 資料總管的資料來源。

程式流程如下所示： 

1.  將資料從 Log Analytics 工作區匯出至 Azure 儲存體帳戶。
2.  在您的 Azure 資料總管叢集中建立外部資料表，並針對資料類型進行對應。
3.  從 Azure 資料總管查詢資料。

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure 資料總管匯出的資料查詢流程。":::



## <a name="send-data-to-azure-storage"></a>將資料傳送至 Azure 儲存體
您可以使用下列任何選項，將 Azure 監視器記錄匯出至 Azure 儲存體帳戶。

- 若要將 Log Analytics 工作區中的所有資料匯出至 Azure 儲存體帳戶或事件中樞，請使用 Azure 監視器記錄的 Log Analytics 工作區資料匯出功能。 請參閱 [Azure 監視器 (preview 中的 Log Analytics 工作區資料匯出) ](logs-data-export.md)
- 使用邏輯應用程式從記錄查詢排程匯出。 這類似于資料匯出功能，但可讓您將已篩選或匯總的資料傳送至 Azure 儲存體。 不過，此方法受限於 [記錄查詢限制](../service-limits.md#log-analytics-workspaces)  ，請參閱 [使用邏輯應用程式將資料從 log Analytics 工作區封存至 Azure 儲存體](logs-export-logic-app.md)。
- 使用邏輯應用程式進行一次匯出。 請參閱 [Logic Apps 和 Power Automate 的 Azure 監視器記錄連接器](logicapp-flow-connector.md)。
- 使用 PowerShell 腳本一次匯出到本機電腦。 請參閱 [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。

> [!TIP]
> 您可以使用現有的 Azure 資料總管叢集，或使用所需的設定來建立新的專用叢集。

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>建立位於 Azure blob 儲存體中的外部資料表
使用 [外部資料表](/azure/data-explorer/kusto/query/schema-entities/externaltables) 將 azure 資料總管連結至 azure 儲存體帳戶。 外部資料表是 Kusto 架構實體，可參考儲存在 Kusto 資料庫外部的資料。 就像資料表一樣，外部資料表具有妥善定義的架構。 和資料表不同的是，資料會儲存在 Kusto 叢集外部並進行管理。 從上一節匯出的資料會儲存在 JSON 行中。

若要建立參考，您需要匯出資料表的架構。 使用 Log Analytics 中的 [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 運算子來取得此資訊，其中包含資料表的資料行及其資料類型。

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Azure 資料總管匯出的資料查詢流程。":::

您現在可以使用輸出來建立 Kusto 查詢，以建立外部資料表。
遵循在 [Azure 儲存體或 Azure Data Lake 中建立和變更外部資料表](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)的指引，以 JSON 格式建立外部資料表，然後從您的 Azure 資料總管資料庫執行查詢。

>[!NOTE]
>外部資料表的建立是由兩個進程所建立。 第一個是建立外部資料表，而第二個則是建立對應。

下列 PowerShell 腳本會建立資料表和對應的 [建立](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) 命令。

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The log lanlyics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The log lanlyics WorkspaceId
    $WorkspaceName, # The log lanlyics workspace name
    $BlobURL, # The Blob URL where to save
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}

$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'

)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

下圖顯示輸出的範例和範例。

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Azure 資料總管匯出的資料查詢流程。":::

[![範例輸出](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>複製、貼上，然後在 Azure 資料總管用戶端工具中執行腳本的輸出，以建立資料表和對應。

## <a name="query-the-exported-data-from-azure-data-explorer"></a>從 Azure 資料總管查詢匯出的資料 

在設定對應之後，您可以從 Azure 資料總管查詢匯出的資料。 您的查詢需要 [external_table](/azure/data-explorer/kusto/query/externaltablefunction) 函式，如下列範例所示。

```kusto
external_table("HBTest","map") | take 10000
```

[![查詢 Log Analytics 匯出的資料](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在 Azure 資料總管中撰寫查詢](https://docs.microsoft.com/azure/data-explorer/write-queries)