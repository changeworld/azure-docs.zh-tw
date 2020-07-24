---
title: 分析 Azure 春季雲端中的記錄和計量 |Microsoft Docs
description: 瞭解如何分析 Azure 春季雲端中的診斷資料
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 56f392210aac6045a9dc8cc3522d36092162f26c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086110"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>使用診斷設定來分析記錄和計量

使用 Azure 春季雲端的診斷功能，您可以使用下列任何一項服務來分析記錄和計量：

* 使用 Azure Log Analytics，其中資料會寫入 Azure 儲存體。 將記錄匯出至 Log Analytics 時，會有延遲。
* 將記錄儲存至儲存體帳戶以進行審核或手動檢查。 您可以指定保留時間（以天為單位）。
* 將記錄串流至事件中樞，以供協力廠商服務或自訂分析解決方案進行內嵌。

選擇您想要監視的記錄類別和度量類別。

> [!TIP]
> 只想要串流您的記錄檔嗎？ 請查看此[Azure CLI 命令](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)！

## <a name="logs"></a>記錄

|Log | 描述 |
|----|----|
| **ApplicationConsole** | 所有客戶應用程式的主控台記錄。 |
| **SystemLogs** | 目前，只有此類別中的[春季 Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server)記錄。 |

## <a name="metrics"></a>計量

如需計量的完整清單，請參閱[春季雲端計量](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)。

若要開始使用，請啟用其中一項服務以接收資料。 若要瞭解如何設定 Log Analytics，請參閱[Azure 監視器中的 Log analytics 入門](../azure-monitor/log-query/get-started-portal.md)。

## <a name="configure-diagnostics-settings"></a>設定診斷設定

1. 在 Azure 入口網站中，移至您的 Azure 春季雲端實例。
1. 選取 [**診斷設定**] 選項，然後選取 [**新增診斷設定**]。
1. 輸入設定的名稱，然後選擇您要傳送記錄的位置。 您可以選取下列三個選項的任何組合：
    * **封存至儲存體帳戶**
    * **串流至事件中樞**
    * **傳送至 Log Analytics**

1. 選擇您想要監視的記錄類別和計量類別，然後指定保留時間（以天為單位）。 保留時間僅適用于儲存體帳戶。
1. 選取 [儲存]。

> [!NOTE]
> 1. 當記錄或計量發出時，以及它們出現在您的儲存體帳戶、事件中樞或 Log Analytics 時，可能會有最多15分鐘的間隔。
> 1. 如果刪除或移動 Azure 春季雲端實例，此作業不會串聯到**診斷設定**資源。 您必須先手動刪除**診斷設定**資源，才能對其父系進行作業，例如 Azure 春季雲端實例。 否則，如果新的 Azure 春季雲端實例布建時所使用的資源識別碼與已刪除的相同，或 Azure 春季雲端實例已移回，則先前的**診斷設定**資源會繼續擴充。

## <a name="view-the-logs-and-metrics"></a>查看記錄和計量
有各種方法可查看記錄和計量，如下列標題底下所述。

### <a name="use-the-logs-blade"></a>使用記錄檔分頁

1. 在 Azure 入口網站中，移至您的 Azure 春季雲端實例。
1. 若要開啟 [**記錄搜尋**] 窗格，請選取 [**記錄**]。
1. 在 [**資料表]** 搜尋方塊中
   * 若要查看記錄，請輸入簡單的查詢，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * 若要查看計量，請輸入簡單的查詢，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```
1. 若要查看搜尋結果，請選取 [**執行**]。

### <a name="use-log-analytics"></a>使用 Log Analytics

1. 在 [Azure 入口網站的左窗格中，選取 [ **Log Analytics**]。
1. 選取您在新增診斷設定時所選擇的 Log Analytics 工作區。
1. 若要開啟 [**記錄搜尋**] 窗格，請選取 [**記錄**]。
1. 在 [**資料表]** 搜尋方塊中，
   * 若要查看記錄，請輸入簡單的查詢，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * 若要查看計量，請輸入簡單的查詢，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 若要查看搜尋結果，請選取 [**執行**]。
1. 您可以藉由設定篩選準則來搜尋特定應用程式或實例的記錄：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==`會區分大小寫，但 `=~` 不會。

若要深入瞭解 Log Analytics 中使用的查詢語言，請參閱[Azure 監視器記錄查詢](../azure-monitor/log-query/query-language.md)。

### <a name="use-your-storage-account"></a>使用您的儲存體帳戶

1. 在 [Azure 入口網站中，尋找左側導覽面板或 [搜尋] 方塊中的 [**儲存體帳戶**]。
1. 選取您在新增診斷設定時所選擇的儲存體帳戶。
1. 若要開啟 [ **Blob 容器**] 窗格，請選取 [ **blob**]。
1. 若要查看應用程式記錄檔，請搜尋名為 [ **insights-記錄-applicationconsole**] 的容器。
1. 若要查看應用程式計量，請搜尋名為 [ **insights-計量-pt1m**] 的容器。

若要深入瞭解如何將診斷資訊傳送至儲存體帳戶，請參閱[在 Azure 儲存體中儲存和查看診斷資料](../storage/common/storage-introduction.md)。

### <a name="use-your-event-hub"></a>使用您的事件中樞

1. 在 [Azure 入口網站中，尋找左側導覽面板或 [搜尋] 方塊中的**事件中樞**。

1. 搜尋並選取您在新增診斷設定時所選擇的事件中樞。
1. 若要開啟 [**事件中樞] 清單**窗格，請選取 [**事件中樞**]。
1. 若要查看應用程式記錄檔，請搜尋名為 [**深入解析-記錄-applicationconsole**] 的事件中樞。
1. 若要查看應用程式計量，請搜尋名為 [ **insights-計量-pt1m**] 的事件中樞。

若要深入瞭解如何將診斷資訊傳送至事件中樞，請參閱[使用事件中樞在最忙碌路徑中串流 Azure 診斷資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)。

## <a name="analyze-the-logs"></a>分析記錄

Azure Log Analytics 是以 Kusto 引擎執行，因此您可以查詢記錄以進行分析。 如需使用 Kusto 查詢記錄的快速簡介，請參閱[Log Analytics 教學](../azure-monitor/log-query/get-started-portal.md)課程。

應用程式記錄會提供有關您應用程式健康情況、效能等的重要資訊和詳細記錄。 在接下來的幾節中，有一些簡單的查詢可協助您瞭解應用程式目前和過去的狀態。

### <a name="show-application-logs-from-azure-spring-cloud"></a>顯示 Azure 春季雲端的應用程式記錄

若要從 Azure 春季雲端審查應用程式記錄清單，並依時間排序，並先顯示最新的記錄，請執行下列查詢：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>顯示包含錯誤或例外狀況的記錄專案

若要查看提及錯誤或例外狀況的未排序記錄專案，請執行下列查詢：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查詢來尋找錯誤，或修改查詢字詞以尋找特定的錯誤碼或例外狀況。

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>顯示過去一小時內，您的應用程式所報告的錯誤和例外狀況數目

若要建立顯示您的應用程式在過去一小時內所記錄的錯誤和例外狀況的圓形圖，請執行下列查詢：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>深入瞭解如何查詢應用程式記錄

Azure 監視器提供使用 Log Analytics 來查詢應用程式記錄的廣泛支援。 若要深入瞭解這項服務，請參閱[開始使用 Azure 監視器中的記錄查詢](../azure-monitor/log-query/get-started-queries.md)。 如需建立查詢以分析應用程式記錄檔的詳細資訊，請參閱[Azure 監視器中的記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)。

## <a name="frequently-asked-questions-faq"></a>常見問題集 (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>如何將多行 JAVA 堆疊追蹤轉換成一行？

有一個因應措施，將多行堆疊追蹤轉換成一行。 您可以修改 JAVA 記錄輸出以重新格式化堆疊追蹤訊息，將分行符號取代為標記。 如果您使用 JAVA Logback 程式庫，您可以藉由新增來重新格式化堆疊追蹤訊息，如下所示 `%replace(%ex){'[\r\n]+', '\\n'}%nopex` ：

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
然後，您可以在 Log Analytics 中再次將權杖取代為分行符號，如下所示：

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
您可以將相同的策略用於其他 JAVA 記錄程式庫。