---
title: 總覽-適用于雲端式應用程式和解決方案的 Azure 無伺服器
description: 瞭解如何使用 Azure Logic Apps 和 Azure Functions 來建立雲端式應用程式和解決方案，而不必擔心基礎結構
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666545"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 無伺服器：使用 Azure Logic Apps 和 Azure Functions 建立雲端式應用程式和解決方案的總覽

[無伺服器](https://azure.microsoft.com/solutions/serverless/)應用程式提供更多的優點，例如增加開發速度、減少程式碼、簡化和調整規模。 本文涵蓋無伺服器解決方案和 Azure 無伺服器供應專案的不同屬性。

## <a name="what-is-serverless"></a>什麼是無伺服器？

無伺服器並不表示沒有伺服器，而是開發人員不必擔心伺服器的問題。 大部分的傳統應用程式開發主要是回答有關調整、裝載及監視解決方案的問題，以滿足應用程式的需求。 在無伺服器的情況下，這些問題會納入解決方案中。 此外，無伺服器應用程式是以耗用量為基礎的方案來計費。 如果從未使用過應用程式，則不會產生任何費用。 這些功能可協助開發人員單獨專注于解決方案的商務邏輯。

適用于無伺服器的核心 Azure 服務是[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)和[Azure Functions](https://azure.microsoft.com/services/functions/)。 這兩種解決方案都遵循先前所述的原則，並協助開發人員以最少的程式碼建立強大的雲端應用程式

## <a name="what-is-azure-logic-apps"></a>什麼是 Azure Logic Apps？

[Azure Logic Apps](logic-apps-overview.md)提供一種方法，可在雲端中簡化及執行可調整的整合和工作流程。 這項服務提供視覺化設計工具，可將您的程式建立模型，並以一系列稱為工作流程的步驟將您的流程自動化。 雲端服務和內部部署系統之間有許多[連接器](../connectors/apis-list.md)，可快速將無伺服器應用程式連線至其他 api。 每個邏輯應用程式都是以觸發程式為開頭，例如「當帳戶新增至 Dynamics CRM 時」。 觸發程式引發之後，工作流程可以執行動作、轉換和條件式邏輯的組合。 Logic Apps 在協調進程中的不同 Azure Functions 時，是很好的選擇，特別是當進程需要與外部系統或 API 互動時。

若要開始使用 Logic Apps，請從[建立您的第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)開始。 如需 Logic Apps 的詳細技術資訊，請參閱[開發人員參考](logic-apps-workflow-definition-language.md)。

## <a name="what-is-azure-functions"></a>什麼是 Azure Functions？

Azure Functions 是一項服務，可讓您輕鬆地在雲端中執行程式碼片段或「函式」。 您只能撰寫目前問題所需的程式碼，而不需要擔心整個應用程式或基礎結構。 Functions 可讓開發更有生產力，而且您可以使用您選擇的開發語言，例如 C#、F#、Node.js、Python 或 PHP。 您只需支付程式碼執行的時間，Azure 就會視需要進行調整。

若要開始使用 Azure Functions，請從[建立您的第一個 Azure 函數](../azure-functions/functions-create-first-azure-function.md)開始。 如需函式的更多技術資訊，請參閱[開發人員參考](../azure-functions/functions-reference.md)。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>如何在 Azure 中建立及部署無伺服器應用程式？

Azure 提供豐富的工具來開發、部署和管理無伺服器應用程式。 您可以使用 Visual Studio 中的[工具](logic-apps-serverless-get-started-vs.md)或[Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)，直接在 Azure 入口網站中建立應用程式。 建立應用程式之後，您可以[使用 Azure Resource Manager 範本快速部署該應用程式](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 也提供監視，您可以透過 Azure 入口網站、透過 API 或 Sdk，或 Azure 監視器記錄和 Application Insights 的整合式工具來存取。

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中建立無伺服器應用程式](logic-apps-serverless-get-started-vs.md)
* [建立具有無伺服器的 customer insights 儀表板](logic-apps-scenario-social-serverless.md)
* [自動部署邏輯應用程式](logic-apps-azure-resource-manager-templates-overview.md)
