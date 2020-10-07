---
title: Azure Functions 概觀
description: 了解如何使用 Azure Functions，在數分鐘內最佳化非同步的工作負載。
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "77621012"
---
# <a name="an-introduction-to-azure-functions"></a>Azure Functions 簡介

Azure Functions 可讓您執行一小段程式碼 (稱為「函式」)，而無須顧慮應用程式基礎結構。 透過 Azure Functions，雲端基礎結構可為您提供大規模持續執行應用程式所需的所有最新伺服器。

函式由特定類型的事件所「觸發」。 [支援的觸發程序](./functions-triggers-bindings.md)包括回應資料的變更、回應訊息、按照排程執行，或作為 HTTP 要求的結果。

雖然您一律可直接對各式各樣的服務撰寫程式碼，但與其他服務的整合可利用繫結來簡化。 繫結可讓您[對各種不同的 Azure 和第三方服務進行宣告式存取](./functions-triggers-bindings.md)。

## <a name="features"></a>特性

Azure Functions 的主要功能包括︰

- **無伺服器應用程式**：Functions 可讓您在 Microsoft Azure 上開發[無伺服器](https://azure.microsoft.com/solutions/serverless/)應用程式。

- **語言的選擇**：使用您選擇的 [C#、Java、JavaScript、Python 和 PowerShell](supported-languages.md) 來撰寫函式。

- **按使用次數付費的定價模式**：僅針對您執行程式碼所花費的時間付費。 請參閱[價格區段](#pricing)中的使用情況主控方案選項。  

- **自備相依性**：Functions 支援 NuGet 和 NPM，以便您存取慣用的程式庫。

- **整合式安全性**：利用 OAuth 提供者 (如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶) 保護 HTTP 觸發的函式。

- **簡化的整合**：輕易與 Azure 服務和軟體即服務 (SaaS) 供應項目整合。

- **彈性開發**：透過 [GitHub](../app-service/scripts/cli-continuous-deployment-github.md)、[Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) 與其他[支援的開發工具](../app-service/deploy-local-git.md)設定連續整合並部署您的程式碼。

- **具狀態的無伺服器架構**：使用 [Durable Functions](durable/durable-functions-overview.md) 來協調無伺服器應用程式。

- **開放原始碼**：Functions 執行階段是開放原始碼，且[可在 GitHub 上取得](https://github.com/azure/azure-webjobs-sdk-script)。

## <a name="what-can-i-do-with-functions"></a>我可以用 Functions 來做什麼？

Functions 是處理大量資料、整合系統、使用物聯網 (IoT)，以及建置簡單 API 和微服務的絕佳解決方案。

您可以利用一系列的範本來展開重要的案例，包括︰

- **HTTP**：根據 [HTTP 要求](functions-create-first-azure-function.md)執行程式碼

- **計時器**：將程式碼排定[在預先定義的時間執行](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**：處理[新的和修改過的 Azure Cosmos DB 文件](./functions-create-cosmos-db-triggered-function.md)

- **Blob 儲存體**：處理[新的和修改過的 Azure 儲存體 Blob](./functions-create-storage-blob-triggered-function.md)

- **佇列儲存體**：回應 [Azure 儲存體佇列訊息](./functions-create-storage-queue-triggered-function.md)

- **事件方格**：[透過訂用帳戶和篩選條件回應 Azure 事件方格事件](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **事件中樞**：回應[大量的 Azure 事件中樞事件](./functions-bindings-event-hubs.md)

- **服務匯流排佇列**：藉由[回應服務匯流排佇列訊息](./functions-bindings-service-bus.md)來連線至其他 Azure 或內部部署服務

- **服務匯流排主題**：藉由[回應服務匯流排主題訊息](./functions-bindings-service-bus.md)來連接其他 Azure 服務或內部部署服務

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Functions 的計費方式

Azure Functions 有三種定價方案。 請選擇最符合您需求的方案：

- **使用量方案**：Azure 會提供所有必要的運算資源。 您不必擔心資源管理，且只需針對程式碼執行的時間支付費用。

- **進階方案**：您可以指定一些預先準備的執行個體，而這些執行個體一律會上線並且可立即回應。 當您的函式執行時，Azure 會提供所需的任何額外計算資源。 您會針對持續執行的預先準備執行個體，以及在 Azure 相應放大和縮小您應用程式時使用的任何其他執行個體付費。

- **App Service 方案**：像執行 Web 應用程式一樣執行您的函式。 如果您對其他應用程式使用 App Service，您可以在相同方案上執行您的函式，不會產生額外的費用。

如需主控方案的詳細資訊，請參閱 [Azure Functions 主控方案比較](functions-scale.md)。 在 [Functions 價格](https://azure.microsoft.com/pricing/details/functions/)頁面上可取得完整的價格詳細資料。

## <a name="next-steps"></a>後續步驟

- [建立您的第一個Azure Functions](functions-create-first-function-vs-code.md)  
  開始使用 [Visual Studio Code](functions-create-first-function-vs-code.md)、[命令列](functions-create-first-azure-function-azure-cli.md)，或使用 [Azure 入口網站](functions-create-first-azure-function.md)。

- [Azure Functions 開發人員參考](functions-reference.md)  
  提供更多有關 Azure Functions 執行階段的技術資訊，以及可供撰寫函數程式碼及定義觸發程序和繫結時參考。

- [如何調整 Azure 函式](functions-scale.md)  
  討論 Azure Functions 可用的服務方案，包括使用情況主控方案，以及如何選擇正確的方案。

- [深入了解 Azure App Service](../app-service/overview.md)  
  Azure Functions 會利用 Azure App Service 來執行核心功能，例如部署、環境變數和診斷。
