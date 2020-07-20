---
title: Azure Spring Cloud 的疑難排解指南 | Microsoft Docs
description: Azure Spring Cloud 的疑難排解指南
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: db5363c5d8adaf29e2c460d9ce36afa2d29ae8e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791651"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>針對常見的 Azure 春季雲端問題進行疑難排解

本文提供針對 Azure 春季雲端開發問題進行疑難排解的指示。 如需詳細資訊，請參閱[Azure 春季雲端常見問題](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、效能和應用程式問題

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的應用程式無法啟動（例如，端點無法連線，或在幾次重試後傳回502）

將記錄匯出至 Azure Log Analytics。 春季應用程式記錄的資料表名為*AppPlatformLogsforSpring*。 若要深入瞭解，請參閱[使用診斷設定來分析記錄和計量](diagnostic-services.md)。

您的記錄中可能會出現下列錯誤訊息：

> springframework。 ApplicationCoNtextException：無法啟動 web 伺服器」

此訊息表示兩個可能的問題之一： 
* 其中一個 Bean 遺漏或其一個相依性遺漏。
* 其中一個 Bean 屬性遺漏或無效。 在此情況下，可能會顯示「Illegalargumentexception 若」。

服務系結可能也會導致應用程式啟動失敗。 若要查詢記錄，請使用與系結服務相關的關鍵字。 比方說，假設您的應用程式系結至 MySQL 實例，而該實例是設定為 [本機系統時間]。 如果應用程式無法啟動，記錄檔中可能會出現下列錯誤訊息：

> "SQLException：伺服器時區值「國際標準時間」無法辨識或代表一個以上的時區。」

若要修正此錯誤，請移至 `server parameters` MySQL 實例的，並將 `time_zone` 值從*系統*變更為 *+ 0:00*。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的應用程式損毀或擲回未預期的錯誤

當您正在進行應用程式損毀時，請先檢查應用程式的執行狀態和探索狀態。 若要這麼做，請移至 Azure 入口網站中的 [_應用程式管理_]，以確保所有應用程式的狀態都是 [執行中 _] 和 [_ _啟動_]。

* 如果狀態為 [執行中] _，但探索狀態為 [_ 未_啟動_]，請移至 [[無法註冊我的應用程式]](#my-application-cant-be-registered)區段。

* 如果探索狀態為 [已啟動]__，您可以移至 [計量] 來檢查應用程式的健康情況。 檢查下列計量：


  - `TomcatErrorCount`（_tomcat. 全域錯誤_）：所有春季應用程式例外狀況都會在此計算。 如果此數目很大，請移至 Azure Log Analytics 來檢查您的應用程式記錄。

  - `AppMemoryMax`（_jvm. memory. max_）：可供應用程式使用的記憶體數量上限。 可能是未定義的金額，如果已定義，則可能會隨時間變更。 如果已定義，則已使用和已認可的記憶體數量一律小於或等於最大值。不過， `OutOfMemoryError` 如果配置嘗試增加已使用的記憶體（例如*使用 > 認可*），則記憶體配置可能會失敗並出現訊息，即使*使用 <= max*仍然是 true 也一樣。 在這種情況下，請嘗試使用參數來增加堆積大小上限 `-Xmx` 。

  - `AppMemoryUsed`（_jvm. 使用的記憶體_）：應用程式目前使用的記憶體數量（以位元組為單位）。 針對一般 load JAVA 應用程式，此計量數列會形成*sawtooth*模式，其中記憶體使用量穩定地增加並減少，而且突然下降，然後模式會重複出現。 此度量數列是因為 JAVA 虛擬機器內的垃圾收集所造成，其中集合動作代表 sawtooth 模式的卸載。
    
    此計量非常重要，可協助找出記憶體問題，例如：
    * 一開始的記憶體激增。
    * 特定邏輯路徑的浪湧記憶體配置。
    * 漸進式記憶體流失。

  如需詳細資訊，請參閱[計量](spring-cloud-concept-metrics.md)。

若要深入瞭解 Azure Log Analytics，請參閱[Azure 監視器中的 Log analytics 入門](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的應用程式遇到高 CPU 使用量或高記憶體使用量

如果您的應用程式遇到高 CPU 或記憶體使用量，則其中一項為真：
* 所有應用程式實例都會遇到高 CPU 或記憶體使用量。
* 某些應用程式實例會遇到高 CPU 或記憶體使用量。

若要確定適用的情況，請執行下列動作：

1. 移至 [**計量**]，然後選取 [**服務 CPU 使用量百分比**] 或 [**使用的服務記憶體**]。
2. 新增 [**應用程式 =** 篩選] 以指定您要監視的應用程式。
3. 依**實例**分割計量。

如果*所有實例*都遇到高 cpu 或記憶體使用量，您必須相應放大應用程式，或相應增加 cpu 或記憶體使用量。 如需詳細資訊，請參閱[教學課程：在 Azure 春季雲端調整應用程式](spring-cloud-tutorial-scale-manual.md)。

如果*某些實例*遇到高 CPU 或記憶體使用量，請檢查實例狀態及其探索狀態。

如需詳細資訊，請參閱[Azure 春季雲端的計量](spring-cloud-concept-metrics.md)。

如果所有實例都已啟動並執行，請移至 Azure Log Analytics 來查詢您的應用程式記錄檔，並檢查您的程式碼邏輯。 這將可協助您查看是否有任何可能影響擴展資料分割。 如需詳細資訊，請參閱[使用診斷設定來分析記錄和計量](diagnostic-services.md)。

若要深入瞭解 Azure Log Analytics，請參閱[Azure 監視器中的 Log analytics 入門](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>將您的春季應用程式部署至 Azure 春季雲端的檢查清單

在您將應用程式上架之前，請確定它符合下列準則：

* 此應用程式可以使用指定的 Java 執行階段版本在本機執行。
* 環境設定 (CPU/RAM/執行個體) 符合應用程式提供者設定的最低需求。
* 設定專案有其預期的值。 如需詳細資訊，請參閱[設定伺服器](spring-cloud-tutorial-config-server.md)。
* 環境變數具有其預期的值。
* JVM 參數具有預期的值。
* 我們建議您停用或移除應用程式封裝中的內嵌設定_伺服器_和_春季服務_登錄服務。
* 如果要透過_服務繫結_來繫結任何 Azure 資源，請確定目標資源已啟動且正在執行中。

## <a name="configuration-and-management"></a>設定和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>我在建立 Azure 春季雲端服務實例時遇到問題

當您使用 Azure 入口網站來設定 Azure 春季雲端服務實例時，Azure 春季雲端會為您執行驗證。

但是，如果您嘗試使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/)來設定 Azure 春季雲端服務實例，請確認：

* 訂用帳戶是作用中狀態。
* Azure 春季雲端[支援](spring-cloud-faq.md)此位置。
* 已經建立執行個體的資源群組。
* 資源名稱符合命名規則。 它必須只包含小寫字母、數位和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數字。 此值必須包含2到32個字元。

如果您想要使用 Resource Manager 範本來設定 Azure 春季雲端服務實例，請先參閱[瞭解 Azure Resource Manager 範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。

Azure 春季雲端服務實例的名稱將用於要求底下的子功能變數名稱稱 `azureapps.io` ，因此如果名稱與現有的功能變數名稱衝突，安裝將會失敗。 您可以在活動記錄中找到更多詳細資料。

### <a name="i-cant-deploy-a-jar-package"></a>我無法部署 JAR 套件

您無法使用 Azure 入口網站或 Resource Manager 範本上傳 JAVA 封存檔案（JAR）/source 封裝。

當您使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，Azure CLI 會定期輪詢部署進度，而在最後，它會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

請確定您的應用程式是以正確的[可執行檔 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)封裝。 如果未正確封裝，您會收到類似下列的錯誤訊息：

> 「錯誤： jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714 無效或已損毀」

### <a name="i-cant-deploy-a-source-package"></a>我無法部署來源套件

您無法使用 Azure 入口網站或 Resource Manager 範本上傳 JAR/來源封裝。

當您使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署應用程式封裝時，Azure CLI 會定期輪詢部署進度，而在最後，它會顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取建置和部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

不過，請注意，一個 Azure 春季雲端服務實例一次只能觸發一個來源套件的一個組建工作。 如需詳細資訊，請參閱[在 Azure 春季雲端中](spring-cloud-howto-staging-environment.md)[部署應用程式](spring-cloud-quickstart-launch-app-portal.md)和設定預備環境。

### <a name="my-application-cant-be-registered"></a>無法註冊我的應用程式

在大部分情況下，當您的專案物件模型（POM）檔案中未正確設定*必要*的相依性和*服務探索*時，就會發生這種情況。 一旦設定之後，內建的服務登錄伺服器端點就會插入做為您應用程式的環境變數。 然後，應用程式會向服務登錄伺服器註冊自己，並探索其他相依的微服務。

在新註冊的實例開始接收流量之前，請至少等候兩分鐘。

如果您要將現有的春季雲端式解決方案遷移至 Azure，請確定您的臨機操作_服務_登錄和設定_伺服器_實例已移除（或已停用），以避免與 Azure 春季雲端所提供的受控實例發生衝突。

您也可以在 Azure Log Analytics 中檢查_服務_登錄用戶端記錄。 如需詳細資訊，請參閱[使用診斷設定來分析記錄和計量](diagnostic-services.md)

若要深入瞭解 Azure Log Analytics，請參閱[Azure 監視器中的 Log analytics 入門](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)來查詢記錄。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要檢查應用程式的環境變數

環境變數會通知 Azure 春季雲端架構，確保 Azure 瞭解在何處和如何設定構成您應用程式的服務。 確保環境變數正確是針對潛在問題進行疑難排解的第一步。  您可以使用 Spring Boot Actuator 端點來檢查您的環境變數。  

> [!WARNING]
> 此程式會使用您的測試端點來公開您的環境變數。  如果您的測試端點可公開存取，或您已將網域名稱指派給您的應用程式，則請勿繼續執行該作業。

1. 移至 `https://<your application test endpoint>/actuator/health`。  
    - 類似於 `{"status":"UP"}` 的回應會指出端點已啟用。
    - 如果回應是負數，請在您的*POM.xml*檔案中包含下列相依性：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 啟用春季開機傳動端點後，請移至 [Azure 入口網站]，然後尋找應用程式的 [設定] 頁面。  新增具有名稱和值的環境變數 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` `*` 。 

1. 重新啟動您的應用程式。

1. 移至 `https://<your application test endpoint>/actuator/env` 並檢查回應。  它看起來應該如下所示：

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
> 請記得先撤銷公開環境變數的設定，再讓應用程式可公開存取。  移至 [Azure 入口網站]，尋找應用程式的 [設定] 頁面，並刪除此環境變數： `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` 。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>我找不到應用程式的計量或記錄

移至 [**應用程式管理**] 以確定應用程式_狀態為 [_ 執行中] 和 [已_啟動_]。

檢查以查看您的應用程式套件中是否已啟用天氣_JMX_ 。 這項功能可以使用 configuration 屬性來啟用 `spring.jmx.enabled=true` 。  

檢查是否已 `spring-boot-actuator` 在應用程式封裝中啟用相依性，以及是否已成功啟動。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果您的應用程式記錄可以封存到儲存體帳戶，但未傳送至 Azure Log Analytics，請檢查您是否[正確設定您的工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果您使用的是 Azure Log Analytics 的免費層，請注意[免費層不提供服務等級協定（SLA）](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)。
