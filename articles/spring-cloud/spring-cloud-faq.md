---
title: 關於 Azure 春季雲端的常見問題 |Microsoft Docs
description: 本文將回答有關 Azure 春季 Cloud 的常見問題。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a8180088e5a0db613d5f04106fe102f58594bf72
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655046"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春季雲端常見問題

本文將回答有關 Azure 春季 Cloud 的常見問題。

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure Spring Cloud？

Azure 春季 Cloud 提供平臺即服務， (適用于春季雲端開發人員的 PaaS) 。 Azure 春季 Cloud 會管理您的應用程式基礎結構，讓您可以專注于應用程式的程式碼和商務邏輯。 Azure 春天雲端內建的核心功能包括 Eureka、Config Server、Service Registry Server、Pivotal 組建服務、藍綠部署等等。 此服務也可讓開發人員將其應用程式系結至其他 Azure 服務，例如 Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis。

Azure 春季 Cloud 藉由整合 Azure 監視器、Application Insights 和 Log Analytics，增強開發人員和操作員的 application diagnostics 體驗。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隱私權是 Azure 和 Azure 春季雲端客戶的最高優先順序。 Azure 藉由安全地加密所有資料，協助確保只有客戶可以存取應用程式資料、記錄或設定。 

* Azure 春季雲端中的服務實例會彼此隔離。
* Azure 春季雲端提供完整的 TLS/SSL 和憑證管理。
* OpenJDK 和 Spring Cloud 執行階段的重大安全性修補，都會盡快套用至 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>哪些區域可以使用 Azure 春季 Cloud？

美國東部、美國東部2、美國中部、美國中南部、美國西部2、西歐、歐洲北部、英國南部、東南亞及澳大利亞東部。

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>是否有任何客戶資料儲存在指定的區域之外？

Azure 春季 Cloud 是一項區域服務。 Azure 春季雲端中的所有客戶資料都會儲存到指定區域的相同地理位置內的多個區域，以供進行複製。 若要深入瞭解地理區域和區域，請參閱 [Azure 中的資料](https://azure.microsoft.com/global-infrastructure/data-residency/)存放區。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季雲端的已知限制為何？

Azure 春季雲端具有下列已知限制：
    
* `spring.application.name` 將會由用來建立每個應用程式的應用程式名稱來覆寫。
* `server.port` 預設為埠1025。 如果套用任何其他值，則會覆寫它。 也請遵循這項設定，而不要在您的程式碼中指定伺服器埠。
* Azure 入口網站和 Azure Resource Manager 範本不支援上傳應用程式封裝。 您只能透過 Azure CLI 部署應用程式來上傳應用程式封裝。

### <a name="what-pricing-tiers-are-available"></a>有哪些定價層可供使用？ 
我應該使用哪一個，以及每一層中的限制為何？
* Azure 春季雲端提供兩種定價層：基本和標準。 基本層的目標是開發/測試及試用 Azure 春季雲端。 標準層已優化，可執行一般用途的生產流量。 如需限制和功能等級的比較，請參閱 [Azure 春季雲端定價詳細資料](https://azure.microsoft.com/pricing/details/spring-cloud/) 。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和回報問題？

如果您在 Azure 春季雲端遇到任何問題，請建立 [Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 若要提交功能要求或提供意見反應，請前往 [Azure 意見](https://feedback.azure.com/forums/34192--general-feedback)反應。

## <a name="development"></a>部署

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是春季雲端開發人員，但不熟悉 Azure。 最快的方式是瞭解如何開發 Azure 春季雲端應用程式？

若要開始使用 Azure 春季 Cloud 的最快速方式，請遵循 [快速入門：使用 Azure 入口網站啟動 Azure 春季 cloud 應用程式](spring-cloud-quickstart.md)中的指示。

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支援的 Java 執行階段為何？

Azure Spring Cloud 支援 Java 8 和 11。 請參閱 [JAVA 執行時間和作業系統版本](#java-runtime-and-os-versions)
::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>我可以在哪裡查看我的春季雲端應用程式記錄和計量？

在 [應用程式概觀] 索引標籤和 [Azure 監視器](../azure-monitor/platform/data-platform-metrics.md#metrics-explorer) \(部分機器翻譯\) 索引標籤中尋找計量。

Azure 春季雲端支援將春季雲端應用程式記錄和計量匯出至 Azure 儲存體、EventHub 及 [Log Analytics](../azure-monitor/platform/data-platform-logs.md)。 Log Analytics 中的資料表名稱是 *AppPlatformLogsforSpring*。 若要瞭解如何啟用，請參閱 [診斷服務](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支援分散式追蹤？

可以。 如需詳細資訊，請參閱 [教學課程：搭配使用分散式追蹤與 Azure 春季雲端](spring-cloud-tutorial-distributed-tracing.md)。

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>服務繫結支援的資源類型為何？

目前支援三個服務：
* Azure Cosmos DB
* 適用於 MySQL 的 Azure 資料庫
* Azure Redis 快取。
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>我可以從我的應用程式內查看、新增或移動永久性磁片區嗎？

可以。

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Azure 春季雲端實例有多少輸出公用 IP 位址？

輸出公用 IP 位址的數目可能會根據層級和其他因素而有所不同。 

| Azure 春季雲端實例類型 | 預設的輸出公用 IP 位址數目 |
| -------------------------------- | ---------------------------------------------- |
| 基本層實例             | 1                                              |
| 標準層實例          | 2                                              |
| VNet 插入實例         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>我可以增加輸出公用 IP 位址的數目嗎？

是，您可以開啟 [支援票證](https://azure.microsoft.com/support/faq/)  來要求更多輸出公用 IP 位址。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>當我刪除/移動 Azure 春天雲端服務實例時，是否也會刪除/移動其擴充功能資源？

這取決於擁有擴充資源之資源提供者的邏輯。 實例的擴充資源 `Microsoft.AppPlatform` 不屬於相同的命名空間，因此行為會因資源提供者而異。 例如，刪除/移動作業將不會串聯至 **診斷設定** 資源。 如果新的 Azure 春季雲端實例布建的資源識別碼與所刪除的相同，或者先前的 Azure 春季雲端實例已移回，則先前的 **診斷設定** 資源會繼續進行擴充。

您可以使用 Azure CLI 來刪除春季雲端的診斷設定：

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>JAVA 執行時間和作業系統版本

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure 春季雲端支援哪些版本的 JAVA 執行時間？

Azure 春季 Cloud 支援最新組建的 JAVA LTS 版本，目前支援的版本為2020、JAVA 8 和 JAVA 11。 請參閱 [安裝適用于 azure 和 Azure Stack 的 JDK](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>哪些人建立了這些 JAVA 執行時間？

Azul 系統。 適用於 Azure 的 Azul Zulu - 企業版 JDK 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。

### <a name="how-often-will-java-runtimes-get-updated"></a>JAVA 執行時間更新的頻率為何？

LTS 和 MTS JDK 版本會有每季的安全性更新和錯誤修正，並視需要提供頻外的重大更新和修補程式。 這項支援包括可回移在較新版本的 Java (如 Java 11) 中報告的 Java 7 和 8 安全性更新和錯誤修正。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>JAVA 8 和 JAVA 11 LTS 版本有多長的支援？

請參閱 [JAVA 的 azure 和 Azure Stack 長期支援](/azure/developer/java/fundamentals/java-jdk-long-term-support)。

* 在2030年12月之前，將會支援 JAVA 8 LTS。
* 在2027年9月之前，將會支援 JAVA 11 LTS。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>如何下載支援的 JAVA 執行時間以進行本機開發？

請參閱 [安裝適用于 azure 和 Azure Stack 的 JDK](/azure/developer/java/fundamentals/java-jdk-install)。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>舊版 JAVA 執行時間的淘汰原則為何？

公開通知將在任何舊的執行階段版本淘汰之前的12個月內寄出。 您將會有12個月的時間來遷移至較新版本。

* 當我們將淘汰 JAVA 版本時，訂用帳戶管理員會收到電子郵件通知。
* 淘汰資訊將于檔中發行。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>如何在 JAVA 執行時間層級取得問題的支援？

您可以使用 Azure 支援服務來開啟支援票證。  瞭解 [如何建立 Azure 支援要求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>執行應用程式的作業系統為何？

使用最新的 Ubuntu LTS 版本，目前的 [ubuntu 20.04 LTS (焦距 Fossa) ](https://releases.ubuntu.com/focal/) 是預設作業系統。

### <a name="how-often-are-os-security-patches-applied"></a>OS 安全性修補程式的套用頻率為何？

適用于 Azure 春季雲端的安全性修補程式會每月推出至生產環境。
重大安全性修補程式 (CVE 分數 >= 9 適用于 Azure 春季雲端的) 會儘快推出。
::: zone-end

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季雲端是否支援藍綠部署？
可以。 如需詳細資訊，請參閱 [設定預備環境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我是否可以存取 Kubernetes 來操作我的應用程式容器？

否。  Azure Spring Cloud 將基礎架構對開發人員抽象化，讓您能夠專注於應用程式程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支援從來源建立容器？

可以。 如需詳細資訊，請參閱 [從原始程式碼啟動您的春季 Cloud 應用程式](spring-cloud-quickstart.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支援應用程式執行個體自動調整？

可以。  如需詳細資訊，請參閱 [設定自動](spring-cloud-tutorial-setup-autoscale.md)調整。

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>將現有的春季 Cloud 微服務遷移到 Azure 春季雲端的最佳作法為何？

當您將現有的春季 Cloud 微服務遷移到 Azure 春季雲端時，最好先觀察以下最佳作法：
* 必須解析所有應用程式相依性。
* 準備您的設定專案、環境變數和 JVM 參數，讓您可以將它們與 Azure 春季雲端中的部署進行比較。
* 如果您想要使用服務系結，請流覽您的 Azure 服務，並確定您已設定適當的存取權限。
* 建議您移除或停用任何可能與由 Azure 春季 Cloud 管理之服務衝突的內嵌服務，例如我們的服務探索服務、Config Server 等等。
* 我們建議您使用正式、穩定的 Pivotal 春季程式庫。 非官方、搶鮮版或分支版的 Pivotal 春季程式庫沒有服務等級協定 (SLA) 支援。

在遷移之後，監視您的 CPU/RAM 計量和網路流量，以確保適當地調整應用程式實例。
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core 版本

### <a name="which-net-core-versions-are-supported"></a>支援哪些 .NET Core 版本？

.NET Core 3.1 和更新版本。

### <a name="how-long-will-net-core-31-be-supported"></a>支援 .NET Core 3.1 多久的時間？

到2022年12月3日為止。 請參閱 [.Net Core 支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。
::: zone-end


## <a name="troubleshooting"></a>疑難排解

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>服務登錄的影響很少無法使用？

在某些罕見的情況下，您可能會看到一些錯誤，例如 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
從您的應用程式記錄檔。 因為網路不穩定或其他網路問題，所以春季架構所引進的問題非常低。 

使用者體驗不會有任何影響，因為 eureka 用戶端會有心跳和重試原則來處理此情況。 您可以將它視為一個暫時性錯誤，並安全地略過它。

我們將在不久的將來增強這個部分，並避免使用者的應用程式發生此錯誤。


## <a name="next-steps"></a>後續步驟

如果您有其他問題，請參閱《 [Azure 春季雲端疑難排解指南》](spring-cloud-troubleshoot.md)。
