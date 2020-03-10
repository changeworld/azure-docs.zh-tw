---
title: Azure Spring Cloud 簡介
description: 了解 Azure Spring Cloud 的功能與優點，以在 Azure 中部署及管理 Java Spring 應用程式。
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273266"
---
# <a name="what-is-azure-spring-cloud"></a>什麼是 Azure Spring Cloud？

Azure Spring Cloud 可讓您在不需要變更程式碼的情況下，輕鬆地將以 Spring Boot 為基礎的微服務應用程式部署到 Azure。  Azure Spring Cloud 會管理 Spring Cloud 應用程式的生命週期，讓開發人員可以專注於處理程式碼。  Spring Cloud 能使用全方位的監視和診斷、設定管理、服務探索、CI/CD 整合、藍色-綠色部署等等，來提供生命週期管理。

Azure Spring Cloud 作為 Azure 生態系統的一部分，可讓您輕鬆繫結到其他 Azure 服務，包括儲存體、資料庫、監視等等。

Azure Spring Cloud 目前提供公開預覽版。 公開預覽版供應項目可讓客戶在其正式發行前，先試驗新功能。  公開預覽功能和服務不適用於生產環境。  如需有關預覽期間支援的詳細資訊，請參閱我們的[常見問題集](https://azure.microsoft.com/support/faq/)或提出[支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)以深入了解。

若要開始使用，請使用 [Azure CLI](spring-cloud-quickstart-launch-app-cli.md)、[Azure 入口網站](spring-cloud-quickstart-launch-app-portal.md)，或是 [Maven](spring-cloud-quickstart-launch-app-maven.md) 來完成 Spring Cloud 快速入門。

GitHub 上可用的其他範例：[Azure Spring Cloud 範例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。

## <a name="application-configuration"></a>應用程式組態

### <a name="spring-cloud-config-server"></a>Spring Cloud Config Server

Azure Spring Cloud Config Server 能在分散式系統中提供外部化設定，並同時支援伺服器端和用戶端。  Config Server 能提供中央位置來管理所有環境的應用程式屬性。  若要深入了解，請造訪 [Spring Cloud Config Server 參考](https://spring.io/projects/spring-cloud-config.md)並完成該教學課程。

### <a name="enable-bluegreen-deployments"></a>啟用藍色/綠色部署

Azure Spring Cloud 支援藍色/綠色部署，以用來將程式碼發佈及更新至生產環境。  利用此變更管理模式，開發人員便可在實作功能和程式碼變更的同時，享有能在必要時立即回溯的安全性。  Azure 可透過管理多個生產環境，並讓開發人員能在不中斷應用程式下輕鬆地更新或復原程式碼變更，來使開發人員能專注在撰寫程式碼上。  若要深入了解預備環境及藍色/綠色部署，請造訪這篇[操作說明文章](spring-cloud-howto-staging-environment.md)。

### <a name="automate-cicd-pipelines"></a>將 CI/CD 管線自動化

Azure Spring Cloud 能使用 Azure CLI 提供與 Azure DevOps 的整合。  您可以使用 Azure DevOps 來將針對您 Spring 應用程式的程式碼整合及部署自動化。  若要深入了解，請造訪這篇[文章](spring-cloud-howto-cicd.md)。

### <a name="scale-your-application"></a>調整您的應用程式

Azure Spring Cloud 可讓您在 Azure Spring Cloud 儀表板中輕鬆地調整微服務。  可供您微服務使用的 vCPU 數目和記憶體數量，皆可以依您的需求相應增加或減少。  調整能在數秒內生效，且不需要進行程式碼變更或重新部署。  若要深入了解，請完成此[教學課程](spring-cloud-tutorial-scale-manual.md)。

## <a name="application-monitoring"></a>應用程式監視

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>使用分散式追蹤和 Azure App Insights 來監視您的應用程式

Spring Cloud 的分散式追蹤工具可讓開發人員對其應用程式中的微服務之間的複雜相互關聯進行偵錯及監視。  透過將 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) \(英文\) 與 Azure 的 [Application Insights](../azure-monitor/insights/insights-overview.md) 整合，Azure 便能直接從 Azure 入口網站提供強大的分散式追蹤功能。  若要深入了解，請完成此[教學課程](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="next-steps"></a>後續步驟

- [從 CLI 啟動您的 Spring Cloud 應用程式](spring-cloud-quickstart-launch-app-cli.md)
