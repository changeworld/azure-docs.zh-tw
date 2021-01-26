---
title: 總覽-以雲端為基礎的應用程式和解決方案的 Azure 無伺服器
description: 瞭解如何使用 Azure Logic Apps 和 Azure Functions，建立雲端式應用程式和解決方案，而不必擔心基礎結構的相關資訊
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788090"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 無伺服器：使用 Azure Logic Apps 和 Azure Functions 建立雲端式應用程式和解決方案的總覽

[無伺服器](https://azure.microsoft.com/solutions/serverless/) 應用程式提供優點，例如增加開發速度、降低程式碼、簡化及調整規模。 本文涵蓋無伺服器解決方案和 Azure 無伺服器供應專案的不同屬性。

## <a name="what-is-serverless"></a>什麼是無伺服器？

無伺服器並不表示沒有伺服器，而是開發人員不需要擔心伺服器。 大部分的傳統應用程式開發主要是回答有關調整、裝載及監視解決方案的問題，以滿足應用程式的需求。 在無伺服器的情況下，這些問題會被視為解決方案的一部分。 此外，無伺服器應用程式會依取用方案計費。 如果從未使用過應用程式，則不會產生任何費用。 這些功能可協助開發人員僅專注于解決方案的商務邏輯。

適用于無伺服器的核心 Azure 服務是 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) 和 [Azure Functions](https://azure.microsoft.com/services/functions/)。 這兩種解決方案都遵循先前所述的原則，並協助開發人員以最少量的程式碼建立強大的雲端應用程式

## <a name="what-is-azure-logic-apps"></a>什麼是 Azure Logic Apps？

[Azure Logic Apps](logic-apps-overview.md) 可讓您在雲端中簡化和執行可調整的整合和工作流程。 這項服務提供視覺化設計工具，以一系列稱為工作流程的步驟來建立程式模型並將其自動化。 雲端服務和內部部署系統之間有許多 [連接器](../connectors/apis-list.md) ，可將無伺服器應用程式快速連接至其他 api。 每個邏輯應用程式都是以觸發程式為開頭，例如「當帳戶新增至 Dynamics CRM 時」。 觸發程式引發之後，工作流程可以執行動作、轉換和條件式邏輯的組合。 Logic Apps 在協調進程中的不同 Azure Functions 時是絕佳的選擇，特別是當程式需要與外部系統或 API 互動時。

若要開始使用 Logic Apps，請從[建立您的第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)開始。 如需有關 Logic Apps 的詳細技術資訊，請參閱 [開發人員參考](logic-apps-workflow-definition-language.md)。

## <a name="what-is-azure-functions"></a>什麼是 Azure Functions？

Azure Functions 是一項服務，可讓您輕鬆地在雲端中執行程式碼片段或「函式」。 您可以只撰寫目前問題所需的程式碼，而不必擔心整個應用程式或基礎結構的需求。 Functions 可讓開發更有生產力，而且您可以使用您選擇的開發語言，例如 C#、F#、Node.js、Python 或 PHP。 您只需支付程式碼執行的時間，而 Azure 會視需要進行調整。

若要開始使用 Azure Functions，請先 [建立您的第一個 Azure 函數](../azure-functions/functions-get-started.md)。 如需函數的詳細技術資訊，請參閱 [開發人員參考](../azure-functions/functions-reference.md)。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>如何在 Azure 中建立和部署無伺服器應用程式？

Azure 提供豐富的工具來開發、部署及管理無伺服器應用程式。 您可以使用 [Visual Studio 中的工具](logic-apps-serverless-get-started-vs.md)或 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)，直接在 Azure 入口網站中建立應用程式。 建立應用程式之後，您可以 [使用 Azure Resource Manager 範本快速部署該應用程式](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 也會提供監視，您可以透過 Azure 入口網站、透過 API 或 Sdk 來存取，或透過整合式工具來存取 Azure 監視器記錄和 Application Insights。

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio 中建立無伺服器應用程式](logic-apps-serverless-get-started-vs.md)
* [使用無伺服器建立客戶見解儀表板](logic-apps-scenario-social-serverless.md)
* [自動部署邏輯應用程式](logic-apps-azure-resource-manager-templates-overview.md)