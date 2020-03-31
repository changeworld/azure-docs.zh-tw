---
title: 概述 - 適用于基於雲的應用和解決方案的 Azure 無伺服器
description: 瞭解如何使用 Azure 邏輯應用和 Azure 函數創建基於雲的應用和解決方案，而不必擔心基礎結構
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666545"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 無伺服器：使用 Azure 邏輯應用和 Azure 函數構建基於雲的應用和解決方案的概述

[無伺服器](https://azure.microsoft.com/solutions/serverless/)應用具有提高開發速度、減少代碼、簡單性和擴展等優點。 本文介紹無伺服器解決方案和 Azure 無伺服器產品的不同屬性。

## <a name="what-is-serverless"></a>什麼是無伺服器？

無伺服器並不意味著沒有伺服器，而是開發人員不必擔心伺服器。 大部分的傳統應用程式開發主要是回答有關調整、裝載及監視解決方案的問題，以滿足應用程式的需求。 使用無伺服器，這些問題將作為解決方案的一部分處理。 此外，無伺服器應用按基於消耗的計畫計費。 如果從未使用該應用程式，則不收取任何費用。 這些功能可説明開發人員只關注解決方案的業務邏輯。

用於無伺服器的核心 Azure 服務是[Azure 邏輯應用](https://azure.microsoft.com/services/logic-apps/)和[Azure 函數](https://azure.microsoft.com/services/functions/)。 這兩種解決方案都遵循前面描述的原則，並説明開發人員以最少的代碼構建強大的雲應用。

## <a name="what-is-azure-logic-apps"></a>什麼是 Azure Logic Apps？

[Azure 邏輯應用](logic-apps-overview.md)提供了一種在雲中簡化和實現可擴展集成和工作流的方法。 此服務提供視覺化設計器，將流程建模並自動執行，作為稱為工作流的一系列步驟。 雲服務和本地系統中有許多[連接器](../connectors/apis-list.md)，它們可快速將無伺服器應用連接到其他 API。 每個邏輯應用都以觸發器開頭，例如"當帳戶添加到 Dynamics CRM 時"。 觸發觸發器後，工作流可以運行操作、轉換和條件邏輯的組合。 邏輯應用是流程中協調不同 Azure 函數的絕佳選擇，尤其是在該過程需要與外部系統或 API 交互時。

若要開始使用 Logic Apps，請從[建立您的第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)開始。 有關邏輯應用的更多技術資訊，請參閱[開發人員參考](logic-apps-workflow-definition-language.md)。

## <a name="what-is-azure-functions"></a>什麼是 Azure Functions？

Azure 函數是一種服務，用於在雲中輕鬆運行代碼或"函數"。 您只能編寫當前問題所需的代碼，而無需擔心整個應用或所需的基礎結構。 Functions 可讓開發更有生產力，而且您可以使用您選擇的開發語言，例如 C#、F#、Node.js、Python 或 PHP。 只需為代碼運行和 Azure 根據需要縮放的時間付費。

要開始使用 Azure 函數，請從[創建第一個 Azure 函數](../azure-functions/functions-create-first-azure-function.md)開始。 有關函數的更多技術資訊，請參閱[開發人員參考](../azure-functions/functions-reference.md)。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>如何在 Azure 中構建和部署無伺服器應用？

Azure 為開發、部署和管理無伺服器應用提供了豐富的工具。 您可以使用 Visual Studio 或[Visual Studio 代碼](quickstart-create-logic-apps-visual-studio-code.md)[中的工具](logic-apps-serverless-get-started-vs.md)直接在 Azure 門戶中生成應用。 生成應用後，可以使用 Azure[資源管理器範本快速部署該應用](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 還提供監視，您可以通過 Azure 門戶、API 或 SDK 或 Azure 監視器日誌和應用程式見解的集成工具進行訪問。

## <a name="next-steps"></a>後續步驟

* [在視覺化工作室中構建無伺服器應用程式](logic-apps-serverless-get-started-vs.md)
* [使用無伺服器創建客戶洞察儀表板](logic-apps-scenario-social-serverless.md)
* [將邏輯應用程式部署自動化](logic-apps-azure-resource-manager-templates-overview.md)
