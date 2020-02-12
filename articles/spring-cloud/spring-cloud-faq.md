---
title: 關於 Azure 春季雲端的常見問題 |Microsoft Docs
description: 本文會回答有關 Azure 春季雲端的常見問題。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: e4fbeef06ae49ffe24f84b1a12dbcdfe0a5f1fec
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278563"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春季雲端常見問題

本文會回答有關 Azure 春季雲端的常見問題。 

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure Spring Cloud？

Azure 春季雲端為雲端開發人員提供了平臺即服務（PaaS）。 Azure 春季雲端會管理您的應用程式基礎結構，讓您可以專注于應用程式的程式碼和商務邏輯。 Azure 春季雲端內建的核心功能包括 Eureka、設定伺服器、服務登錄伺服器、Pivotal 組建服務、藍綠部署等等。 此服務也可讓開發人員將其應用程式與其他 Azure 服務（例如 Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis）系結。

Azure 春季雲端藉由整合 Azure 監視器、Application Insights 和 Log Analytics，為開發人員和操作員增強應用程式診斷體驗。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服務方案？

Azure Spring Cloud 會在預覽期間提供一個服務方案。  春季雲端部署包含16個 vCPU 核心和 32 gb 的記憶體。  部署中每個微服務實例的上限是4個具有 8 GB 記憶體的 vCPU 核心。

資源 | 金額
------- | -------
應用程式執行個體 (每個 Spring 應用程式) | 20
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 500
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10
永續性磁碟區 | 10 x 50 GB

\*_若要提高限制，請開啟[支援票證](https://azure.microsoft.com/support/faq/)。_

如需詳細資訊，請參閱[Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隱私權是 Azure 和 Azure 春季雲端客戶的最高優先順序。 Azure 可透過安全地加密所有資料，協助確保只有客戶可以存取應用程式資料、記錄或設定。 Azure Spring Cloud 中的所有服務執行個體都彼此互相隔離。

Azure Spring Cloud 提供完整的 SSL 和憑證管理。

OpenJDK 和 Spring Cloud 執行階段的重大安全性修補，都會盡快套用至 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春季雲端有哪些區域可供使用？

美國東部、美國西部 2、西歐和東南亞。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春季雲端的已知限制為何？

在預覽版本中，Azure 春季雲端具有下列已知限制：

* `spring.application.name` 將會被用來建立每個應用程式的應用程式名稱覆寫。
* Git 存放庫的設定檔中不允許 `server.port`。 將它新增至設定檔，可能會導致您的應用程式無法從其他應用程式或網際網路連線。
* Azure 入口網站和 Azure Resource Manager 範本不支援上傳應用程式套件。 您只能透過 Azure CLI 部署應用程式來上傳應用程式套件。
* 若要瞭解配額限制，請參閱[Azure 春季雲端供應專案有哪些服務方案？](#what-service-plans-does-azure-spring-cloud-offer)。

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和回報問題？

如果您遇到 Azure 春季雲端的任何問題，請建立[Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 若要提交功能要求或提供意見反應，請前往[Azure 意見](https://feedback.azure.com/forums/34192--general-feedback)反應。

## <a name="development"></a>開發

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是雲端開發人員，而不是 Azure 的新手。 若要瞭解如何開發 Azure 春季雲端應用程式，最快的方式是？

如需開始使用 Azure 春季雲端的最快速方式，請遵循[快速入門：使用 Azure 入口網站啟動 Azure 春季雲端應用程式](spring-cloud-quickstart-launch-app-portal.md)中的指示。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支援的 Java 執行階段為何？

Azure Spring Cloud 支援 Java 8 和 11。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>我可以在哪裡查看春季雲端應用程式記錄和計量？

在 [應用程式概觀] 索引標籤和 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) \(部分機器翻譯\) 索引標籤中尋找計量。

Azure 春季雲端支援將春季雲端應用程式記錄和計量匯出至 Azure 儲存體、EventHub 和[Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 Log Analytics 中的資料表名稱是*AppPlatformLogsforSpring*。 若要瞭解如何啟用它，請參閱[診斷服務](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支援分散式追蹤？

可以。 如需詳細資訊，請參閱[教學課程：搭配 Azure 春季雲端使用分散式追蹤](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服務繫結支援的資源類型為何？

目前支援三項服務： Azure Cosmos DB、適用於 MySQL 的 Azure 資料庫和 Azure Cache for Redis。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>我可以從應用程式內查看、新增或移動持續性磁片區嗎？

可以。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春季雲端是否支援藍綠部署？
可以。 如需詳細資訊，請參閱[設定預備環境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我是否可以存取 Kubernetes 來操作我的應用程式容器？

不會。  Azure Spring Cloud 將基礎架構對開發人員抽象化，讓您能夠專注於應用程式程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支援從來源建立容器？

可以。 如需詳細資訊，請參閱[從原始程式碼啟動您的春天雲端應用程式](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支援應用程式執行個體自動調整？

不會。

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
