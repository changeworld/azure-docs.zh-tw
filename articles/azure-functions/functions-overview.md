---
title: Azure Functions 概觀
description: 了解 Azure Functions 如何協助建置強固的無伺服器應用程式。
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 269d911c13cbc59a054c5548e106780d630c8e2b
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620863"
---
# <a name="introduction-to-azure-functions"></a>Azure Functions 簡介

Azure Functions 是無伺服器解決方案，可讓您撰寫較少的程式碼、維護較少的基礎結構，並節省成本。 雲端基礎結構會提供讓應用程式保持執行所需的所有最新伺服器，而不需要擔心部署和維護伺服器。

您可以著重於最重要的程式碼片段，讓 Azure Functions 處理其餘部分。<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

我們經常會建置系統來回應一系列的重大事件。 無論是建立 Web API、回應資料庫變更、處理 IoT 資料流程，甚至是管理訊息佇列，每個應用程式都需要一種方法，可在這些事件發生時執行某些程式碼。

為了符合此需求，Azure Functions 在兩種重要的方式中提供「隨選計算」。

首先，Azure Functions 可讓您將系統的邏輯實作為容易使用的程式碼區塊。 這些程式碼區塊稱為「函式」。 當您需要回應重大事件時，可以隨時執行不同的函式。

第二，隨著要求的增加，Azure Functions 會符合需求，視需要提供多個資源和函式執行個體 - 但只有在必要時才提供。 隨著要求的下降，系統會自動捨棄任何額外的資源和應用程式執行個體。

所有計算資源來自何處？ Azure Functions [會視需要提供多個或較少的計算資源](./functions-scale.md)，以符合您的應用程式需求。

視需要提供計算資源，是 Azure Functions 中[無伺服器運算](https://azure.microsoft.com/solutions/serverless/)的要素。

## <a name="scenarios"></a>案例

在許多情況下，函式[會與雲端服務整合](./functions-triggers-bindings.md)，以提供功能豐富的實作。

以下是常見，但「不是全部」的 Azure Functions 案例集合。

| 如果您想要... | 接著... |
| --- | --- |
| **建置 Web API** | 使用 [HTTP 觸發程序](./functions-bindings-http-webhook.md)為您的 Web 應用程式實作端點 |
| **處理檔案上傳** | 在 [Blob 儲存體](./functions-bindings-storage-blob.md)中上傳或變更檔案時執行程式碼 |
| **建置無伺服器工作流程** | 使用 [Durable Functions](./durable/durable-functions-overview.md) 將一系列函式鏈結在一起 |
| **回應資料庫變更** | 在 [Cosmos DB](./functions-bindings-cosmosdb-v2.md) 中建立或更新文件時，執行自訂邏輯 Cosmos DB |
| **執行排程的工作** | 在[設定時間](./functions-bindings-timer.md)執行程式碼 |
| **建立可靠的訊息佇列系統** | 使用[佇列儲存體](./functions-bindings-storage-queue.md)、[服務匯流排](./functions-bindings-service-bus.md)或[事件中樞](./functions-bindings-event-hubs.md)處理訊息佇列 |
| **分析 IoT 資料流** | 收集和處理[來自 IoT 裝置的資料](./functions-bindings-event-iot.md) |
| **即時處理資料** | 使用[函式和 SignalR](./functions-bindings-signalr-service.md) 在當下回應資料 |

建置函式時，可使用下列選項和資源：

- **使用您慣用的語言**：以 [C# 、JAVA、JavaScript、PowerShell 或 Python](./supported-languages.md) 撰寫函式，或使用 [自訂處理常式](./functions-custom-handlers.md)以虛擬方式使用任何其他語言。

- **自動化部署**：從工具型方法到使用外部管線，有 [許多部署選項](./functions-deployment-technologies.md)可供使用。

- **函式疑難排解**：使用 [監視工具](./functions-monitoring.md)和 [測試策略](./functions-test-a-function.md)，深入了解您的應用程式。

- **彈性定價選項**：透過 [耗用量](./pricing.md) 方案，您只需要在函式執行時付費，而 [進階](./pricing.md)和 [App Service](./pricing.md) 方案會針對特殊需求提供相關功能。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用課程、範例和互動式教學課程](./functions-get-started.md)
