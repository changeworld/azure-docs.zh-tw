---
title: 有關 Azure 春雲的常見問題 |微軟文檔
description: 本文回答了有關 Azure 春雲的常見問題。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298764"
---
# <a name="azure-spring-cloud-faq"></a>Azure 春雲常見問題解答

本文回答了有關 Azure 春雲的常見問題。 

## <a name="general"></a>一般

### <a name="why-azure-spring-cloud"></a>為何選擇 Azure Spring Cloud？

Azure 春雲為春雲開發人員提供了一個平臺即服務 （PaaS）。 Azure Spring Cloud 管理應用程式基礎結構，以便您可以專注于應用程式代碼和業務邏輯。 Azure 春雲中內置的核心功能包括尤里卡、佈建服務器、服務註冊表伺服器、Pivotal 構建服務、藍綠色部署等。 此服務還使開發人員能夠將其應用程式與其他 Azure 服務綁定，例如 Azure 宇宙資料庫、MySQL 的 Azure 資料庫和 Redis 的 Azure 緩存。

Azure Spring Cloud 通過集成 Azure 監視器、應用程式見解和日誌分析，增強了開發人員和操作員的應用程式診斷體驗。

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Azure Spring Cloud 提供哪些服務方案？

Azure Spring Cloud 會在預覽期間提供一個服務方案。  Spring Cloud 部署包含 16 個 vCPU 內核和 32 GB 記憶體。  部署中的每個微服務實例的上限為 4 個 vCPU 內核，記憶體為 8 GB。

資源 | Amount
------- | -------
應用程式執行個體 (每個 Spring 應用程式) | 20
總應用程式執行個體 (每個 Azure Spring Cloud 服務執行個體) | 500
Azure Spring Cloud 服務執行個體 (每個訂用帳戶在每個區域) | 10
永續性磁碟區 | 10 x 50 GB

\*_要提高限制，打開[支援票證](https://azure.microsoft.com/support/faq/)。_

有關詳細資訊，請參閱[Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud 的安全性如何？

安全性和隱私是 Azure 和 Azure 春雲客戶的首要任務之一。 Azure 通過安全地加密所有這些資料，有助於確保只有客戶才能訪問應用程式資料、日誌或配置。 Azure Spring Cloud 中的所有服務執行個體都彼此互相隔離。

Azure 春雲提供完整的 TLS/SSL 和證書管理。

OpenJDK 和 Spring Cloud 執行階段的重大安全性修補，都會盡快套用至 Azure Spring Cloud。

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure 春雲在哪些區域可用？

美國東部、美國西部 2、西歐和東南亞。

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure 春雲的已知限制是什麼？

在預覽發佈期間，Azure 春雲具有以下已知限制：

* `spring.application.name`將覆蓋用於創建每個應用程式的應用程式名稱。
* `server.port`不允許在 Git 存儲庫中的設定檔中。 將其添加到設定檔可能會使應用程式無法從其他應用程式或 Internet 訪問。
* Azure 門戶和 Azure 資源管理器範本不支援上載應用程式包。 只能通過 Azure CLI 部署應用程式來上載應用程式包。
* 要瞭解配額限制，請參閱[Azure 春雲提供哪些服務方案？](#what-service-plans-does-azure-spring-cloud-offer)

### <a name="how-can-i-provide-feedback-and-report-issues"></a>如何提供意見反應和回報問題？

如果 Azure 春雲遇到任何問題，請創建[Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 要提交功能請求或提供回饋，請轉到[Azure 回饋](https://feedback.azure.com/forums/34192--general-feedback)。

## <a name="development"></a>部署

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>我是春雲開發人員，但屬於 Azure。 我學習如何開發 Azure 春雲應用程式的最快方法是什麼？

要獲得使用 Azure 春雲的最快方法，請按照["快速入門：使用 Azure 門戶啟動 Azure 春雲"應用程式](spring-cloud-quickstart-launch-app-portal.md)。

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud 支援的 Java 執行階段為何？

Azure Spring Cloud 支援 Java 8 和 11。

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>在哪裡可以查看 Spring Cloud 應用程式日誌和指標？

在 [應用程式概觀] 索引標籤和 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) \(部分機器翻譯\) 索引標籤中尋找計量。

Azure Spring Cloud 支援將春雲應用程式日誌和指標匯出到 Azure 存儲、事件Hub 和[日誌分析](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries)。 日誌分析中的表名稱是*AppPlatformLogsforSpring*。 要瞭解如何啟用它，請參閱[診斷服務](diagnostic-services.md)。

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud 是否支援分散式追蹤？

是。 有關詳細資訊，請參閱[教程：使用 Azure 春雲進行分散式跟蹤](spring-cloud-tutorial-distributed-tracing.md)。

### <a name="what-resource-types-does-service-binding-support"></a>服務繫結支援的資源類型為何？

當前支援三個服務：Azure 宇宙資料庫、MySQL 的 Azure 資料庫和用於 Redis 的 Azure 緩存。

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>是否可以查看、添加或移動應用程式內部的持久卷？

是。

## <a name="deployment"></a>部署

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure 春雲是否支援藍綠色部署？
是。 有關詳細資訊，請參閱[設置暫存環境](spring-cloud-howto-staging-environment.md)。

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>我是否可以存取 Kubernetes 來操作我的應用程式容器？

否。  Azure Spring Cloud 將基礎架構對開發人員抽象化，讓您能夠專注於應用程式程式碼和商務邏輯。

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud 是否支援從來源建立容器？

是。 有關詳細資訊，請參閱[從原始程式碼啟動春雲應用程式](spring-cloud-launch-from-source.md)。

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud 是否支援應用程式執行個體自動調整？

否。

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>將現有春雲微服務遷移到 Azure 春雲的最佳做法是什麼？

將現有的 Spring Cloud 微服務遷移到 Azure 春雲時，最好遵守以下最佳實踐：
* 必須解析所有應用程式相依性。
* 準備配置條目、環境變數和 JVM 參數，以便將它們與 Azure Spring Cloud 中的部署進行比較。
* 如果要使用服務綁定，請流覽 Azure 服務並確保已設置適當的存取權限。
* 我們建議您刪除或禁用可能與 Azure Spring Cloud 管理的服務（如我們的服務發現服務、佈建服務器等）衝突的任何嵌入式服務。
* 我們建議您使用官方、穩定的樞軸泉庫。 Pivotal Spring 庫的非官方、測試版或分叉版本沒有服務等級協定 （SLA） 支援。

遷移後，監視 CPU/RAM 指標和網路流量，以確保應用程式實例得到適當縮放。

## <a name="next-steps"></a>後續步驟

如果另有問題，請參閱 Azure[春雲故障排除指南](spring-cloud-troubleshoot.md)。
