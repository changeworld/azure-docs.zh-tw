---
title: 如何在 Azure 春季雲端中使用 Application Insights JAVA In-Process 代理程式
description: 如何在 Azure 春季雲端中使用 Application Insights JAVA In-Process 代理程式來監視應用程式和微服務。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881345"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Azure 中的 JAVA In-Process 代理程式，雲端 (預覽) 

本檔說明如何在 Azure 春季雲端中使用 Application Insights JAVA 代理程式來監視應用程式和微服務。 

您可以使用這項功能：

* 搭配不同的篩選搜尋追蹤資料。
* 查看微服務的相依性對應。
* 檢查要求效能。
* 監視即時即時計量。
* 檢查要求失敗。
* 檢查應用程式計量。

Application Insights 提供許多可觀察的觀點，包括：

* 應用程式對應
* 效能
* 失敗
* 計量
* 即時計量    
* 可用性

## <a name="enable-java-in-process-agent-for-application-insights"></a>啟用 Application Insights 的 JAVA In-Process 代理程式

使用下列程式啟用 JAVA In-Process 代理程式預覽功能。

1. 移至服務實例的 [服務總覽] 頁面。
2. 按一下 [監視] 分頁底下 **Application Insights** 專案。
3. 按一下 [ **啟用] Application Insights** 按鈕，以啟用 **Application Insights** 整合。
4. 選取現有的 Application Insights 實例，或建立一個新的實例。
5. 小雞 **啟用 java 同進程代理程式** ，以啟用預覽 java 同進程代理程式功能。 您也可以在此自訂從0到100的取樣率。
6.  按一下 [儲存] 以儲存變更。

## <a name="portal"></a>入口網站

1. 移至 **服務 |[總覽**] 頁面，然後選取 [**監視**] 區段中的 [ **Application Insights** ]。 
2. 按一下 [ **啟用 Application Insights** ]，以在 Azure 春季雲端中啟用 Application Insights。
3. 按一下 [ **啟用 java 同進程代理程式** ]，以啟用 java IPA 預覽功能。 啟用 IPA 預覽功能時，您可以設定一個選擇性的取樣率 (預設值 10.0% ) 。

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>使用 Application Insights 功能

當 **Application Insights** 功能啟用時，您可以：

在左側流覽窗格中，按一下 [ **Application Insights** ] 以跳至 Application Insights 的 [ **總覽** ] 頁面。 

* 按一下 [ **應用程式對應** ] 以查看應用程式之間呼叫的狀態。

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 按一下 [客戶-服務] 之間的連結， `petclinic` 以查看更多詳細資料，例如 SQL 的查詢。

* 在左側流覽窗格中，按一下 [ **效能** ] 以查看所有應用程式作業的效能資料，以及相依性和角色。

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 在左側流覽窗格中，按一下 [ **失敗** ] 以查看您的應用程式是否有非預期的內容。

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 在左側流覽窗格中，按一下 [ **計量** ]，然後選取命名空間，您會看到彈簧開機計量和自訂度量（如果有的話）。

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 在左側流覽窗格中，按一下 [ **即時計量** ] 以查看不同維度的即時計量。

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 在左側流覽窗格中，按一下 [ **可用性** ]，藉由 [在 Application Insights 中建立可用性測試](../azure-monitor/app/monitor-web-app-availability.md)，來監視 Web 應用程式的可用性和回應性。

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM 範本
若要使用 Azure Resource Manager 範本，請將下列內容複寫到 `azuredeploy.json` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
使用 CLI 命令套用 ARM 範本：

* 針對現有的 Azure 春季 Cloud 實例：

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* 針對新建立的 Azure 春季 Cloud 實例：

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* 若要停用應用程式深入解析：

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>另請參閱
* [搭配使用分散式追蹤與 Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [分析記錄和計量](diagnostic-services.md)
* [即時串流記錄](spring-cloud-howto-log-streaming.md)