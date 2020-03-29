---
title: 分析 Azure 春雲中的日誌和指標 |微軟文檔
description: 瞭解如何分析 Azure 春雲中的診斷資料
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 544de1b4ac46a58d533f71a46266807a3b93820a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920037"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>使用診斷設置分析日誌和指標

使用 Azure 春雲的診斷功能，可以使用以下任一服務分析日誌和指標：

* 使用 Azure 日誌分析，其中將資料寫入 Azure 存儲。 將日誌匯出到日誌分析時，會有延遲。
* 將日誌保存到存儲帳戶以進行審核或手動檢查。 您可以指定保留時間（以天表示）。
* 將日誌資料流到事件中心，以便由協力廠商服務或自訂分析解決方案引入。

選擇要監視的日誌類別和指標類別。

## <a name="logs"></a>記錄

|Log | 描述 |
|----|----|
| **應用程式主控台** | 所有客戶應用程式的主控台日誌。 | 
| **系統日誌** | 目前，只有[春雲佈建服務器](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server)在此類別中日誌。 |

## <a name="metrics"></a>計量

有關指標的完整清單，請參閱 Spring [Cloud 指標](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)。

要開始，使這些服務之一能夠接收資料。 要瞭解如何配置日誌分析，請參閱在[Azure 監視器 中開始使用日誌分析](../azure-monitor/log-query/get-started-portal.md)。 

## <a name="configure-diagnostics-settings"></a>配置診斷設置

1. 在 Azure 門戶中，轉到 Azure 春雲實例。
1. 選擇 **"診斷設置"** 選項，然後選擇 **"添加診斷"設置**。
1. 輸入設置的名稱，然後選擇要發送日誌的位置。 您可以選擇以下三個選項的任意組合：
    * **存檔到存儲帳戶**
    * **資料流到事件中心**
    * **傳送至 Log Analytics**

1. 選擇要監視的日誌類別和指標類別，然後指定保留時間（以天表示）。 保留時間僅適用于存儲帳戶。
1. 選取 [儲存]****。

> [!NOTE]
> 在發出日誌或指標以及日誌或指標顯示在存儲帳戶、事件中心或日誌分析中之間，間隔可能長達 15 分鐘。

## <a name="view-the-logs-and-metrics"></a>查看日誌和指標
查看日誌和指標的方法有多種，如下標題所述。

### <a name="use-logs-blade"></a>使用日誌邊欄選項卡

1. 在 Azure 門戶中，轉到 Azure 春雲實例。
1. 要打開 **"日誌搜索"** 窗格，請選擇 **"日誌**"。
1. 在 **"日誌**"搜索框中
   * 要查看日誌，請輸入一個簡單的查詢，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * 要查看指標，請輸入一個簡單的查詢，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```
1. 要查看搜尋結果，請選擇"**運行**"。

### <a name="use-log-analytics"></a>使用 Log Analytics

1. 在 Azure 門戶中，在左側窗格中，選擇 **"日誌分析**"。
1. 選擇添加診斷設置時選擇的日誌分析工作區。
1. 要打開 **"日誌搜索"** 窗格，請選擇 **"日誌**"。
1. 在 **"日誌**"搜索框中，
   * 要查看日誌，請輸入一個簡單的查詢，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * 要查看指標，請輸入一個簡單的查詢，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 要查看搜尋結果，請選擇"**運行**"。
1. 您可以通過設置篩選器條件來搜索特定應用程式或實例的日誌：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`區分大小寫，但不`=~`敏感。

要瞭解有關日誌分析中使用的查詢語言的更多資訊，請參閱[Azure 監視器日誌查詢](../azure-monitor/log-query/query-language.md)。

### <a name="use-your-storage-account"></a>使用存儲帳戶 

1. 在 Azure 門戶中，在左側窗格中，選擇 **"存儲帳戶**"。

1. 選擇添加診斷設置時選擇的存儲帳戶。
1. 要打開**Blob 容器**窗格，請選擇**Blob**。
1. 要查看應用程式日誌，請搜索一個稱為**見解日誌-應用程式主控台的**容器。
1. 要查看應用程式指標，請搜索一個稱為**見解度量-pt1m 的**容器。

要瞭解有關向存儲帳戶發送診斷資訊的詳細資訊，請參閱在[Azure 存儲 中存儲和查看診斷資料](../storage/common/storage-introduction.md)。

### <a name="use-your-event-hub"></a>使用活動中心

1. 在 Azure 門戶中，在左側窗格中，選擇**事件中心**。

1. 搜索並選擇添加診斷設置時選擇的事件中心。
1. 要打開**事件中心清單**窗格，請選擇**事件中心**。
1. 要查看應用程式日誌，請搜索一個稱為**見解日誌-應用程式主控台**的事件中心。
1. 要查看應用程式指標，請搜索一個事件中心，稱為**見解度量-pt1m**。

要瞭解有關向事件中心發送診斷資訊的詳細資訊，請參閱[使用事件中心在熱路徑中資料流 Azure 診斷資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)。

## <a name="analyze-the-logs"></a>分析日誌

Azure 日誌分析正在使用 Kusto 引擎運行，因此您可以查詢日誌進行分析。 有關使用 Kusto 查詢日誌的快速介紹，請查看[日誌分析教程](../azure-monitor/log-query/get-started-portal.md)。

應用程式日誌提供有關應用程式運行狀況、性能等的關鍵資訊和詳細日誌。 在下一節中，有一些簡單的查詢可説明您瞭解應用程式的當前和過去狀態。

### <a name="show-application-logs-from-azure-spring-cloud"></a>從 Azure 春雲顯示應用程式日誌

要查看 Azure Spring Cloud 的應用程式日誌清單（按時間排序，首先顯示最新日誌）請運行以下查詢：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>顯示包含錯誤或異常的日誌條目

要查看提及錯誤或異常的未排序的日誌條目，請運行以下查詢：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查詢可以查找錯誤，或修改查詢術語以查找特定的錯誤代碼或異常。 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>顯示應用程式在過去一小時內報告的錯誤和異常數

要創建顯示應用程式在最後一小時記錄的錯誤和異常數的圓形圖，請運行以下查詢：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>瞭解有關查詢應用程式日誌的更多資訊

Azure 監視器通過使用日誌分析為查詢應用程式日誌提供了廣泛的支援。 要瞭解有關此服務詳細資訊，請參閱在[Azure 監視器 中開始使用日誌查詢](../azure-monitor/log-query/get-started-queries.md)。 有關構建查詢以分析應用程式日誌的詳細資訊，請參閱[Azure 監視器 中的日誌查詢概述](../azure-monitor/log-query/log-query-overview.md)。
