---
title: 關於 Azure 春季雲端的常見問題 |Microsoft Docs
description: 本文會回答有關 Azure 春季雲端的常見問題。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: f1871871fa3a191c636a965977dba485d2c77f1f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037503"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春季雲端常見問題

本文會回答有關 Azure 春季雲端的常見問題。

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure Spring Cloud？

Azure 春季雲端為雲端開發人員提供了平臺即服務（PaaS）。 Azure 春季雲端會管理您的應用程式基礎結構，讓您可以專注于應用程式的程式碼和商務邏輯。 Azure 春季雲端內建的核心功能包括 Eureka、設定伺服器、服務登錄伺服器、Pivotal 組建服務、藍綠部署等等。 此服務也可讓開發人員將其應用程式與其他 Azure 服務（例如 Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis）系結。

Azure 春季雲端藉由整合 Azure 監視器、Application Insights 和 Log Analytics，為開發人員和操作員增強應用程式診斷體驗。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隱私權是 Azure 和 Azure 春季雲端客戶的最高優先順序。 Azure 可透過安全地加密所有資料，協助確保只有客戶可以存取應用程式資料、記錄或設定。 

* Azure 春季雲端中的服務實例會彼此隔離。
* Azure 春季雲端提供完整的 TLS/SSL 和憑證管理。
* OpenJDK 和 Spring Cloud 執行階段的重大安全性修補，都會盡快套用至 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春季雲端有哪些區域可供使用？

美國東部、美國西部 2、西歐和東南亞。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季雲端的已知限制為何？

在預覽版本中，Azure 春季雲端具有下列已知限制：

* `spring.application.name`將會被用來建立每個應用程式的應用程式名稱覆寫。
* `server.port`預設為埠80/443。 如果套用任何其他值，則會將它覆寫為80/443。
* Azure 入口網站和 Azure Resource Manager 範本不支援上傳應用程式套件。 您只能透過 Azure CLI 部署應用程式來上傳應用程式套件。

### <a name="what-pricing-tiers-are-available"></a>有哪些定價層可供使用？ 
我應該使用哪一種，以及每一層內的限制為何？
* Azure 春季雲端提供兩個定價層：基本和標準。 基本層是以開發/測試為目標，並試用 Azure 春季雲端。 標準層已優化，可執行一般用途的生產流量。 如需限制和功能層級的比較，請參閱[Azure 春季雲端定價詳細資料](https://azure.microsoft.com/pricing/details/spring-cloud/)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和回報問題？

如果您遇到 Azure 春季雲端的任何問題，請建立[Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 若要提交功能要求或提供意見反應，請前往[Azure 意見](https://feedback.azure.com/forums/34192--general-feedback)反應。

## <a name="development"></a>部署

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是雲端開發人員，而不是 Azure 的新手。 若要瞭解如何開發 Azure 春季雲端應用程式，最快的方式是？

如需開始使用 Azure 春季雲端的最快速方式，請遵循[快速入門：使用 Azure 入口網站啟動 Azure 春季雲端應用程式](spring-cloud-quickstart-launch-app-portal.md)中的指示。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支援的 Java 執行階段為何？

Azure Spring Cloud 支援 Java 8 和 11。 請參閱[JAVA 執行時間和作業系統版本](#java-runtime-and-os-versions)

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>我可以在哪裡查看春季雲端應用程式記錄和計量？

在 [應用程式概觀] 索引標籤和 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) \(部分機器翻譯\) 索引標籤中尋找計量。

Azure 春季雲端支援將春季雲端應用程式記錄和計量匯出至 Azure 儲存體、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的資料表名稱是*AppPlatformLogsforSpring*。 若要瞭解如何啟用它，請參閱[診斷服務](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支援分散式追蹤？

是。 如需詳細資訊，請參閱[教學課程：搭配 Azure 春季雲端使用分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服務繫結支援的資源類型為何？

目前支援三個服務：
* Azure Cosmos DB
* 適用於 MySQL 的 Azure 資料庫
* Azure Redis 快取。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>我可以從應用程式內查看、新增或移動持續性磁片區嗎？

是。

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>當我刪除/移動 Azure 春季雲端服務實例時，其延伸模組資源也會一併刪除/移動？

這取決於擁有延伸模組資源之資源提供者的邏輯。 實例的擴充功能資源 `Microsoft.AppPlatform` 不屬於相同的命名空間，因此行為會因資源提供者而異。 例如，刪除/移動作業不會串聯至**診斷設定**資源。 如果新的 Azure 春季雲端實例布建時所使用的資源識別碼與已刪除的相同，或先前的 Azure 春季雲端實例已移回，則先前的**診斷設定**資源會繼續擴充。

## <a name="java-runtime-and-os-versions"></a>JAVA 執行時間和作業系統版本

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure 春季雲端支援哪些版本的 JAVA 執行時間？

Azure 春季雲端支援 JAVA LTS 版本與最新組建（目前為2020年6月），支援 JAVA 8 組建252和 JAVA 11 build 7。 請參閱[安裝適用于 Azure 的 JDK 和 Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>誰建立了這些 JAVA 執行時間？

Azul 系統。 適用於 Azure 的 Azul Zulu - 企業版 JDK 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。

### <a name="how-often-will-java-runtimes-get-updated"></a>JAVA 執行時間的更新頻率為何？

LTS 和 MTS JDK 版本會有每季的安全性更新和錯誤修正，並視需要提供頻外的重大更新和修補程式。 這項支援包括可回移在較新版本的 Java (如 Java 11) 中報告的 Java 7 和 8 安全性更新和錯誤修正。

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>支援 JAVA 8 和 JAVA 11 LTS 版本的時間長度？

請參閱[適用于 Azure 和 Azure Stack 的 JAVA 長期支援](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)。

* 在2030年12月之前，將支援 JAVA 8 LTS。
* 在2027年9月之前，將支援 JAVA 11 LTS。

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>如何下載支援的 JAVA 執行時間以進行本機開發？

請參閱[安裝適用于 Azure 的 JDK 和 Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)。

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>舊版 JAVA 執行時間的淘汰原則是什麼？

公開通知會在過去12個月內送出，然後才淘汰任何舊的執行階段版本。 您將會有12個月的時間，可以遷移至較新的版本。

* 當我們將淘汰 JAVA 版本時，訂用帳戶管理員會收到電子郵件通知。
* 淘汰資訊將會在檔中發佈。

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>如何在 JAVA 執行時間層級取得問題的支援？

您可以使用 Azure 支援開啟支援票證。  請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

### <a name="what-is-the-operation-system-to-run-my-apps"></a>執行我的應用程式的作業系統是什麼？

會使用最新的 Ubuntu LTS 版本，目前的[ubuntu 20.04 LTS （焦點 Fossa）](https://releases.ubuntu.com/focal/)是預設的作業系統。

### <a name="how-often-will-os-security-patches-be-applied"></a>OS 安全性修補程式的套用頻率為何？

適用于 Azure 春季雲端的安全性修補程式將以每月的方式推出至生產環境。
適用于 Azure 春季雲端的重大安全性修補程式（CVE 分數 >= 9）將會儘快推出。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季雲端是否支援藍綠部署？
是。 如需詳細資訊，請參閱[設定預備環境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我是否可以存取 Kubernetes 來操作我的應用程式容器？

不可以。  Azure Spring Cloud 將基礎架構對開發人員抽象化，讓您能夠專注於應用程式程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支援從來源建立容器？

是。 如需詳細資訊，請參閱[從原始程式碼啟動您的春天雲端應用程式](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支援應用程式執行個體自動調整？

不可以。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>將現有的春季 Cloud 微服務遷移至 Azure 春季雲端的最佳作法為何？

當您將現有的春季 Cloud 微服務遷移至 Azure 春季雲端時，最好先觀察下列最佳作法：
* 必須解析所有應用程式相依性。
* 準備您的設定專案、環境變數及 JVM 參數，讓您可以將它們與 Azure 春季雲端中的部署進行比較。
* 如果您想要使用服務系結，請流覽您的 Azure 服務，並確定您已設定適當的存取權限。
* 建議您移除或停用任何可能與 Azure 春季雲端所管理的服務發生衝突的內嵌服務，例如我們的服務探索服務、設定伺服器等等。
* 建議您使用官方、穩定的 Pivotal 彈簧程式庫。 非官方、搶鮮版（Beta）或分支版的 Pivotal 春季程式庫沒有服務等級協定（SLA）支援。

在遷移之後，請監視 CPU/RAM 計量和網路流量，以確保應用程式實例會適當地調整。

## <a name="next-steps"></a>後續步驟

如果您有進一步的問題，請參閱[Azure 春季雲端疑難排解指南](spring-cloud-troubleshoot.md)。
