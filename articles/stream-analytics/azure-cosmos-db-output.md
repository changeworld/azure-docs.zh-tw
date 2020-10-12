---
title: Azure 串流分析的 Azure Cosmos DB 輸出
description: 本文說明如何將資料從 Azure 串流分析輸出至 Azure Cosmos DB。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e322135cfdb7aaff331367e84c603e8344436528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906260"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure 串流分析的 Azure Cosmos DB 輸出

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 是全域散發的資料庫服務，在全球各地提供無限的彈性調整、透過無從驗證結構描述資料模型的豐富查詢和自動索引。 若要了解串流分析的 Azure Cosmos DB 容器選項，請參閱[以 Azure Cosmos DB 作為輸出的串流分析](stream-analytics-documentdb-output.md)一文。

Azure China 21Vianet 和 Azure 德國 (T-Systems International) 區域目前無法從串流分析產生 Azure Cosmos DB 輸出。

> [!Note]
> Azure 串流分析僅支援使用 SQL API 連接到 Azure Cosmos DB。
> 尚不支援其他 Azure Cosmos DB API。 如果您將 Azure Stream Analytics 指向使用其他 API 建立的 Azure Cosmos DB 帳戶，可能無法正確儲存資料。

下表描述用來建立 Azure Cosmos DB 輸出的屬性。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 在您的串流分析查詢中參照此輸出時所用的別名。 |
| 接收 | Azure Cosmos DB。 |
| 匯入選項 | 選擇 [從您的訂用帳戶選取 Cosmos DB]，或 [手動提供 Cosmos DB 設定]。
| 帳戶識別碼 | Azure Cosmos DB 帳戶的名稱或端點 URI。 |
| 帳戶金鑰 | Azure Cosmos DB 帳戶的共用存取金鑰。 |
| 資料庫 | Azure Cosmos DB 資料庫名稱。 |
| 容器名稱 | 要使用的容器名稱，必須存在於 Cosmos DB 中。 範例：  <br /><ul><li> _MyContainer_：名為 "MyContainer" 的容器必須存在。</li>|
| 文件識別碼 |選擇性。 輸出事件中的欄位名稱會用來指定主索引鍵，此為插入或更新作業的依據。

## <a name="partitioning"></a>資料分割

分割區索引鍵是以查詢中的 PARTITION BY 子句為基礎。 輸出寫入器的數目會遵循完全平行化 [查詢](stream-analytics-scale-jobs.md)的輸入資料分割。 串流分析會將 Cosmos DB 輸出分割區索引鍵轉換成字串。 例如，如果您的資料分割索引鍵的值為1，且類型為 Bigint，則會轉換成字串類型的 "1"。

## <a name="output-batch-size"></a>輸出批次大小

如需訊息大小上限，請參閱 [Azure Cosmos DB 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 批次大小和寫入頻率會依 Azure Cosmos DB 回應進行動態調整。 串流分析不會做任何預先決定的限制。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
