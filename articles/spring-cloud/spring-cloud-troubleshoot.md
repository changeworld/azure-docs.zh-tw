---
title: Azure Spring Cloud 的疑難排解指南 | Microsoft Docs
description: Azure Spring Cloud 的疑難排解指南
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c5346858aa119f11ef34916b24c70c966286ab86
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089038"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>針對常見的 Azure 春季雲端問題進行疑難排解

本文提供針對 Azure 春季雲端開發問題進行疑難排解的指示。 如需詳細資訊，請參閱 [Azure 春季雲端常見問題](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、效能和應用程式問題

::: zone pivot="programming-language-java"
### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的應用程式無法啟動 (例如，端點無法連線，或在幾次重試之後傳回 502) 

將記錄匯出至 Azure Log Analytics。 春季應用程式記錄的資料表名為 *AppPlatformLogsforSpring*。 若要深入瞭解，請參閱 [使用診斷設定來分析記錄和計量](diagnostic-services.md)。

下列錯誤訊息可能會出現在您的記錄中：

> "springframework. ApplicationCoNtextException：無法啟動網頁伺服器"

此訊息表示兩個可能的問題之一： 
* 其中一個 Bean 遺漏或其一個相依性遺漏。
* 其中一個 Bean 屬性遺漏或無效。 在此情況下，可能會顯示 "IllegalArgumentException"。

服務系結可能也會導致應用程式啟動失敗。 若要查詢記錄，請使用與系結服務相關的關鍵字。 比方說，假設您的應用程式系結至設定為本機系統時間的 MySQL 實例。 如果應用程式無法啟動，記錄檔中可能會出現下列錯誤訊息：

> SQLException：伺服器時區值 ' 國際標準時間 ' 無法辨識或代表一個以上的時區」。

若要修正這個錯誤，請移至 `server parameters` MySQL 實例的，然後將 `time_zone` 值從 [ *系統* ] 變更為 [ *+ 0:00*]。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的應用程式損毀或擲回未預期的錯誤

當您正在偵測應用程式當機時，請先檢查應用程式的執行狀態和探索狀態。 若要這樣做，請移至 Azure 入口網站中的 [_應用程式管理_]，以確保所有應用程式的_狀態都在_執行_中。_

* 如果狀態為 [執行中] _，但探索_ 狀態不是 [已 _啟動_]，請移至「 [我的應用程式無法註冊](#my-application-cant-be-registered) 」一節。

* 如果探索狀態為 [已啟動]__，您可以移至 [計量] 來檢查應用程式的健康情況。 檢查下列計量：


  - `TomcatErrorCount` (_tomcat。錯誤_) ：所有春季應用程式例外狀況都在此計算。 如果此數目很大，請移至 Azure Log Analytics 來檢查您的應用程式記錄。

  - `AppMemoryMax` (_jvm. memory. max_) ：應用程式可用的最大記憶體數量。 數量可能是未定義的，如果已定義，則可能會隨時間變更。 如果已定義，則已使用和已認可的記憶體數量一律會小於或等於 max。但是， `OutOfMemoryError` 如果配置嘗試增加已使用的記憶體（例如使用的 *> 認可*），則記憶體配置可能會失敗，並會顯示一則訊息（即使 *使用的 <= max* 仍為 true）。 在這種情況下，請嘗試使用參數來增加堆積大小上限 `-Xmx` 。

  - `AppMemoryUsed` (_jvm。使用_) ：應用程式目前使用的記憶體數量（以位元組為單位）。 若為一般 load JAVA 應用程式，此計量系列形成 *鋸齒* 模式，其中的記憶體使用量會穩定地增加並減少，且突然下降，然後再重複一次模式。 此計量系列發生的原因是 JAVA 虛擬機器內的垃圾收集，其中集合動作代表鋸齒模式的下降。
    
    此計量很重要，可協助識別記憶體問題，例如：
    * 一開始就會有記憶體激增。
    * 特定邏輯路徑的激增記憶體配置。
    * 逐漸洩漏記憶體。
  如需詳細資訊，請參閱 [計量](spring-cloud-concept-metrics.md)。
  
* 如果應用程式無法啟動，請確認應用程式具有有效的 jvm 參數。 如果 jvm 記憶體設定過高，記錄中可能會出現下列錯誤訊息：

  >「所需的記憶體2728741K 大於2000M 可供配置使用」



若要深入瞭解 Azure Log Analytics，請參閱 [Azure 監視器中的開始使用 Log analytics](../azure-monitor/log-query/get-started-portal.md)。
::: zone-end

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的應用程式遇到高 CPU 使用量或高記憶體使用量

如果您的應用程式遇到高 CPU 或記憶體使用量，則下列兩個專案的其中一項為真：
* 所有應用程式實例都遇到高 CPU 或記憶體使用量。
* 有些應用程式實例遇到高 CPU 或記憶體使用量。

若要確定適用何種情況，請執行下列動作：

1. 移至 [ **計量**]，然後選取 [ **服務 CPU 使用量百分比** ] 或 [ **使用的服務記憶體**]。
2. 新增 **應用程式 =** 篩選器，以指定您想要監視的應用程式。
3. 依 **實例**分割計量。

如果 *所有實例* 都遇到高 cpu 或記憶體使用量，您需要相應放大應用程式，或擴大 cpu 或記憶體使用量。 如需詳細資訊，請參閱 [教學課程：在 Azure 春季雲端中調整應用程式](spring-cloud-tutorial-scale-manual.md)。

如果 *某些實例* 遇到高 CPU 或記憶體使用量，請檢查實例狀態及其探索狀態。

如需詳細資訊，請參閱 [Azure 春季雲端的計量](spring-cloud-concept-metrics.md)。

如果所有實例都已啟動且正在執行，請移至 Azure Log Analytics 來查詢您的應用程式記錄，並檢查您的程式碼邏輯。 這可協助您查看是否有任何可能會影響規模分割。 如需詳細資訊，請參閱 [使用診斷設定來分析記錄和計量](diagnostic-services.md)。

若要深入瞭解 Azure Log Analytics，請參閱 [Azure 監視器中的開始使用 Log analytics](../azure-monitor/log-query/get-started-portal.md)。 使用 [Kusto 查詢語言](/azure/kusto/query/)來查詢記錄。

::: zone pivot="programming-language-java"
### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>將您的春季應用程式部署到 Azure 春季雲端的檢查清單

在您將應用程式上架之前，請確定它符合下列準則：

* 此應用程式可以使用指定的 Java 執行階段版本在本機執行。
* 環境設定 (CPU/RAM/執行個體) 符合應用程式提供者設定的最低需求。
* 設定專案具有預期的值。 如需詳細資訊，請參閱[設定伺服器](spring-cloud-tutorial-config-server.md)。
* 環境變數具有預期的值。
* JVM 參數具有預期的值。
* 建議您停用或移除應用程式封裝中的內嵌設定 _伺服器_ 和 _春季 Service Registry_ 服務。
* 如果要透過_服務繫結_來繫結任何 Azure 資源，請確定目標資源已啟動且正在執行中。
::: zone-end

## <a name="configuration-and-management"></a>設定和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在建立 Azure 春季雲端服務實例時遇到問題

當您使用 Azure 入口網站設定 Azure 春季雲端服務實例時，Azure 春季 Cloud 會為您執行驗證。

但是，如果您嘗試使用 [Azure CLI](/cli/azure/get-started-with-azure-cli) 或 [Azure Resource Manager 範本](../azure-resource-manager/index.yml)來設定 Azure 春季 Cloud 服務實例，請確認：

* 訂用帳戶是作用中狀態。
* Azure 春季雲端 [支援](spring-cloud-faq.md) 此位置。
* 已經建立執行個體的資源群組。
* 資源名稱符合命名規則。 它必須只包含小寫字母、數位和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數位。 值必須包含2到32個字元。

如果您想要使用 Resource Manager 範本來設定 Azure 春季 Cloud 服務實例，請先參閱 [瞭解 Azure Resource Manager 範本的結構和語法](../azure-resource-manager/templates/template-syntax.md)。

Azure 春季雲端服務實例的名稱將會用來在下要求子功能變數名稱稱 `azureapps.io` ，因此如果此名稱與現有的名稱衝突，則安裝程式將會失敗。 您可能會在活動記錄中找到更多詳細資料。

::: zone pivot="programming-language-java"
### <a name="i-cant-deploy-a-net-core-app"></a>我無法部署 .NET Core 應用程式

您無法使用 Azure 入口網站或 Resource Manager 範本，為 .NET Core Steeltoe 應用程式上傳 *.zip 檔案。*

當您使用 [Azure CLI](/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，Azure CLI 會定期輪詢部署進度，最後會顯示部署結果。

確定您的應用程式是以正確的 *.zip* 檔案格式封裝。 如果未正確封裝，進程將會停止回應，否則您會收到錯誤訊息。
::: zone-end

::: zone pivot="programming-language-java"
### <a name="i-cant-deploy-a-jar-package"></a>我無法部署 JAR 套件

您無法使用 Azure 入口網站或 Resource Manager 範本，將 JAVA 封存檔案 (JAR) /source 封裝上傳。

當您使用 [Azure CLI](/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，Azure CLI 會定期輪詢部署進度，最後會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

確定您的應用程式是以正確的 [可執行檔 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)封裝的。 如果未正確封裝，您會收到類似下列的錯誤訊息：

> 「錯誤：無效或已損毀的 jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714」

### <a name="i-cant-deploy-a-source-package"></a>我無法部署來源套件

您無法使用 Azure 入口網站或 Resource Manager 範本上傳 JAR/來源套件。

當您使用 [Azure CLI](/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，Azure CLI 會定期輪詢部署進度，最後會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取建置和部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

不過請注意，一個 Azure 春季雲端服務實例一次只能針對一個來源封裝觸發一個組建作業。 如需詳細資訊，請參閱[在 Azure 春季雲端中](spring-cloud-howto-staging-environment.md)[部署應用程式](spring-cloud-quickstart.md)和設定預備環境。

### <a name="my-application-cant-be-registered"></a>無法註冊我的應用程式

在大多數情況下，當您的專案物件模型中未正確設定 *必要* 的相依性和 *服務探索* (POM) 檔案時，就會發生這種情況。 設定之後，內建的服務登錄伺服器端點會以您的應用程式插入為環境變數。 然後，應用程式會向 Service Registry 伺服器註冊自己，並探索其他相依的微服務。

在新註冊的實例開始接收流量之前，請至少等候兩分鐘。

如果您要將現有的春季雲端式解決方案遷移至 Azure，請確定您的臨機操作 _服務_ 登錄和 _Config Server_ 實例已 (移除) ，以避免與 Azure 春季雲端所提供的受控實例發生衝突。

您也可以在 Azure Log Analytics 中檢查 _服務_ 登錄用戶端記錄。 如需詳細資訊，請參閱 [使用診斷設定來分析記錄和計量](diagnostic-services.md)

若要深入瞭解 Azure Log Analytics，請參閱 [Azure 監視器中的開始使用 Log analytics](../azure-monitor/log-query/get-started-portal.md)。 使用 [Kusto 查詢語言](/azure/kusto/query/)來查詢記錄。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要檢查應用程式的環境變數

環境變數會通知 Azure 春季雲端架構，以確保 Azure 瞭解在哪裡及如何設定構成您應用程式的服務。 確保環境變數正確是針對潛在問題進行疑難排解的第一步。  您可以使用 Spring Boot Actuator 端點來檢查您的環境變數。  

> [!WARNING]
> 此程式會使用您的測試端點來公開您的環境變數。  如果您的測試端點可公開存取，或您已將網域名稱指派給您的應用程式，則請勿繼續執行該作業。

1. 前往 `https://<your application test endpoint>/actuator/health`。  
    - 類似於 `{"status":"UP"}` 的回應會指出端點已啟用。
    - 如果回應是負面的，請在您的 *POM.xml* 檔案中包含下列相依性：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 啟用彈簧開機傳動程式端點之後，請移至 Azure 入口網站並尋找您應用程式的 [設定] 頁面。  新增具有名稱和值的環境變數 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` `*` 。 

1. 重新啟動您的應用程式。

1. 移至 `https://<your application test endpoint>/actuator/env` 並檢查回應。  其看起來應該如下：

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

尋找名為的子節點 `systemEnvironment` 。  此節點包含您應用程式的環境變數。

> [!IMPORTANT]
> 請記得先撤銷公開環境變數的設定，再讓應用程式可公開存取。  移至 Azure 入口網站，尋找應用程式的 [設定] 頁面，然後刪除此環境變數：  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>我找不到應用程式的計量或記錄

移至 [ **應用程式管理** ]，以確保應用程式狀態為 [ _正在_ 執行] 和 [ _啟動_]。

檢查以查看應用程式封裝中是否已啟用氣象 _JMX_ 。 您可以使用 configuration 屬性來啟用此功能 `spring.jmx.enabled=true` 。  

檢查是否已 `spring-boot-actuator` 在您的應用程式套件中啟用相依性，以及是否已順利啟動。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果您的應用程式記錄可以封存到儲存體帳戶，但不會傳送至 Azure Log Analytics，請查看您是否 [正確設定工作區](../azure-monitor/learn/quick-create-workspace.md)。 如果您是使用 Azure Log Analytics 的免費層，請注意， [免費層不提供服務等級協定 (SLA) ](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。
::: zone-end

## <a name="next-steps"></a>後續步驟

* [如何在 Azure 春季雲端中自行診斷和解決問題](spring-cloud-howto-self-diagnose-solve.md)