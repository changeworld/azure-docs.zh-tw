---
title: Azure Functions 耗用量方案裝載
description: 瞭解 Azure 函數取用方案裝載如何讓您在可動態調整規模的環境中執行程式碼，但您只需支付執行期間所使用的資源費用。
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760543"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions 耗用量方案裝載

當您使用取用方案時，會根據傳入事件的數目，動態新增和移除 Azure Functions 主控制項的實例。 取用方案是 Azure Functions 的完全 <em>無伺服器</em> 裝載選項。

## <a name="benefits"></a>優點

取用方案會自動調整，即使在高負載期間也會自動調整。 在取用方案中執行函式時，只有在您的函式執行時才會向您收取計算資源的費用。 在取用方案中，函式執行會在一段可設定的時間之後逾時。

如需將取用方案與其他方案和裝載類型的比較，請參閱函式 [規模和裝載選項](functions-scale.md)。

## <a name="billing"></a>計費

帳單是根據執行數目、執行時間以及使用的記憶體。 使用量會在函式應用程式內的所有函式中匯總。 如需詳細資訊，請參閱 [Azure Functions 價格頁面](https://azure.microsoft.com/pricing/details/functions/)。

若要深入瞭解如何在取用方案中執行時預估成本，請參閱 [瞭解使用量方案成本](functions-consumption-costs.md)。

## <a name="create-a-consumption-plan-function-app"></a>建立耗用量方案函式應用程式

當您在 Azure 入口網站中建立函數應用程式時，取用方案是預設值。 使用 Api 來建立函數應用程式時，您不需要先建立 App Service 計畫，就像使用 Premium 和專用方案一樣。

您可以使用下列連結來瞭解如何以程式設計方式或在 Azure 入口網站中，在取用方案中建立無伺服器函式應用程式：

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure 入口網站](./functions-get-started.md)
+ [Azure Resource Manager 範本](functions-create-first-function-resource-manager.md)

當您從 [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) 或 [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure)發行函數專案時，也可以在取用方案中建立函數應用程式。

## <a name="multiple-apps-in-the-same-plan"></a>相同方案中的多個應用程式

相同區域中的函數應用程式可以指派給相同的取用方案。 在相同的取用方案中執行多個應用程式不會造成任何不利或影響。 將多個應用程式指派給相同的取用方案，並不會影響每個應用程式的復原能力、擴充性或可靠性。

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 裝載選項](functions-scale.md)
+ [Azure Functions 中的事件驅動規模調整](event-driven-scaling.md)
