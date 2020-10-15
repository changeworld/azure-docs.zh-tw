---
title: 教學課程：搭配 Azure Spring Cloud 使用斷路器儀表板
description: 了解如何搭配 Azure Spring Cloud 使用斷路器儀表板。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java
ms.openlocfilehash: fa66f17c6f96ac7f70188c5a28c0b180ed2f03e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906885"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>搭配 Azure Spring Cloud 使用斷路器儀表板

**本文適用於：** ✔️ Java

::: zone pivot="programming-language-java"
Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) 已廣泛用於彙總多個 [Hystrix](https://github.com/Netflix/Hystrix) 計量串流，讓您可以使用 Hystrix 儀表板在單一檢視中監視串流。 本教學課程將示範如何在 Azure Spring Cloud 上使用這些功能。
> [!NOTE]
> Netflix Hystrix 廣泛用於許多現有的 Spring Cloud 應用程式中，但已不再進行開發。 如果您正在開發新專案，請改用 Spring Cloud Circuit Breaker 的實作項目，例如 [resilience4j](https://github.com/resilience4j/resilience4j)。 不同於本教學課程中顯示的 Turbine，新的 Spring Cloud Circuit Breaker 架構會將其計量資料管線的所有實作合併到 Micrometer。 我們仍在努力讓 Azure Spring Cloud 可支援 Micrometer，因此本教學課程不包含此相關內容。

## <a name="prepare-your-sample-applications"></a>準備您的應用程式範例
此範例是從此[存放庫](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)派生而來。

將範例存放庫複製到您的開發環境：
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

建立將在本教學課程中使用的 3 個應用程式：
* user-service：具有單一 /personalized/{id} 端點的簡單 REST 服務
* recommendation-service：簡單的 REST 服務，其具有由 user-service 呼叫的單一端點 /recommendations。
* hystrix-turbine：Hystrix 儀表板服務，可顯示 Hystrix 串流及彙總其他服務中 Hystrix 計量串流的 Turbine 服務。
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>佈建您的 Azure Spring Cloud 執行個體
遵循下列程序，[在 Azure CLI 上佈建服務執行個體](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)。

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>將您的應用程式部署至 Azure Spring Cloud
這些應用程式不會使用**組態伺服器**，因此不需要為 Azure Spring Cloud 設定 **組態伺服器**。  建立並部署，如下所示：
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>驗證您的應用程式
在所有應用程式都已執行並可供探索之後，透過瀏覽器從 https://<username>-user-service.azuremicroservices.io/personalized/1 路徑存取 `user-service`。 如果 user-service 可以存取 `recommendation-service`，您應該會取得下列輸出。 如果網頁無法正常執行，請重新整理多次。
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>存取您的 Hystrix 儀表板和計量串流
確認使用的是公用端點或私人測試端點。

### <a name="using-public-endpoints"></a>使用公用端點
在瀏覽器中，以 `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` 路徑存取 hystrix-turbine。  下圖顯示在此應用程式中執行的 Hystrix 儀表板。

![Hystrix 儀表板](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

將 Turbine 串流 URL `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` 複製到文字方塊，然後按一下 [監視串流]。  這將會顯示儀表板。 如果檢視器中沒有顯示任何內容，請按 `user-service` 端點來產生串流。

![Hystrix 串流](media/spring-cloud-circuit-breaker/hystrix-stream.png) 現在您可以試驗斷路器儀表板。
> [!NOTE] 
> 在生產環境中，Hystrix 儀表板和計量串流不應公開至網際網路。

### <a name="using-private-test-endpoints"></a>使用私人測試端點
您也可以從 `test-endpoint` 存取 Hystrix 計量串流。 作為後端服務，我們並未針對 `recommendation-service` 指派公用端點，但我們可以使用測試端點來顯示其計量，其位址為：`https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Hystrix 測試端點串流](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

作為 Web 應用程式，Hystrix 儀表板應在 `test-endpoint` 上運作。 如果沒有正常運作，則可能有兩個原因：第一個原因是已使用 `test-endpoint` 變更基底 URL，從 `/ to /<APP-NAME>/<DEPLOYMENT-NAME>`，第二個原因是，Web 應用程式使用靜態資源的絕對路徑。 若要讓其在 `test-endpoint` 上運作，您可能需要手動編輯前端檔案中的 <base>。

## <a name="next-steps"></a>後續步驟
* [在 Azure CLI 上佈建服務執行個體](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)
* [準備 Java Spring 應用程式以部署到 Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
::: zone-end
