---
title: Azure Cosmos DB 的自動化效能、成本、安全性建議
description: 瞭解如何根據您的工作負載模式，針對 Azure Cosmos DB 來查看自訂的效能、成本、安全性和其他建議。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450082"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB 的自動化建議

所有的雲端服務，包括 Azure Cosmos DB 取得新功能、功能和改進的經常性更新。 您的應用程式必須跟上最新的效能和安全性更新。 Azure 入口網站提供自訂的建議，可讓您將應用程式的效能最大化。 Azure Cosmos DB 的諮詢引擎會持續分析 Azure Cosmos DB 資源的使用量歷程記錄，並根據您的工作負載模式提供建議。 這些建議會對應到資料分割、索引、網路、安全性等區域。這些自訂的建議可協助您改善應用程式的效能。

## <a name="view-recommendations"></a>檢視建議

您可以透過下列方式來查看 Azure Cosmos DB 的建議：

- 其中一種方式是在 [通知] 索引標籤中查看建議。如果有新的建議，您會看到一個訊息列。 登入您的[Azure 入口網站](https://portal.azure.com)，然後流覽至您的 Azure Cosmos 帳戶。 在您的 Azure Cosmos 帳戶中，開啟 [**通知**] 窗格，然後選取 [**建議**] 索引標籤。您可以選取訊息並查看建議。  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="從 Azure Cosmos DB 窗格中查看建議":::

- 您也可以透過不同的值區（例如成本、安全性、可靠性、效能和營運卓越）分類的[Azure Advisor](../advisor/advisor-overview.md)來尋找建議。 您可以選取特定的訂用帳戶，並依資源類型進行篩選，也就是**Azure Cosmos DB 帳戶**。  當您選取特定的建議時，它會顯示您可以採取的動作來讓您的工作負載受益。

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="從 Azure Advisor 窗格中查看建議":::

並非 [Azure Cosmos DB] 窗格中顯示的所有建議都可在 Azure Advisor 中使用，反之亦然。 這是因為根據建議的類型，它們會放在 [Azure Advisor] 窗格中，Azure Cosmos DB 窗格] 或 [兩者]。

目前 Azure Cosmos DB 支援下欄區域的建議。 其中每個建議都包含檔相關章節的連結，因此您可以輕鬆地採取後續步驟。

## <a name="sdk-usage-recommendations"></a>SDK 使用方式建議

在此類別中，advisor 會偵測舊版 Sdk 的使用方式，並建議您升級至較新的版本，以利用最新的錯誤修正和效能改進。 目前提供下列 SDK 特有的建議：

|名稱  |說明  |
|---------|---------|
| 舊的 Spark 連接器 | 偵測舊版 Spark 連接器的使用方式，並建議升級。 |
| 舊版 .NET SDK | 偵測舊版 .NET SDK 的使用方式，並建議升級。 |
| 舊版 JAVA SDK | 偵測舊版 JAVA 連接器的使用方式，並建議升級。 |

## <a name="indexing-recommendations"></a>索引建議

在此類別中，advisor 會偵測索引編制模式、編制索引原則、索引路徑，並在目前的設定會影響查詢效能時建議變更。 目前提供下列索引特有的建議：

|名稱  |說明  |
|---------|---------|
| 延遲索引 | 偵測延遲索引模式的使用，並建議改用一致的索引編制模式。 Azure Cosmos DB 的延遲編制索引模式的目的有限，而且在某些情況下可能會影響查詢結果的有效性，因此建議使用一致的索引編制模式。 |
| 複合索引| 偵測查詢可以受益于複合索引，並建議使用這些帳戶的帳戶。 複合索引可以大幅改善某些查詢的效能和輸送量耗用量。|
| 具有許多索引路徑的預設索引編制原則 | 偵測以許多索引路徑在預設索引上執行的容器，並建議自訂索引編制原則。|
| 具有高 RU/秒費用的排序依據查詢| 偵測由具有高 RU/s 費用的查詢發出訂單的容器，並建議探索複合索引。|
| 沒有索引和高 RU/秒耗用量的 MongoDB 3.6 帳戶| 使用3.6 版的容器來偵測 Azure Cosmos DB 的適用于 MongoDB 的 API，其會發出具有高 RU/秒費用的查詢，並建議新增索引。|

## <a name="cost-optimization-recommendations"></a>成本優化建議

在此類別中，advisor 會偵測 RU/秒使用量，並判斷您可以藉由對資源進行一些變更，或利用不同的計價模式來將價格優化。 目前提供下列成本優化特定的建議：

|名稱  |說明  |
|---------|---------|
| 保留容量 | 偵測您的 RU/秒使用量，並向可以受益的使用者建議保留實例。 |
| 非使用中容器 | 偵測超過30天未使用的容器，並建議您減少這類容器的輸送量，或將其刪除。|
| 高輸送量的新訂用帳戶 | 偵測每日有異常高 RU/秒的新訂用帳戶，並提供通知給他們。 此通知專門提供認知給新客戶，Azure Cosmos DB 在布建的以輸送量為基礎的模型，而不是以耗用量為基礎的模型上操作。 |

## <a name="migration-recommendations"></a>遷移建議

在此類別中，advisor 會偵測到您使用舊版功能建議進行遷移，讓您可以利用 Azure Cosmos DB 的大量擴充性和其他優勢。 目前提供下列的特定遷移建議：

|名稱  |說明  |
|---------|---------|
| 非資料分割容器 | 偵測已接近其最大儲存體限制的固定大小容器，並建議將它們遷移至已分割的容器。|

## <a name="query-usage-recommendations"></a>查詢使用方式建議

在此類別中，advisor 會偵測查詢執行，並識別查詢效能可以利用某些變更進行調整。 目前提供下列查詢使用建議：

|名稱  |說明  |
|---------|---------|
| 具有固定頁面大小的查詢 | 偵測以固定頁面大小發出的查詢，並建議使用-1 （頁面大小沒有限制），而不是定義特定的值。 此選項可減少取得所有結果所需的網路往返次數。 |

## <a name="next-steps"></a>後續步驟

* [調整 Azure Cosmos DB 中的查詢效能](sql-api-query-metrics.md)
* 使用 Azure Cosmos DB 時針對[查詢問題進行疑難排解](troubleshoot-query-performance.md)
