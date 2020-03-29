---
title: Azure Spring Cloud 的疑難排解指南 | Microsoft Docs
description: Azure Spring Cloud 的疑難排解指南
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277572"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>解決常見的 Azure 春雲問題

本文提供有關解決 Azure 春雲開發問題疑難排解的說明。 有關詳細資訊，請參閱[Azure 春雲常見問題解答](spring-cloud-faq.md)。

## <a name="availability-performance-and-application-issues"></a>可用性、效能和應用程式問題

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>我的應用程式無法啟動（例如，終結點無法連接，或者經過幾次重試後返回 502）

將記錄匯出至 Azure Log Analytics。 Spring 應用程式日誌的表名為*AppPlatformLogsforSpring*。 要瞭解更多資訊，請參閱[使用診斷設置分析日誌和指標](diagnostic-services.md)。

日誌中可能會出現以下錯誤訊息：

> "org.springframework.上下文.應用程式上下文例外：無法啟動 Web 服務器"

該消息指示兩個可能的問題之一： 
* 其中一個 Bean 遺漏或其一個相依性遺漏。
* 其中一個 Bean 屬性遺漏或無效。 在這種情況下，可能會顯示"java.lang.非法參數例外"。

服務綁定還可能導致應用程式啟動失敗。 要查詢日誌，請使用與綁定服務相關的關鍵字。 例如，假設應用程式具有設置為本地系統時間的 MySQL 實例的綁定。 如果應用程式無法啟動，日誌中可能會出現以下錯誤訊息：

> java.sql.SQLException：伺服器時區值"協調通用時間"無法識別或表示多個時區。

要修復`server parameters`此錯誤，請轉到 MySQL 實例，並將`time_zone`值從*SYSTEM*更改為 *+0：00*。


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>我的應用程式損毀或擲回未預期的錯誤

調試應用程式崩潰時，請從檢查應用程式的運行狀態和發現狀態開始。 為此，請訪問 Azure 門戶中的應用_管理_，以確保所有應用程式的狀態都_處於運行_狀態並_UP_。

* 如果狀態為 _"正在運行_，但發現狀態不是[UP"，請轉到"無法註冊我的應用程式"](#my-application-cant-be-registered)部分。 _UP_

* 如果探索狀態為 [已啟動]__，您可以移至 [計量] 來檢查應用程式的健康情況。 檢查下列計量：


  - `TomcatErrorCount`_（tomcat.global.error）：_ 所有春季應用程式異常都在這裡計算。 如果此數目很大，請移至 Azure Log Analytics 來檢查您的應用程式記錄。

  - `AppMemoryMax`_（jvm.memory.max）：_ 應用程式可用的最大記憶體量。 金額可能未定義，如果定義金額，則可能會隨時間而變化。 如果已定義，則使用和提交的記憶體量始終小於或等於最大值。但是，如果分配嘗試增加*已使用的*記憶體`OutOfMemoryError`（>提交）時，即使*使用<= 最大值*仍為 true，則消息可能會失敗。 在這種情況下，請嘗試使用`-Xmx`參數增加最大堆大小。

  - `AppMemoryUsed`_（jvm.memory.使用_）：應用程式當前使用的記憶體量（以位元組為單位）。 對於普通負載 JAVA 應用程式，此指標系列形成*鋸齒*模式，其中記憶體使用量以較小的增量穩步增加和減少，並突然下降很多，然後模式重複出現。 此指標系列發生的原因是 JAVA 虛擬機器中的垃圾回收，其中收集動作表示鋸齒模式上的丟棄。
    
    此指標對於説明識別記憶體問題非常重要，例如：
    * 一開始的記憶爆炸。
    * 特定邏輯路徑的浪湧記憶體分配。
    * 逐漸洩漏記憶體。

  有關詳細資訊，請參閱[指標](spring-cloud-concept-metrics.md)。

要瞭解有關 Azure 日誌分析的更多資訊，請參閱在[Azure 監視器 中開始使用日誌分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>我的應用程式遇到高 CPU 使用量或高記憶體使用量

如果應用程式 CPU 或記憶體使用率高，則其中一項是正確的：
* 所有應用實例都體驗高 CPU 或記憶體使用率。
* 某些應用實例的 CPU 或記憶體使用率很高。

要確定適用哪種情況，請執行以下操作：

1. 轉到**指標**，然後選擇服務 CPU**使用率或****已使用的服務記憶體**。
2. 添加**App+** 篩選器以指定要監視的應用程式。
3. 按**實例**拆分指標。

如果*所有實例*都遇到高 CPU 或記憶體使用率，則需要擴展應用程式或擴展 CPU 或記憶體使用量。 有關詳細資訊，請參閱[教程：在 Azure 春雲中縮放應用程式](spring-cloud-tutorial-scale-manual.md)。

如果*某些實例*CPU 或記憶體使用率較高，請檢查實例狀態及其發現狀態。

有關詳細資訊，請參閱[Azure 春雲的指標](spring-cloud-concept-metrics.md)。

如果所有實例都已啟動並運行，請轉到 Azure 日誌分析以查詢應用程式日誌並查看代碼邏輯。 這將有助於您瞭解其中是否有任何可能會影響比例分區。 有關詳細資訊，請參閱[使用診斷設置分析日誌和指標](diagnostic-services.md)。

要瞭解有關 Azure 日誌分析的更多資訊，請參閱在[Azure 監視器 中開始使用日誌分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)查詢日誌。

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>將春季應用程式部署到 Azure 春雲的檢查表

在將應用程式加入之前，請確保它符合以下條件：

* 此應用程式可以使用指定的 Java 執行階段版本在本機執行。
* 環境設定 (CPU/RAM/執行個體) 符合應用程式提供者設定的最低需求。
* 配置項具有其預期值。 如需詳細資訊，請參閱[設定伺服器](spring-cloud-tutorial-config-server.md)。
* 環境變數具有其預期值。
* JVM 參數具有其預期值。
* 我們建議您禁用或刪除應用程式包中嵌入的_Config 伺服器_和_Spring 服務註冊表_服務。
* 如果要透過_服務繫結_來繫結任何 Azure 資源，請確定目標資源已啟動且正在執行中。

## <a name="configuration-and-management"></a>設定和管理

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>創建 Azure Spring 雲服務實例時遇到問題

使用 Azure 門戶設置 Azure 春雲服務實例時，Azure 春雲會為您執行驗證。

但是，如果您嘗試使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)或[Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/)設置 Azure 春風雲服務實例，請驗證：

* 訂用帳戶是作用中狀態。
* Azure 春雲[支援](spring-cloud-faq.md)該位置。
* 已經建立執行個體的資源群組。
* 資源名稱符合命名規則。 它必須僅包含小寫字母、數位和連字號。 第一個字元必須是字母。 最後一個字元必須是字母或數字。 該值必須包含從 2 到 32 個字元。

如果要使用資源管理器範本設置 Azure 春雲服務實例，請首先參考[瞭解 Azure 資源管理器範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。

Azure Spring Cloud 服務實例的名稱將用於在 下`azureapps.io`請求子功能變數名稱，因此，如果名稱與現有名稱衝突，則設置將失敗。 您可以在活動日誌中找到更多詳細資訊。

### <a name="i-cant-deploy-a-jar-package"></a>無法部署 JAR 包

無法使用 Azure 門戶或資源管理器範本上載 JAVA 存檔檔 （JAR）/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署應用程式包時，Azure CLI 會定期輪詢部署進度，最後顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

確保您的應用程式以正確的[可執行 JAR 格式](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)打包。 如果未正確打包，您將收到類似于以下內容的錯誤訊息：

> "Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>無法部署源包

不能使用 Azure 門戶或資源管理器範本上載 JAR/源包。

使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)部署應用程式包時，Azure CLI 會定期輪詢部署進度，最後顯示部署結果。

如果輪詢中斷，您仍然可以使用下列命令來提取建置和部署記錄：

`az spring-cloud app show-deploy-log -n <app-name>`

但是，請注意，一個 Azure Spring Cloud 服務實例一次只能觸發一個源包的一個生成作業。 有關詳細資訊，請參閱在 Azure Spring Cloud 中[部署應用程式](spring-cloud-quickstart-launch-app-portal.md)並[設置暫存環境](spring-cloud-howto-staging-environment.md)。

### <a name="my-application-cant-be-registered"></a>無法註冊我的應用程式

在大多數情況下，當專案物件模型 （POM） 檔中未正確配置*所需的依賴項**和服務發現*時，將發生這種情況。 配置後，內置服務註冊表伺服器終結點將作為環境變數隨應用程式一起注入。 然後，應用程式在服務註冊表伺服器註冊自己，併發現其他從屬微服務。

等待至少兩分鐘，然後新註冊的實例開始接收流量。

如果要將現有的基於 Spring Cloud 的解決方案遷移到 Azure，請確保刪除（或禁用）臨時_服務註冊表_和_佈建服務器_實例，以避免與 Azure Spring Cloud 提供的託管實例發生衝突。

您還可以在 Azure 日誌分析中檢查_服務註冊表_用戶端日誌。 有關詳細資訊，請參閱[使用診斷設置分析日誌和指標](diagnostic-services.md)

要瞭解有關 Azure 日誌分析的更多資訊，請參閱在[Azure 監視器 中開始使用日誌分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。 使用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)查詢日誌。

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>我想要檢查應用程式的環境變數

環境變數通知 Azure 春雲框架，確保 Azure 瞭解配置構成應用程式的服務的位置以及如何。 確保環境變數正確是針對潛在問題進行疑難排解的第一步。  您可以使用 Spring Boot Actuator 端點來檢查您的環境變數。  

> [!WARNING]
> 此過程通過使用測試終結點公開環境變數。  如果您的測試端點可公開存取，或您已將網域名稱指派給您的應用程式，則請勿繼續執行該作業。

1. 移至 `https://<your application test endpoint>/actuator/health`。  
    - 類似於 `{"status":"UP"}` 的回應會指出端點已啟用。
    - 如果回應為負，請在*POM.xml*檔中包括以下依賴項：

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. 啟用 Spring Boot 執行器終結點後，請轉到 Azure 門戶並查找應用程式的配置頁。  添加名稱`MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`和值`*`的環境變數。 

1. 重新啟動您的應用程式。

1. 轉到`https://<your application test endpoint>/actuator/env`並檢查回應。  它看起來應該如下所示：

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

查找名為`systemEnvironment`的子節點。  此節點包含您應用程式的環境變數。

> [!IMPORTANT]
> 請記得先撤銷公開環境變數的設定，再讓應用程式可公開存取。  轉到 Azure 門戶，查找應用程式的配置頁，然後刪除此環境變數： `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`。

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>我找不到應用程式的指標或日誌

轉到**應用程式管理**以確保應用程式狀態_正在運行_和_UP。_

如果可以看到_JVM_中的指標，但_Tomcat_沒有指標，請檢查在應用程式包中`spring-boot-actuator`是否啟用了依賴項，以及它是否成功啟動。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果應用程式日誌可以存檔到存儲帳戶，但不能發送到 Azure 日誌分析，請檢查是否[正確設置工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。 如果使用 Azure 日誌分析的免費層，請注意[，免費層不提供服務等級協定 （SLA）。](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
