---
title: 使用診斷記錄來監視 Azure 資料總管擷取作業
description: 瞭解如何為 Azure 資料資源管理器設置診斷日誌以監視引入操作。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277435"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>使用診斷日誌監視 Azure 資料資源管理器引入操作（預覽）

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後，將（載入）資料引入資料庫中的表，以便可以針對它執行查詢。 [Azure 監視器診斷日誌](/azure/azure-monitor/platform/diagnostic-logs-overview)提供有關 Azure 資源操作的資料。 Azure 資料資源管理器使用診斷日誌來深入瞭解引入成功和失敗。 您可以將動作記錄匯出到 Azure 存儲、事件中心或日誌分析以監視引入狀態。 Azure 存儲和 Azure 事件中心的日誌可以路由到 Azure 資料資源管理器群集中的表以進行進一步分析。

## <a name="prerequisites"></a>Prerequisites

* 如果沒有 Azure 訂閱，請創建[一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* 創建[群集和資料庫](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com/)。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>為 Azure 資料資源管理器群集設置診斷日誌

診斷日誌可用於配置以下日誌資料的集合：
* 成功引入操作：這些日誌包含有關成功完成引入操作的資訊。
* 失敗的引入操作：這些日誌包含有關失敗的引入操作的詳細資訊，包括錯誤詳細資訊。 

然後，資料將存檔到存儲帳戶中，資料流到事件中心，或者根據您的規範發送到日誌分析。

### <a name="enable-diagnostic-logs"></a>啟用診斷記錄

診斷記錄預設為停用。 要啟用診斷日誌，執行以下步驟：

1. 在[Azure 門戶](https://portal.azure.com)中，選擇要監視的 Azure 資料資源管理器群集資源。
1. 在 [監視]**** 下方，選取 [診斷設定]****。
  
    ![添加診斷日誌](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. 選擇 **"添加診斷設置**"。
1. 在 **"診斷設置"** 視窗中：
 
    ![診斷設置配置](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 為診斷設置選擇 **"名稱**"。
    1. 選擇一個或多個目標：存儲帳戶、事件中心或日誌分析。
    1. 選擇要收集的日誌：`SucceededIngestion`或`FailedIngestion`。
    1. 選擇要收集的[指標](using-metrics.md#supported-azure-data-explorer-metrics)（可選）。  
    1. 選擇 **"保存**"以保存新的診斷日誌設置和指標。
    1. 在 Azure 門戶中**創建新的支援請求**，以請求啟動診斷日誌。

幾分鐘後將設置新設置。 日誌然後顯示在配置的存檔目標（存儲帳戶、事件中心或日誌分析）中。 

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述

所有[Azure 監視器診斷日誌共用一個公共頂級架構](/azure/azure-monitor/platform/diagnostic-logs-schema)。 Azure 資料資源管理器具有用於其自身事件的唯一屬性。 所有日誌都以 JSON 格式存儲。

### <a name="ingestion-logs-schema"></a>引入日誌架構

日誌 JSON 字串包括下表中列出的元素：

|名稱               |描述
|---                |---
|time               |報告的時間
|resourceId         |Azure Resource Manager 資源識別碼
|operationName      |操作的名稱：'MICROSOFT。庫斯特托/集群/INGEST/行動'
|operationVersion   |架構版本："1.0" 
|category           |操作的類別。 `SucceededIngestion` 或 `FailedIngestion`。 [成功操作](#successful-ingestion-operation-log)或[操作失敗](#failed-ingestion-operation-log)的屬性不同。
|properties         |操作的詳細資訊。

#### <a name="successful-ingestion-operation-log"></a>成功引入動作記錄

**例子：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功操作診斷日誌的屬性**

|名稱               |描述
|---                |---
|成功上        |攝取完成時間
|operationId        |Azure 資料資源管理器引入操作 ID
|[資料庫]           |目標資料庫的名稱
|資料表              |目標表的名稱
|攝入來源Id  |攝入資料來源的 ID
|引入源路徑|引入資料來源或 blob URI 的路徑
|根活動Id     |活動識別碼

#### <a name="failed-ingestion-operation-log"></a>引入動作記錄失敗

**例子：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**故障操作診斷日誌的屬性**

|名稱               |描述
|---                |---
|失敗           |攝取完成時間
|operationId        |Azure 資料資源管理器引入操作 ID
|[資料庫]           |目標資料庫的名稱
|資料表              |目標表的名稱
|攝入來源Id  |攝入資料來源的 ID
|引入源路徑|引入資料來源或 blob URI 的路徑
|根活動Id     |活動識別碼
|詳細資料            |故障和錯誤訊息的詳細說明
|errorCode          |錯誤碼 
|失敗狀態      |`Permanent` 或 `Transient`。 重試瞬態故障可能會成功。
|源自更新政策|如果失敗源自更新策略，則為 True
|應該重新嘗試        |如果重試可能成功，則為 True

## <a name="next-steps"></a>後續步驟

* [教程：在 Azure 資料資源管理器中引入和查詢監視資料](ingest-data-no-code.md)
* [使用計量來監視叢集健康情況](using-metrics.md)

