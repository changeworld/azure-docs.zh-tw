---
title: 如何監視 Azure 範疇
description: 瞭解如何使用 Azure 監視器來設定 Azure 範疇計量、警示和診斷設定。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 2c21f84b9a10db504afb8ead67ae479518a0afba
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603196"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure 監視器中的 Azure 範疇計量

本文說明如何使用 Azure 監視器設定 Azure 範疇的計量、警示和診斷設定。

## <a name="monitor-azure-purview"></a>監視 Azure 範疇

Azure 範疇系統管理員可以使用 Azure 監視器來追蹤範疇帳戶的操作狀態。 系統會收集計量以提供資料點，讓您追蹤潛在的問題、進行疑難排解，以及改善範疇帳戶的可靠性。 計量會針對 Azure 範疇中發生的事件傳送至 Azure 監視器。

## <a name="aggregated-metrics"></a>匯總的計量

您可以從範疇帳戶的 Azure 入口網站存取計量。 計量的存取權是由範疇帳戶的角色指派所控制。 使用者必須是 Azure 範疇中「監視讀者」角色的一部分，才能查看計量。 查看 [監視讀取者角色許可權](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles) ，以深入瞭解角色存取層級。

建立範疇帳戶的人員會自動取得查看度量的許可權。 如果任何人想要查看計量，請依照下列步驟將其新增至「 **監視讀者** 」角色：

### <a name="add-a-user-to-the-monitoring-reader-role"></a>將使用者新增至監視讀者角色

若要將使用者新增至「 **監視讀者** 」角色，範疇帳戶或訂用帳戶擁有者的擁有者可以遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com) 並搜尋 Azure 範疇帳戶名稱。

2. 選取 [存取控制 (IAM)]。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="顯示如何存取 IAM 的螢幕擷取畫面。":::

3. 選取 [ **新增角色指派**]。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="顯示如何新增角色指派的螢幕擷取畫面。":::

4. 選取角色 **監視讀取器** ，並設定指派存取權給 **Azure AD 使用者、群組或服務主體**。 並指派 AAD 帳戶以存取計量。  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="顯示如何新增「監視讀者」角色的螢幕擷取畫面。":::

## <a name="metrics-visualization"></a>計量視覺效果

「 **監視讀者** 」角色中的使用者可以看到傳送給 Azure 監視器的匯總計量和診斷記錄。 計量會列在對應範疇帳戶的 Azure 入口網站中。 在 Azure 入口網站中，選取 [計量] 區段以查看所有可用計量的清單。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="顯示可用範疇計量區段的螢幕擷取畫面。" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure 範疇使用者也可以直接從 Azure 範疇帳戶的管理中心存取 [計量] 頁面。 在範疇管理中心的主頁面中選取 Azure 監視器，以啟動 Azure 入口網站。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="從管理中心啟動範疇計量的螢幕擷取畫面。" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>可用的計量

若要取得熟悉，瞭解如何使用 Azure 入口網站中的 [計量] 區段，請參閱下列兩份檔。 [開始使用](../azure-monitor/platform/metrics-getting-started.md) 計量瀏覽器和計量 [瀏覽器的先進功能](../azure-monitor/platform/metrics-charts.md)。

下表包含可在 Azure 入口網站中探索的計量清單：

| 標準名稱 | 計量命名空間 | 彙總類型 | 描述 |
| ------------------- | ------------------- | ------------------- | ----------------- |
| 取消掃描 | 自動掃描 | Sum <br> Count | 匯總一段時間內取消的資料來源掃描 |
| 掃描已完成 | 自動掃描 | Sum <br> Count | 匯總一段時間內完成的資料來源掃描 |
| 掃描失敗 | 自動掃描 | Sum <br> Count | 匯總一段時間內失敗的資料來源掃描 |
| 掃描花費時間 | 自動掃描 | 最小值 <br> 最大值 <br> Sum <br> 平均 | 匯總在一段時間內掃描所花費的總時間 |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Azure 儲存體帳戶的診斷記錄

原始遙測事件會發出至 Azure 監視器。 您可以將事件記錄到選擇的客戶儲存體帳戶，以供進一步分析。 您可以透過 Azure 入口網站上範疇帳戶的診斷設定來匯出記錄檔。

遵循下列步驟來建立 Azure 範疇帳戶的診斷設定。

1. 依照下列文章建立新的診斷設定，以收集平臺記錄和計量： [建立診斷設定以將平臺記錄和計量傳送至不同的目的地](../azure-monitor/platform/diagnostic-settings.md)。 選取 [僅目的地] 作為 Azure 儲存體帳戶。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="顯示建立診斷記錄的螢幕擷取畫面。" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. 將事件記錄到儲存體帳戶。 建議將專用儲存體帳戶用於封存診斷記錄。 遵循本文以 [建立儲存體帳戶](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)。

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="顯示指派診斷記錄之儲存體帳戶的螢幕擷取畫面。" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

在新建立的儲存體帳戶中，最多允許15分鐘的時間開始接收記錄。 [請參閱 Azure 儲存體帳戶中的資料保留和資源記錄的架構](../azure-monitor/platform/resource-logs-collect-storage.md)。 設定診斷記錄之後，事件會流向儲存體帳戶。

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

此事件會追蹤掃描生命週期。 掃描工作會遵循一連串狀態的進度，從佇列、執行中，最後是已成功的終端狀態 |失敗 |取消。 記錄每個狀態轉換的事件，事件的架構將會有下列屬性。

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

事件實例的範例記錄如下一節所示。

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>後續步驟

[查看資產深入解析](asset-insights.md)
