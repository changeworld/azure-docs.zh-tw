---
title: Azure 串流分析的 Azure Functions 輸出
description: 本文說明 azure 功能作為 Azure 串流分析的輸出。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019613"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure 串流分析的 Azure Functions 輸出

Azure Functions 是無伺服器計算服務，您可以用來依需求執行程式碼，無需明確佈建或管理基礎結構。 可讓您實作在 Azure 或合作夥伴服務中發生事件所觸發的程式碼。 Azure Functions 回應觸發程序的這個功能使其自然輸出 Azure 串流分析。 此輸出配接器可讓使用者將串流分析連接至 Azure Functions，然後執行指令碼或程式碼片段，以回應各種不同的事件。

Azure Functions 來自串流分析的輸出，在 Azure 中國世紀和 Azure 德國 (的 T 系統國際) 區域中無法使用。 也不支援從在多租使用者叢集中執行的串流分析作業連線到虛擬網路內的 Azure Functions (VNet) 。

Azure 串流分析會透過 HTTP 觸發程序叫用 Azure Functions。 Azure Functions 輸出配接器可搭配下列可設定屬性使用：

| 屬性名稱 | 描述 |
| --- | --- |
| 函式應用程式 |Azure Functions 應用程式的名稱。 |
| 函式 |Azure Functions 應用程式中函式的名稱。 |
| Key |如果您想要使用另一個訂用帳戶中的 Azure Function，可以藉由提供存取函式的金鑰來達到這個目的。 |
| 批次大小上限 |屬性可讓您針對傳送到您 Azure Function 的每個輸出批次，設定大小上限。 輸入是以位元組為單位。 根據預設，此值是 262,144 個位元組 (256 KB)。 |
| 批次計數上限  |屬性可讓您在傳送至 Azure Functions 的每個批次中，指定事件數目上限。 預設值是 100。 |

Azure 串流分析針對已成功處理的批次，預期來自 Functions 應用程式是 HTTP 狀態 200。

當 Azure 串流分析從 Azure 函式收到 413 (http 要求實體太大) 例外狀況時，會縮減傳送至 Azure Functions 的批次大小。 在 Azure 函式程式碼中，使用這個例外狀況可確保 Azure 串流分析不會傳送過大的批次。 同時，請確認函式中使用的最大批次計數和大小值都符合串流分析入口網站中輸入的值。

> [!NOTE]
> 在測試連線期間，串流分析會將空的批次傳送至 Azure Functions，以測試兩者之間的連線是否運作。 請確定您的 Functions 應用程式會處理空的批次要求，以確保測試連線通過。

此外，如果在某個時間範圍內沒有登陸任何事件，則不會產生任何輸出。 如此一來，就不會呼叫 **computeResult** 函式。 此行為與內建的視窗型彙總函式一致。

## <a name="partitioning"></a>資料分割

分割區索引鍵是以查詢中的 PARTITION BY 子句為基礎。 輸出寫入器的數目會遵循完全平行化 [查詢](stream-analytics-scale-jobs.md)的輸入資料分割。

## <a name="output-batch-size"></a>輸出批次大小

預設批次大小為 262,144 個位元組 (256 KB)。 每個批次的預設事件計數為 100。 批次大小可以設定，並可以在串流分析的輸出選項中增加或減少。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)