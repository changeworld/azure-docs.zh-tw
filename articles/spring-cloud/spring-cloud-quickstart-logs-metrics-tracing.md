---
title: 快速入門 - 使用記錄、計量和追蹤來監視 Azure Spring Cloud 應用程式
description: 使用記錄串流、記錄分析、計量和追蹤來監視 Azure Spring Cloud 上的 Piggymetrics 範例應用程式。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046828"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>快速入門：使用記錄、計量和追蹤來監視 Azure Spring Cloud 應用程式

透過 Azure Spring Cloud 內建的監視功能，您就可以對複雜的問題進行偵錯和監視。 Azure Spring Cloud 可整合 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 與 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 這項整合可讓您從 Azure 入口網站獲得強大的記錄、計量和分散式追蹤功能。 下列程序說明如何搭配已部署的 PiggyMetrics 應用程式使用記錄串流、記錄分析、計量和分散式追蹤。

## <a name="prerequisites"></a>先決條件

完成先前的步驟： 

* [佈建 Azure Spring Cloud 的執行個體](spring-cloud-quickstart-provision-service-instance.md)
* [設定 Config Server](spring-cloud-quickstart-setup-config-server.md)
* [建置和部署應用程式](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>記錄

在 Azure Spring Cloud 上查看記錄的方式有兩種：**記錄串流**是每個應用程式執行個體的即時記錄，或使用**記錄分析**查看具有進階查詢功能的彙總記錄。

### <a name="log-streaming"></a>記錄串流

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

您可以在 Azure CLI 中搭配下列命令來使用記錄串流。

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

您會看到類似下面的記錄：

[ ![Azure CLI 中的記錄串流](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> 使用 `az spring-cloud app logs -h` 探索更多參數和記錄串流功能。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

若要使用 Azure Toolkit for IntelliJ 取得記錄：

1. 選取 [Azure Explorer]，然後選取 [Spring Cloud]。

1. 以滑鼠右鍵按一下正在執行的應用程式。

1. 從下拉式清單中選取 [串流記錄]。

   ![選取串流記錄](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. 選取**執行個體**。

   ![選取執行個體](media/spring-cloud-intellij-howto/select-instance.png)
    
1. 串流記錄會顯示在輸出視窗中。

   ![串流記錄輸出](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. 移至 [服務 |總覽] 頁面，並在 [監視] 區段中選取 [記錄]。 在 Azure Spring Cloud 的其中一個範例查詢上按一下 [執行]。 

   [ ![記錄分析項目](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. 然後您會看到篩選過的記錄。 如需撰寫查詢的詳細指引，請參閱 [Azure Log Analytics 文件](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)。

   [ ![記錄分析查詢](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>計量

1. 移至 [服務 |總覽] 頁面，並在 [監視] 區段中選取 [計量]。 在 [計量] 中選取 [`system.cpu.usage`] 來新增第一個計量，並在 [彙總] 中選取 [`Avg`] 來查看整體 CPU 使用量的時間表。

   [ ![計量項目](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. 按一下上方工具列中的 [新增篩選]，然後選取 [`App=Gateway`]，僅查看**閘道**應用程式的 CPU 使用量。

   [ ![在計量中使用篩選](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. 關閉上面建立的篩選器，按一下 [套用分割]，然後針對 [值] 選取 [`App`]，以查看不同應用程式的 CPU 使用量。

   [ ![在計量中套用分割](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>分散式追蹤

1. 移至 [服務 |總覽] 頁面，並在 [監視] 區段中選取 [分散式追蹤]。 然後按一下右側的 [檢視應用程式對應] 索引標籤。

   [ ![分散式追蹤項目](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 您現在可以看到 Piggymetrics 應用程式之間的呼叫狀態。 

   [ ![分散式追蹤概觀](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. 按一下 **gateway** 和 **account-service** 之間的連結，以查看更多詳細資料，例如 HTTP 方法的最慢呼叫。

   [ ![分散式追蹤](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. 最後，按一下 [調查效能] 來探索更強大的內建效能分析。

   [ ![分散式追蹤效能](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>清除資源

在前述步驟中，您在資源群組中建立了 Azure 資源。 如果您在未來不需要這些資源，請從入口網站刪除資源群組，或在 Cloud Shell 中執行下列命令：

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

在上述步驟中，您也會設定預設的資源群組名稱。 若要清除該預設值，請在 Cloud Shell 中執行下列命令：

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Spring Cloud 的現成監視功能，請參閱：

> [!div class="nextstepaction"]
> [診斷服務](diagnostic-services.md)
> [分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)
> [即時串流記錄](spring-cloud-howto-log-streaming.md)
